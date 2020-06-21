---
title: Filtri in ASP.NET Core
author: Rick-Anderson
description: Informazioni sul funzionamento dei filtri e su come usarli in ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/filters
ms.openlocfilehash: 068b471c1f5fa5f0ca87dd7b028badf70f8c1b67
ms.sourcegitcommit: 77729ba225d5143c0e3954db005906f4a5c7da95
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2020
ms.locfileid: "85122165"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="e6fd7-103">Filtri in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e6fd7-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e6fd7-104">Di [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="e6fd7-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="e6fd7-105">I *filtri* in ASP.NET Core consentono l'esecuzione del codice prima o dopo fasi specifiche della pipeline di elaborazione della richiesta.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="e6fd7-106">I filtri predefiniti gestiscono attività, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="e6fd7-107">Autorizzazione (impedire l'accesso alle risorse per cui un utente non è autorizzato).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="e6fd7-108">Memorizzazione nella cache delle risposte (blocco della pipeline delle richieste per restituire una risposta memorizzata nella cache).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="e6fd7-109">I filtri personalizzati possono essere creati per gestire problemi relativi a più settori.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="e6fd7-110">Esempi di problematiche trasversali includono la gestione degli errori, la memorizzazione nella cache, la configurazione, l'autorizzazione e la registrazione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="e6fd7-111">I filtri evitano la duplicazione del codice.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="e6fd7-112">Ad esempio, un filtro eccezioni per la gestione degli errori potrebbe consolidare la gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="e6fd7-113">Questo documento si applica a Razor pagine, controller API e controller con visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="e6fd7-114">I filtri non funzionano direttamente con i [ Razor componenti](xref:blazor/components/index).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="e6fd7-115">Un filtro può influire indirettamente su un componente solo quando:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="e6fd7-116">Il componente è incorporato in una pagina o in una vista.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="e6fd7-117">La pagina o il controller/Visualizzazione usa il filtro.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="e6fd7-118">[Visualizzare o scaricare un esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="e6fd7-119">Funzionamento dei filtri</span><span class="sxs-lookup"><span data-stu-id="e6fd7-119">How filters work</span></span>

<span data-ttu-id="e6fd7-120">I filtri vengono eseguiti all'interno della *pipeline di chiamata di azioni ASP.NET Core*, talvolta definita *pipeline di filtro*.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="e6fd7-121">La pipeline di filtro viene eseguita dopo che ASP.NET Core ha selezionato l'azione da eseguire.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![La richiesta viene elaborata tramite altri middleware, middleware di routing, selezione dell'azione e pipeline di chiamata dell'azione.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="e6fd7-124">Tipi di filtro</span><span class="sxs-lookup"><span data-stu-id="e6fd7-124">Filter types</span></span>

<span data-ttu-id="e6fd7-125">Ogni tipo di filtro viene eseguito in una fase diversa della pipeline di filtro:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="e6fd7-126">I [filtri di autorizzazione](#authorization-filters) vengono eseguiti per primi e consentono di determinare se l'utente è autorizzato per la richiesta.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="e6fd7-127">I filtri di autorizzazione consentono di cortocircuitare la pipeline se la richiesta non è autorizzata.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="e6fd7-128">[Filtri risorse](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="e6fd7-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="e6fd7-129">Vengono eseguiti dopo l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-129">Run after authorization.</span></span>  
  * <span data-ttu-id="e6fd7-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>esegue il codice prima del resto della pipeline filtro.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="e6fd7-131">Ad esempio, `OnResourceExecuting` esegue il codice prima dell'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="e6fd7-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>esegue il codice una volta completato il resto della pipeline.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="e6fd7-133">[Filtri azione](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="e6fd7-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="e6fd7-134">Eseguire il codice immediatamente prima e dopo la chiamata di un metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="e6fd7-135">Consente di modificare gli argomenti passati in un'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="e6fd7-136">Può modificare il risultato restituito dall'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="e6fd7-137">**Non** sono supportate nelle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="e6fd7-138">I [filtri eccezioni](#exception-filters) applicano i criteri globali alle eccezioni non gestite che si verificano prima della scrittura del corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="e6fd7-139">I [filtri risultati](#result-filters) eseguono il codice immediatamente prima e dopo l'esecuzione dei risultati dell'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="e6fd7-140">Vengono eseguiti solo quando il metodo di azione è stato eseguito correttamente.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="e6fd7-141">Sono utili per la logica che deve racchiudere l'esecuzione del formattatore o di una vista.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="e6fd7-142">Il diagramma seguente illustra come interagiscono i tipi di filtro nella pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![La richiesta passa attraverso i filtri autorizzazione, i filtri risorse, l'associazione di modelli, i filtri azione, l'esecuzione dell'azione e la conversione del risultato dell'azione, i filtri eccezioni, i filtri risultato e l'esecuzione del risultato.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="e6fd7-145">Implementazione</span><span class="sxs-lookup"><span data-stu-id="e6fd7-145">Implementation</span></span>

<span data-ttu-id="e6fd7-146">I filtri supportano sia implementazioni sincrone che asincrone tramite definizioni di interfaccia diverse.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="e6fd7-147">I filtri sincroni eseguono codice prima e dopo la fase della pipeline.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="e6fd7-148">La chiamata di <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*>, ad esempio, avviene prima della chiamata del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="e6fd7-149">La chiamata di <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> avviene dopo l'esecuzione del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="e6fd7-150">I filtri asincroni definiscono un `On-Stage-ExecutionAsync` metodo.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="e6fd7-151">Ad esempio, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="e6fd7-152">Nel codice precedente, `SampleAsyncActionFilter` ha un <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ) che esegue il metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="e6fd7-153">Fasi di filtro multiple</span><span class="sxs-lookup"><span data-stu-id="e6fd7-153">Multiple filter stages</span></span>

<span data-ttu-id="e6fd7-154">È possibile implementare interfacce per più fasi di filtro in una singola classe.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="e6fd7-155">Ad esempio, la <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> classe implementa:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="e6fd7-156">Sincrona: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> e<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="e6fd7-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="e6fd7-157">Asincrono: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> e<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="e6fd7-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="e6fd7-158">Implementare la versione sincrona **oppure** la versione asincrona di un'interfaccia di filtro, **non** entrambe.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="e6fd7-159">Il runtime controlla per prima cosa se il filtro implementa l'interfaccia asincrona e, in tal caso, la chiama.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="e6fd7-160">In caso contrario, chiama i metodi dell'interfaccia sincrona.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="e6fd7-161">Se in una classe sono implementate entrambe le interfacce, sincrona e asincrona, viene chiamato solo il metodo asincrono.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="e6fd7-162">Quando si usano classi astratte come <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , eseguire l'override solo dei metodi sincroni o del metodo asincrono per ogni tipo di filtro.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="e6fd7-163">Attributi filtro predefiniti</span><span class="sxs-lookup"><span data-stu-id="e6fd7-163">Built-in filter attributes</span></span>

<span data-ttu-id="e6fd7-164">ASP.NET Core include filtri predefiniti basati su attributi che è possibile impostare come sottoclasse e personalizzare.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="e6fd7-165">Il filtro dei risultati seguente, ad esempio, aggiunge un'intestazione alla risposta:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="e6fd7-166">Gli attributi consentono ai filtri di accettare argomenti, come illustrato nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="e6fd7-167">Applicare `AddHeaderAttribute` a un controller o a un metodo di azione e specificare il nome e il valore dell'intestazione HTTP:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="e6fd7-168">Usare uno strumento come gli [strumenti di sviluppo del browser](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) per esaminare le intestazioni.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="e6fd7-169">In **intestazioni risposta** `author: Rick Anderson` viene visualizzato.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="e6fd7-170">Il codice seguente implementa un oggetto `ActionFilterAttribute` che:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="e6fd7-171">Legge il titolo e il nome dal sistema di configurazione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="e6fd7-172">A differenza dell'esempio precedente, il codice seguente non richiede l'aggiunta di parametri di filtro al codice.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="e6fd7-173">Aggiunge il titolo e il nome all'intestazione della risposta.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="e6fd7-174">Le opzioni di configurazione vengono fornite dal [sistema di configurazione](xref:fundamentals/configuration/index) usando il [modello di opzioni](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="e6fd7-175">Ad esempio, dal *appsettings.jssu* file:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-175">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="e6fd7-176">Nel `StartUp.ConfigureServices` eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="e6fd7-177">La `PositionOptions` classe viene aggiunta al contenitore del servizio con l' `"Position"` area di configurazione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="e6fd7-178">`MyActionFilterAttribute`Viene aggiunto al contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="e6fd7-179">Il codice seguente illustra la `PositionOptions` classe:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="e6fd7-180">Il codice seguente applica al `MyActionFilterAttribute` `Index2` Metodo:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="e6fd7-181">In **intestazioni di risposta**, `author: Rick Anderson` e `Editor: Joe Smith` viene visualizzato quando `Sample/Index2` viene chiamato l'endpoint.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="e6fd7-182">Il codice seguente applica `MyActionFilterAttribute` e alla `AddHeaderAttribute` Razor pagina:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="e6fd7-183">Non è possibile applicare i filtri ai Razor metodi del gestore di pagina.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="e6fd7-184">Possono essere applicati al Razor modello di pagina o a livello globale.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="e6fd7-185">Molte delle interfacce del filtro presentano attributi corrispondenti che possono essere usati come classi di base per implementazioni personalizzate.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="e6fd7-186">Attributi dei filtri:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="e6fd7-187">Ambiti dei filtri e ordine di esecuzione</span><span class="sxs-lookup"><span data-stu-id="e6fd7-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="e6fd7-188">È possibile aggiungere un filtro alla pipeline in uno dei tre *ambiti*:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="e6fd7-189">Uso di un attributo in un'azione del controller.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="e6fd7-190">Gli attributi di filtro non possono essere applicati ai Razor metodi del gestore di pagine.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="e6fd7-191">Uso di un attributo in un controller o in una Razor pagina.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="e6fd7-192">A livello globale per tutti i controller, le azioni e le Razor pagine, come illustrato nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="e6fd7-193">Ordine di esecuzione predefinito</span><span class="sxs-lookup"><span data-stu-id="e6fd7-193">Default order of execution</span></span>

<span data-ttu-id="e6fd7-194">Quando sono presenti più filtri per una particolare fase della pipeline, l'ambito determina l'ordine di esecuzione predefinito dei filtri.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="e6fd7-195">I filtri globali racchiudono i filtri di classe, che a loro volta racchiudono i filtri dei metodi.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="e6fd7-196">Come risultato dell'annidamento dei filtri, il codice *after* dei filtri viene eseguito in ordine inverso rispetto al codice *before*.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="e6fd7-197">Sequenza di filtro:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-197">The filter sequence:</span></span>

* <span data-ttu-id="e6fd7-198">Codice *before* dei filtri globali.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="e6fd7-199">Codice *prima* dei filtri di pagina e controller Razor .</span><span class="sxs-lookup"><span data-stu-id="e6fd7-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="e6fd7-200">Codice *before* dei filtri del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="e6fd7-201">Codice *after* dei filtri del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="e6fd7-202">Codice *dopo* i filtri del controller e della Razor pagina.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="e6fd7-203">Codice *after* dei filtri globali.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="e6fd7-204">L'esempio seguente illustra l'ordine in cui i metodi dei filtri vengono chiamati per i filtri di azione sincroni.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="e6fd7-205">Sequenza</span><span class="sxs-lookup"><span data-stu-id="e6fd7-205">Sequence</span></span> | <span data-ttu-id="e6fd7-206">Ambito del filtro</span><span class="sxs-lookup"><span data-stu-id="e6fd7-206">Filter scope</span></span> | <span data-ttu-id="e6fd7-207">Metodo del filtro</span><span class="sxs-lookup"><span data-stu-id="e6fd7-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="e6fd7-208">1</span><span class="sxs-lookup"><span data-stu-id="e6fd7-208">1</span></span> | <span data-ttu-id="e6fd7-209">Globale</span><span class="sxs-lookup"><span data-stu-id="e6fd7-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="e6fd7-210">2</span><span class="sxs-lookup"><span data-stu-id="e6fd7-210">2</span></span> | <span data-ttu-id="e6fd7-211">Controller o Razor pagina</span><span class="sxs-lookup"><span data-stu-id="e6fd7-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="e6fd7-212">3</span><span class="sxs-lookup"><span data-stu-id="e6fd7-212">3</span></span> | <span data-ttu-id="e6fd7-213">Metodo</span><span class="sxs-lookup"><span data-stu-id="e6fd7-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="e6fd7-214">4</span><span class="sxs-lookup"><span data-stu-id="e6fd7-214">4</span></span> | <span data-ttu-id="e6fd7-215">Metodo</span><span class="sxs-lookup"><span data-stu-id="e6fd7-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="e6fd7-216">5</span><span class="sxs-lookup"><span data-stu-id="e6fd7-216">5</span></span> | <span data-ttu-id="e6fd7-217">Controller o Razor pagina</span><span class="sxs-lookup"><span data-stu-id="e6fd7-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="e6fd7-218">6</span><span class="sxs-lookup"><span data-stu-id="e6fd7-218">6</span></span> | <span data-ttu-id="e6fd7-219">Globale</span><span class="sxs-lookup"><span data-stu-id="e6fd7-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="e6fd7-220">Filtri a livello di controller</span><span class="sxs-lookup"><span data-stu-id="e6fd7-220">Controller level filters</span></span>

<span data-ttu-id="e6fd7-221">Ogni controller che eredita dalla classe di base <xref:Microsoft.AspNetCore.Mvc.Controller> include i metodi [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) e [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="e6fd7-222">Questi metodi:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-222">These methods:</span></span>

* <span data-ttu-id="e6fd7-223">Eseguono il wrapping dei filtri che vengono eseguiti per una determinata azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="e6fd7-224">La chiamata di `OnActionExecuting` avviene prima di qualsiasi filtro dell'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="e6fd7-225">La chiamata di `OnActionExecuted` avviene dopo tutti i filtri dell'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="e6fd7-226">La chiamata di `OnActionExecutionAsync` avviene prima di qualsiasi filtro dell'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="e6fd7-227">Il codice del filtro dopo `next` viene eseguito dopo il metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="e6fd7-228">Ad esempio, l'applicazione di `MySampleActionFilter` nell'esempio scaricato avviene a livello globale all'avvio.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="e6fd7-229">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-229">The `TestController`:</span></span>

* <span data-ttu-id="e6fd7-230">Applica `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) all' `FilterTest2` azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="e6fd7-231">Esegue l'override di `OnActionExecuting` e `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="e6fd7-232">Passando a `https://localhost:5001/Test2/FilterTest2`, viene eseguito il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="e6fd7-233">Filtri a livello di controller impostare la proprietà [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) su `int.MinValue` .</span><span class="sxs-lookup"><span data-stu-id="e6fd7-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="e6fd7-234">I filtri a livello di controller **non** possono essere impostati per l'esecuzione dopo i filtri applicati ai metodi.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="e6fd7-235">L'ordine è illustrato nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-235">Order is explained in the next section.</span></span>

<span data-ttu-id="e6fd7-236">Per le Razor pagine, vedere [implementare Razor filtri di pagina eseguendo l'override dei metodi di filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="e6fd7-237">Override dell'ordine predefinito</span><span class="sxs-lookup"><span data-stu-id="e6fd7-237">Overriding the default order</span></span>

<span data-ttu-id="e6fd7-238">È possibile eseguire l'override della sequenza di esecuzione predefinita implementando <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="e6fd7-239">`IOrderedFilter` espone la proprietà <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order>, che ha la precedenza sull'ambito per determinare l'ordine di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="e6fd7-240">Un filtro con un valore di `Order` inferiore:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="e6fd7-241">Esegue il codice *before* prima di quello di un filtro con un valore di `Order` più alto.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="e6fd7-242">Esegue il codice *after* dopo quello di un filtro con un valore di `Order` più alto.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="e6fd7-243">La `Order` proprietà viene impostata con un parametro del costruttore:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="e6fd7-244">Considerare i due filtri azione nel controller seguente:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="e6fd7-245">In è stato aggiunto un filtro globale `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e6fd7-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="e6fd7-246">I 3 filtri vengono eseguiti nell'ordine seguente:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="e6fd7-247">La proprietà `Order` ha la precedenza sull'ambito nel determinare l'ordine di esecuzione dei filtri.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="e6fd7-248">I filtri vengono ordinati prima in base all'ordine, poi viene usato l'ambito per interrompere i collegamenti.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="e6fd7-249">Tutti i filtri predefiniti implementano `IOrderedFilter` e impostano il valore predefinito di `Order` su 0.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="e6fd7-250">Come indicato in precedenza, i filtri a livello di controller impostano la proprietà [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) su `int.MinValue` per i filtri incorporati, l'ambito determina l'ordine a meno che non `Order` sia impostato su un valore diverso da zero.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="e6fd7-251">Nel codice precedente, `MySampleActionFilter` ha ambito globale, quindi viene eseguito prima di `MyAction2FilterAttribute` , che ha ambito del controller.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="e6fd7-252">Per `MyAction2FilterAttribute` eseguire prima l'esecuzione, impostare l'ordine su `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="e6fd7-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="e6fd7-253">Per eseguire prima l'esecuzione del filtro globale `MySampleActionFilter` , impostare `Order` su `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="e6fd7-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="e6fd7-254">Annullamento e corto circuito</span><span class="sxs-lookup"><span data-stu-id="e6fd7-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="e6fd7-255">È possibile causare il corto circuito della pipeline di filtro impostando la proprietà <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> sul parametro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fornito al metodo di filtro.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="e6fd7-256">Ad esempio, il filtro di risorse seguente impedisce l'esecuzione del resto della pipeline:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="e6fd7-257">Nel codice seguente sia il filtro `ShortCircuitingResourceFilter` che il filtro `AddHeader` hanno come destinazione il metodo di azione `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="e6fd7-258">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="e6fd7-259">Viene eseguito per primo perché è un filtro risorsa e `AddHeader` è un filtro azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="e6fd7-260">Blocca il resto della pipeline.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="e6fd7-261">Pertanto il filtro `AddHeader` non viene mai eseguito per l'azione `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="e6fd7-262">Questo comportamento sarebbe lo stesso se entrambi i filtri venissero applicati a livello di metodo di azione, a condizione che il filtro `ShortCircuitingResourceFilter` venga eseguito prima.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="e6fd7-263">`ShortCircuitingResourceFilter` viene eseguito per primo per il tipo di filtro o per l'uso esplicito della proprietà `Order`.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="e6fd7-264">Inserimento delle dipendenze</span><span class="sxs-lookup"><span data-stu-id="e6fd7-264">Dependency injection</span></span>

<span data-ttu-id="e6fd7-265">È possibile aggiungere filtri per tipo o per istanza.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="e6fd7-266">Se viene aggiunta un'istanza, tale istanza viene usata per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="e6fd7-267">Se viene aggiunto un tipo, il filtro è attivato dal tipo.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="e6fd7-268">Un filtro attivato dal tipo comporta:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-268">A type-activated filter means:</span></span>

* <span data-ttu-id="e6fd7-269">La creazione di un'istanza per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-269">An instance is created for each request.</span></span>
* <span data-ttu-id="e6fd7-270">Il popolamento di tutte le dipendenze del costruttore tramite [inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="e6fd7-271">I filtri implementati come attributi e aggiunti direttamente alle classi controller o ai metodi di azione non possono avere dipendenze costruttore specificate dall'[inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="e6fd7-272">Le dipendenze del costruttore non possono essere fornite tramite inserimento delle dipendenze in quanto:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="e6fd7-273">I parametri del costruttore devono essere forniti agli attributi nella posizione in cui vengono applicati.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="e6fd7-274">Questa è una limitazione del funzionamento degli attributi.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="e6fd7-275">I filtri seguenti supportano le dipendenze del costruttore fornite dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="e6fd7-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementato nell'attributo.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="e6fd7-277">I filtri precedenti possono essere applicati a un controller o a un metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="e6fd7-278">I logger sono disponibili tramite l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-278">Loggers are available from DI.</span></span> <span data-ttu-id="e6fd7-279">Evitare tuttavia di creare e usare filtri esclusivamente per scopi di registrazione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="e6fd7-280">La funzionalità di [registrazione del framework predefinita](xref:fundamentals/logging/index) in genere fornisce il necessario per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="e6fd7-281">La registrazione aggiunta ai filtri:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-281">Logging added to filters:</span></span>

* <span data-ttu-id="e6fd7-282">Deve concentrarsi su problemi del dominio di business o comportamenti specifici del filtro.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="e6fd7-283">**Non** deve registrare azioni o altri eventi del framework.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="e6fd7-284">Le azioni di log dei filtri incorporati e gli eventi del Framework.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="e6fd7-285">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="e6fd7-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="e6fd7-286">I tipi di implementazione del filtro di servizi sono registrati in `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="e6fd7-287"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera un'istanza del filtro dall'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="e6fd7-288">Il codice seguente illustra `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="e6fd7-289">Nel codice seguente l'oggetto `AddHeaderResultServiceFilter` viene aggiunto al contenitore di inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="e6fd7-290">Nel codice seguente l'attributo `ServiceFilter` recupera un'istanza del filtro `AddHeaderResultServiceFilter` dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="e6fd7-291">Quando si usa l'impostazione `ServiceFilterAttribute`, [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="e6fd7-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="e6fd7-292">Indica che l'istanza del filtro *può* essere riutilizzata all'esterno dell'ambito della richiesta in cui è stata creata.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="e6fd7-293">Il runtime di ASP.NET Core non garantisce:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="e6fd7-294">Che venga creata una singola istanza del filtro.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="e6fd7-295">Che il filtro non verrà richiesto di nuovo dal contenitore di inserimento delle dipendenze in un momento successivo.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="e6fd7-296">Non si deve usare con un filtro che dipende da servizi con una durata diversa da singleton.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="e6fd7-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="e6fd7-298">`IFilterFactory` espone il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> per creare un'istanza <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="e6fd7-299">`CreateInstance` carica il tipo specificato dall'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="e6fd7-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="e6fd7-300">TypeFilterAttribute</span></span>

<span data-ttu-id="e6fd7-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> è simile a <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, ma il relativo tipo non viene risolto direttamente dal contenitore dell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="e6fd7-302">Viene creata un'istanza del tipo tramite <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="e6fd7-303">Poiché i tipi `TypeFilterAttribute` non vengono risolti direttamente dal contenitore di inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="e6fd7-304">Non è necessario che i tipi a cui viene fatto riferimento tramite `TypeFilterAttribute` siano registrati nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="e6fd7-305">Le loro dipendenze vengono soddisfatte dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="e6fd7-306">In via facoltativa, `TypeFilterAttribute` può anche accettare gli argomenti del costruttore per il tipo.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="e6fd7-307">Quando si usa l'impostazione `TypeFilterAttribute`, [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="e6fd7-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="e6fd7-308">Indica che l'istanza del filtro *può* essere riutilizzata all'esterno dell'ambito della richiesta in cui è stata creata.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="e6fd7-309">Il runtime di ASP.NET Core non fornisce alcuna garanzia che venga creata una singola istanza del filtro.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="e6fd7-310">Non si deve usare con un filtro che dipende da servizi con una durata diversa da singleton.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="e6fd7-311">L'esempio seguente illustra come passare argomenti a un tipo usando `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="e6fd7-312">Filtri autorizzazione</span><span class="sxs-lookup"><span data-stu-id="e6fd7-312">Authorization filters</span></span>

<span data-ttu-id="e6fd7-313">I filtri di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-313">Authorization filters:</span></span>

* <span data-ttu-id="e6fd7-314">Sono i primi filtri a essere eseguiti nella pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="e6fd7-315">Controllano l'accesso ai metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-315">Control access to action methods.</span></span>
* <span data-ttu-id="e6fd7-316">Dispongono di un metodo precedente, ma non di un metodo successivo.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="e6fd7-317">I filtri di autorizzazione personalizzati richiedono un framework di autorizzazione personalizzato.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="e6fd7-318">È preferibile configurare criteri di autorizzazione o scrivere criteri di autorizzazione personalizzati piuttosto che scrivere un filtro personalizzato.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="e6fd7-319">Il filtro di autorizzazione predefinito:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="e6fd7-320">Chiama il sistema di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-320">Calls the authorization system.</span></span>
* <span data-ttu-id="e6fd7-321">Non autorizza le richieste.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-321">Does not authorize requests.</span></span>

<span data-ttu-id="e6fd7-322">**Non** generare eccezioni nei filtri di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="e6fd7-323">L'eccezione non verrà gestita.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-323">The exception will not be handled.</span></span>
* <span data-ttu-id="e6fd7-324">I filtri di eccezione non gestiranno l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="e6fd7-325">È consigliabile emettere una richiesta di verifica in caso di eccezione in un filtro di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="e6fd7-326">Altre informazioni sull'[autorizzazione](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="e6fd7-327">Filtri risorse</span><span class="sxs-lookup"><span data-stu-id="e6fd7-327">Resource filters</span></span>

<span data-ttu-id="e6fd7-328">I filtri di risorse:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-328">Resource filters:</span></span>

* <span data-ttu-id="e6fd7-329">Implementano l'interfaccia <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="e6fd7-330">L'esecuzione racchiude la maggior parte della pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="e6fd7-331">Solo i [filtri di autorizzazione](#authorization-filters) vengono eseguiti prima dei filtri di risorse.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="e6fd7-332">I filtri di risorse sono utili per causare il corto circuito della maggior parte della pipeline.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="e6fd7-333">Un filtro di memorizzazione nella cache può, ad esempio, evitare il resto della pipeline in caso di riscontro nella cache.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="e6fd7-334">Esempi di filtri di risorse:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-334">Resource filter examples:</span></span>

* <span data-ttu-id="e6fd7-335">Il [filtro di risorse di corto circuito](#short-circuiting-resource-filter) illustrato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="e6fd7-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="e6fd7-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="e6fd7-337">Impedisce all'associazione di modelli di accedere ai dati del modulo.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="e6fd7-338">Viene usato quando si caricano file di grandi dimensioni per impedire la lettura dei dati del modulo in memoria.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="e6fd7-339">Filtri azione</span><span class="sxs-lookup"><span data-stu-id="e6fd7-339">Action filters</span></span>

<span data-ttu-id="e6fd7-340">I filtri azione **non** si applicano alle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-340">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="e6fd7-341">Pages supporta <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="e6fd7-341"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="e6fd7-342">Per ulteriori informazioni, vedere [metodi di filtro per le Razor pagine](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="e6fd7-343">I filtri di azione:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-343">Action filters:</span></span>

* <span data-ttu-id="e6fd7-344">Implementano l'interfaccia <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="e6fd7-345">La loro esecuzione circonda l'esecuzione dei metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="e6fd7-346">Il codice seguente mostra un filtro di azione di esempio:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="e6fd7-347">La classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> specifica le proprietà seguenti:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="e6fd7-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>: consente di leggere gli input in un metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="e6fd7-349"><xref:Microsoft.AspNetCore.Mvc.Controller>: consente di modificare l'istanza del controller.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="e6fd7-350"><xref:System.Web.Mvc.ActionExecutingContext.Result>: l'impostazione di `Result` causa il corto circuito del metodo di azione e dei filtri di azione successivi.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="e6fd7-351">La generazione di un'eccezione in un metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="e6fd7-352">Impedisce l'esecuzione dei filtri successivi.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="e6fd7-353">A differenza dell'impostazione di `Result`, è considerata un errore anziché un risultato positivo.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="e6fd7-354">La classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> specifica `Controller` e `Result` oltre alle proprietà seguenti:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="e6fd7-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>: true se un altro file ha causato il corto circuito dell'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="e6fd7-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception>: non Null se l'azione o un filtro di azione eseguito in precedenza ha generato un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="e6fd7-357">Se si imposta questa proprietà su Null:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-357">Setting this property to null:</span></span>

  * <span data-ttu-id="e6fd7-358">L'eccezione viene gestita in modo efficace.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="e6fd7-359">L'oggetto `Result` viene eseguito come se fosse stato restituito dal metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="e6fd7-360">Per un oggetto `IAsyncActionFilter`, una chiamata a <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="e6fd7-361">Esegue qualsiasi filtro azione successivo e il metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="e6fd7-362">Restituisce `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="e6fd7-363">Per causare il corto circuito, assegnare <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a un'istanza del risultato e non chiamare `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="e6fd7-364">Il framework fornisce un oggetto <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> astratto che è possibile impostare come sottoclasse.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="e6fd7-365">Il filtro di azione `OnActionExecuting` può essere usato per:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="e6fd7-366">Convalidare lo stato del modello.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-366">Validate model state.</span></span>
* <span data-ttu-id="e6fd7-367">Restituire un errore se lo stato non è valido.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="e6fd7-368">I controller annotati con l' `[ApiController]` attributo convalidano automaticamente lo stato del modello e restituiscono una risposta 400.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-368">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="e6fd7-369">Per altre informazioni, vedere [Risposte HTTP 400 automatiche](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-369">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="e6fd7-370">Il metodo `OnActionExecuted` viene eseguito dopo il metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-370">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="e6fd7-371">È possibile visualizzare e modificare i risultati dell'azione tramite la proprietà <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-371">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="e6fd7-372">L'impostazione di <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> è true se un altro filtro ha causato il corto circuito dell'esecuzione dell'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-372"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="e6fd7-373">L'impostazione di <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> è un valore non Null se l'azione o un filtro di azione successivo ha generato un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="e6fd7-374">Impostazione di `Exception` su null:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-374">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="e6fd7-375">Gestisce un'eccezione in modo efficace.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-375">Effectively handles an exception.</span></span>
  * <span data-ttu-id="e6fd7-376">L'oggetto `ActionExecutedContext.Result` viene eseguito come se fosse stato restituito normalmente dal metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-376">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="e6fd7-377">Filtri eccezioni</span><span class="sxs-lookup"><span data-stu-id="e6fd7-377">Exception filters</span></span>

<span data-ttu-id="e6fd7-378">Filtri eccezioni:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-378">Exception filters:</span></span>

* <span data-ttu-id="e6fd7-379">Implementano <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-379">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="e6fd7-380">Possono essere usati per implementare criteri di gestione degli errori comuni.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-380">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="e6fd7-381">Il filtro di eccezione di esempio seguente usa una visualizzazione degli errori personalizzata per mostrare i dettagli sulle eccezioni che si verificano quando l'app è in fase di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-381">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="e6fd7-382">Il codice seguente verifica il filtro eccezioni:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-382">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="e6fd7-383">Filtri eccezioni:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-383">Exception filters:</span></span>

* <span data-ttu-id="e6fd7-384">Non dispone di eventi precedenti o successivi.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-384">Don't have before and after events.</span></span>
* <span data-ttu-id="e6fd7-385">Implementano <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-385">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="e6fd7-386">Gestire le eccezioni non gestite che si verificano nella Razor creazione della pagina o del controller, dell' [associazione di modelli](xref:mvc/models/model-binding), dei filtri azione o dei metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-386">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="e6fd7-387">**Non** rilevano le eccezioni che si verificano nei filtri di risorse, nei filtri dei risultati o nell'esecuzione dei risultati MVC.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-387">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="e6fd7-388">Per gestire un'eccezione, impostare la proprietà <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> su `true` o scrivere una risposta.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-388">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="e6fd7-389">In questo modo si arresta la propagazione dell'eccezione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-389">This stops propagation of the exception.</span></span> <span data-ttu-id="e6fd7-390">Un filtro di eccezione non può modificare un'eccezione in una "operazione riuscita".</span><span class="sxs-lookup"><span data-stu-id="e6fd7-390">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="e6fd7-391">Questa operazione può essere eseguita solo tramite un filtro di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-391">Only an action filter can do that.</span></span>

<span data-ttu-id="e6fd7-392">Filtri eccezioni:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-392">Exception filters:</span></span>

* <span data-ttu-id="e6fd7-393">Sono ideali per intercettare le eccezioni che si verificano nelle azioni.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-393">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="e6fd7-394">Non sono flessibili quanto il middleware di gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-394">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="e6fd7-395">Scegliere il middleware per la gestione delle eccezioni.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-395">Prefer middleware for exception handling.</span></span> <span data-ttu-id="e6fd7-396">Usare i filtri di eccezione solo quando la gestione degli errori *varia* in base al metodo di azione chiamato.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-396">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="e6fd7-397">Un'app può ad esempio avere metodi di azione sia per gli endpoint API che per le visualizzazioni/HTML.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-397">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="e6fd7-398">Gli endpoint dell'API possono restituire informazioni sull'errore come JSON, mentre le azioni basate sulla visualizzazione possono restituire una pagina di errore in formato HTML.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-398">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="e6fd7-399">Filtri risultato</span><span class="sxs-lookup"><span data-stu-id="e6fd7-399">Result filters</span></span>

<span data-ttu-id="e6fd7-400">I filtri dei risultati:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-400">Result filters:</span></span>

* <span data-ttu-id="e6fd7-401">Implementare un'interfaccia:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-401">Implement an interface:</span></span>
  * <span data-ttu-id="e6fd7-402"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="e6fd7-402"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="e6fd7-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="e6fd7-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="e6fd7-404">La loro esecuzione circonda l'esecuzione dei risultati dell'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-404">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="e6fd7-405">IResultFilter e IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="e6fd7-405">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="e6fd7-406">Il codice seguente illustra un filtro dei risultati che aggiunge un'intestazione HTTP:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-406">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="e6fd7-407">Il tipo di risultato eseguito dipende dall'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-407">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="e6fd7-408">Un'azione che restituisce una visualizzazione include tutte le operazioni di elaborazione Razor come parte dell' <xref:Microsoft.AspNetCore.Mvc.ViewResult> oggetto in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-408">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="e6fd7-409">Un metodo API può eseguire la serializzazione in quanto parte dell'esecuzione del risultato.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-409">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="e6fd7-410">Altre informazioni sui [risultati dell'azione](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-410">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="e6fd7-411">I filtri dei risultati vengono eseguiti solo quando un filtro azione o azione produce un risultato di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-411">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="e6fd7-412">I filtri dei risultati non vengono eseguiti nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-412">Result filters are not executed when:</span></span>

* <span data-ttu-id="e6fd7-413">Un filtro di autorizzazione o un filtro risorse cortocircui la pipeline.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-413">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="e6fd7-414">Un filtro di eccezione non gestisca un'eccezione producendo un risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-414">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="e6fd7-415">Il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> può causare il corto circuito dell'esecuzione del risultato dell'azione e dei filtri dei risultati successivi se si imposta <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> su `true`.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-415">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="e6fd7-416">In caso di corto circuito, scrivere nell'oggetto risposta per evitare di generare una risposta vuota.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-416">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="e6fd7-417">Generazione di un'eccezione in `IResultFilter.OnResultExecuting` :</span><span class="sxs-lookup"><span data-stu-id="e6fd7-417">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="e6fd7-418">Impedisce l'esecuzione del risultato dell'azione e dei filtri successivi.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-418">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="e6fd7-419">Viene considerato un errore anziché un risultato positivo.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-419">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="e6fd7-420">Quando <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> viene eseguito il metodo, è probabile che la risposta sia già stata inviata al client.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-420">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="e6fd7-421">Se la risposta è già stata inviata al client, non è possibile modificarla.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-421">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="e6fd7-422">L'impostazione di `ResultExecutedContext.Canceled` è `true` se un altro filtro ha causato il corto circuito dell'esecuzione del risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-422">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="e6fd7-423">L'impostazione di `ResultExecutedContext.Exception` è un valore non Null se il risultato dell'azione o un filtro dei risultati successivo ha generato un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-423">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="e6fd7-424">`Exception`L'impostazione di su null consente di gestire in modo efficace un'eccezione e impedisce che l'eccezione venga generata nuovamente in un secondo momento nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-424">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="e6fd7-425">Non c'è alcun modo affidabile per scrivere i dati in una risposta quando si gestisce un'eccezione in un filtro dei risultati.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-425">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="e6fd7-426">Se le intestazioni sono state scaricate nel client quando il risultato di un'azione genera un'eccezione, non c'è alcun meccanismo affidabile per inviare un codice di errore.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-426">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="e6fd7-427">Per un oggetto <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, una chiamata a `await next` in <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> esegue tutti i filtri dei risultati successivi e il risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-427">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="e6fd7-428">Per causare il corto circuito, impostare [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) su `true` e non chiamare `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-428">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="e6fd7-429">Il framework fornisce un oggetto `ResultFilterAttribute` astratto che è possibile impostare come sottoclasse.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-429">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="e6fd7-430">La classe [AddHeaderAttribute](#add-header-attribute) illustrata in precedenza è un esempio di un attributo di filtro dei risultati.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-430">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="e6fd7-431">IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="e6fd7-431">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="e6fd7-432">Le interfacce <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> dichiarano un'implementazione di <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> eseguita per tutti i risultati dell'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-432">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="e6fd7-433">Sono inclusi i risultati dell'azione prodotti da:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-433">This includes action results produced by:</span></span>

* <span data-ttu-id="e6fd7-434">Filtri di autorizzazione e filtri delle risorse che interessano il cortocircuito.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-434">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="e6fd7-435">Filtri eccezioni.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-435">Exception filters.</span></span>

<span data-ttu-id="e6fd7-436">Ad esempio, il filtro seguente viene eseguito sempre e imposta un risultato dell'azione (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) con un codice di stato *422 Entità non elaborabile* quando la negoziazione del contenuto ha esito negativo:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-436">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="e6fd7-437">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="e6fd7-437">IFilterFactory</span></span>

<span data-ttu-id="e6fd7-438"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-438"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="e6fd7-439">Pertanto, un'istanza di `IFilterFactory` può essere usata come un'istanza di `IFilterMetadata` in un punto qualsiasi della pipeline filtro.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-439">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="e6fd7-440">Quando il runtime si prepara per richiamare il filtro, cerca di eseguirne il cast a un oggetto `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-440">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="e6fd7-441">Se l'esecuzione del cast ha esito positivo, viene chiamato il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> per creare l'istanza di `IFilterMetadata` richiamata.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-441">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="e6fd7-442">In questo modo viene specificata una struttura flessibile, poiché non è necessario impostare in modo esplicito la pipeline filtro all'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-442">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="e6fd7-443">Un altro approccio alla creazione di filtri consiste nell'implementare `IFilterFactory` usando implementazioni dell'attributo personalizzate:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-443">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="e6fd7-444">Il filtro viene applicato nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-444">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="e6fd7-445">Testare il codice precedente eseguendo l' [esempio di download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="e6fd7-445">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="e6fd7-446">Richiamare gli strumenti di sviluppo F12.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-446">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="e6fd7-447">Accedere a `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-447">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="e6fd7-448">Gli strumenti di sviluppo F12 visualizzano le intestazioni di risposta seguenti aggiunte dal codice di esempio:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-448">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="e6fd7-449">**Autore:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="e6fd7-449">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="e6fd7-450">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="e6fd7-450">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="e6fd7-451">**interno:**`My header`</span><span class="sxs-lookup"><span data-stu-id="e6fd7-451">**internal:** `My header`</span></span>

<span data-ttu-id="e6fd7-452">Il codice precedente crea l'intestazione di risposta **internal:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-452">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="e6fd7-453">Implementazione di IFilterFactory in un attributo</span><span class="sxs-lookup"><span data-stu-id="e6fd7-453">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="e6fd7-454">I filtri che implementano `IFilterFactory` sono utili per i filtri che:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-454">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="e6fd7-455">Non richiedono il passaggio di parametri.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-455">Don't require passing parameters.</span></span>
* <span data-ttu-id="e6fd7-456">Hanno dipendenze del costruttore che devono essere soddisfatte dall'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-456">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="e6fd7-457"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-457"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="e6fd7-458">`IFilterFactory` espone il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> per creare un'istanza <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-458">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="e6fd7-459">`CreateInstance` carica il tipo specificato dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-459">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="e6fd7-460">Il codice seguente illustra tre approcci all'applicazione di `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-460">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="e6fd7-461">Nel codice precedente la decorazione del metodo con `[SampleActionFilter]` è l'approccio da preferire per applicare `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-461">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="e6fd7-462">Uso di middleware nella pipeline filtro</span><span class="sxs-lookup"><span data-stu-id="e6fd7-462">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="e6fd7-463">I filtri risorse funzionano come [middleware](xref:fundamentals/middleware/index) in quanto racchiudono l'esecuzione di tutto ciò che viene dopo nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-463">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="e6fd7-464">Tuttavia, i filtri differiscono dal middleware in quanto fanno parte del runtime, il che significa che hanno accesso a contesto e costrutti.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-464">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="e6fd7-465">Per usare il middleware come filtro, creare un tipo con un metodo `Configure` che specifica il middleware da inserire nella pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-465">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="e6fd7-466">L'esempio seguente usa il middleware di localizzazione per stabilire le impostazioni cultura correnti per una richiesta:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-466">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="e6fd7-467">Usare <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> per eseguire il middleware:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-467">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="e6fd7-468">I filtri middleware vengono eseguiti nella stessa fase della pipeline filtro come filtri risorse, prima dell'associazione di modelli e dopo il resto della pipeline.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-468">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="e6fd7-469">Azioni successive</span><span class="sxs-lookup"><span data-stu-id="e6fd7-469">Next actions</span></span>

* <span data-ttu-id="e6fd7-470">Vedere [metodi di filtro per le Razor pagine](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-470">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="e6fd7-471">Per sperimentare i filtri, [scaricare, testare e modificare l'esempio di GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-471">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e6fd7-472">Di [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="e6fd7-472">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="e6fd7-473">I *filtri* in ASP.NET Core consentono l'esecuzione del codice prima o dopo fasi specifiche della pipeline di elaborazione della richiesta.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-473">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="e6fd7-474">I filtri predefiniti gestiscono attività, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-474">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="e6fd7-475">Autorizzazione (impedire l'accesso alle risorse per cui un utente non è autorizzato).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-475">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="e6fd7-476">Memorizzazione nella cache delle risposte (blocco della pipeline delle richieste per restituire una risposta memorizzata nella cache).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-476">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="e6fd7-477">I filtri personalizzati possono essere creati per gestire problemi relativi a più settori.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-477">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="e6fd7-478">Esempi di problematiche trasversali includono la gestione degli errori, la memorizzazione nella cache, la configurazione, l'autorizzazione e la registrazione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-478">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="e6fd7-479">I filtri evitano la duplicazione del codice.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-479">Filters avoid duplicating code.</span></span> <span data-ttu-id="e6fd7-480">Ad esempio, un filtro eccezioni per la gestione degli errori potrebbe consolidare la gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-480">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="e6fd7-481">Questo documento si applica a Razor pagine, controller API e controller con visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-481">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="e6fd7-482">[Visualizzare o scaricare un esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-482">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="e6fd7-483">Funzionamento dei filtri</span><span class="sxs-lookup"><span data-stu-id="e6fd7-483">How filters work</span></span>

<span data-ttu-id="e6fd7-484">I filtri vengono eseguiti all'interno della *pipeline di chiamata di azioni ASP.NET Core*, talvolta definita *pipeline di filtro*.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-484">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="e6fd7-485">La pipeline di filtro viene eseguita dopo che ASP.NET Core ha selezionato l'azione da eseguire.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-485">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![La richiesta viene elaborata tramite altro middleware, il middleware di routing, la selezione dell'azione e la pipeline di chiamata di azioni ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="e6fd7-488">Tipi di filtro</span><span class="sxs-lookup"><span data-stu-id="e6fd7-488">Filter types</span></span>

<span data-ttu-id="e6fd7-489">Ogni tipo di filtro viene eseguito in una fase diversa della pipeline di filtro:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-489">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="e6fd7-490">I [filtri di autorizzazione](#authorization-filters) vengono eseguiti per primi e consentono di determinare se l'utente è autorizzato per la richiesta.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-490">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="e6fd7-491">I filtri di autorizzazione causano il corto circuito della pipeline se la richiesta non è autorizzata.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-491">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="e6fd7-492">[Filtri risorse](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="e6fd7-492">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="e6fd7-493">Vengono eseguiti dopo l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-493">Run after authorization.</span></span>  
  * <span data-ttu-id="e6fd7-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> può eseguire codice prima del resto della pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="e6fd7-495">Ad esempio, `OnResourceExecuting` può eseguire codice prima dell'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-495">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="e6fd7-496"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> può eseguire codice dopo che il resto della pipeline è stato completato.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-496"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="e6fd7-497">I [filtri azione](#action-filters) possono eseguire codice immediatamente prima e dopo la chiamata di un metodo di azione singolo.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-497">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="e6fd7-498">Possono essere usati per modificare gli argomenti passati a un'azione e il risultato restituito dall'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-498">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="e6fd7-499">I filtri azione **non** sono supportati nelle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-499">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="e6fd7-500">I [filtri eccezioni](#exception-filters) vengono usati per applicare i criteri globali a eccezioni non gestite che si verificano prima che qualsiasi elemento sia stato scritto nel corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-500">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="e6fd7-501">I [filtri risultato](#result-filters) possono eseguire codice immediatamente prima e dopo l'esecuzione di risultati di azioni singole.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-501">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="e6fd7-502">Vengono eseguiti solo quando il metodo di azione è stato eseguito correttamente.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-502">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="e6fd7-503">Sono utili per la logica che deve racchiudere l'esecuzione del formattatore o di una vista.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-503">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="e6fd7-504">Il diagramma seguente illustra come interagiscono i tipi di filtro nella pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-504">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![La richiesta passa attraverso i filtri autorizzazione, i filtri risorse, l'associazione di modelli, i filtri azione, l'esecuzione dell'azione e la conversione del risultato dell'azione, i filtri eccezioni, i filtri risultato e l'esecuzione del risultato.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="e6fd7-507">Implementazione</span><span class="sxs-lookup"><span data-stu-id="e6fd7-507">Implementation</span></span>

<span data-ttu-id="e6fd7-508">I filtri supportano sia implementazioni sincrone che asincrone tramite definizioni di interfaccia diverse.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-508">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="e6fd7-509">I filtri sincroni possono eseguire codice prima (`On-Stage-Executing`) e dopo (`On-Stage-Executed`) la relativa fase della pipeline.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-509">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="e6fd7-510">La chiamata di `OnActionExecuting`, ad esempio, avviene prima della chiamata del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-510">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="e6fd7-511">La chiamata di `OnActionExecuted` avviene dopo l'esecuzione del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-511">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="e6fd7-512">I filtri asincroni definiscono un metodo `On-Stage-ExecutionAsync`:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-512">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="e6fd7-513">Nel codice precedente `SampleAsyncActionFilter` ha un oggetto <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) che esegue il metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-513">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="e6fd7-514">Ognuno dei metodi `On-Stage-ExecutionAsync` accetta un oggetto `FilterType-ExecutionDelegate` che esegue la fase della pipeline del filtro.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-514">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="e6fd7-515">Fasi di filtro multiple</span><span class="sxs-lookup"><span data-stu-id="e6fd7-515">Multiple filter stages</span></span>

<span data-ttu-id="e6fd7-516">È possibile implementare interfacce per più fasi di filtro in una singola classe.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-516">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="e6fd7-517">Ad esempio, la classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementa `IActionFilter`, `IResultFilter` e i relativi equivalenti asincroni.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-517">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="e6fd7-518">Implementare la versione sincrona **oppure** la versione asincrona di un'interfaccia di filtro, **non** entrambe.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-518">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="e6fd7-519">Il runtime controlla per prima cosa se il filtro implementa l'interfaccia asincrona e, in tal caso, la chiama.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-519">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="e6fd7-520">In caso contrario, chiama i metodi dell'interfaccia sincrona.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-520">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="e6fd7-521">Se in una classe sono implementate entrambe le interfacce, sincrona e asincrona, viene chiamato solo il metodo asincrono.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-521">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="e6fd7-522">Quando si usano classi astratte come <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> eseguire l'override solo dei metodi sincroni o del metodo asincrono per ogni tipo di filtro.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-522">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="e6fd7-523">Attributi filtro predefiniti</span><span class="sxs-lookup"><span data-stu-id="e6fd7-523">Built-in filter attributes</span></span>

<span data-ttu-id="e6fd7-524">ASP.NET Core include filtri predefiniti basati su attributi che è possibile impostare come sottoclasse e personalizzare.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-524">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="e6fd7-525">Il filtro dei risultati seguente, ad esempio, aggiunge un'intestazione alla risposta:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-525">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="e6fd7-526">Gli attributi consentono ai filtri di accettare argomenti, come illustrato nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-526">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="e6fd7-527">Applicare `AddHeaderAttribute` a un controller o a un metodo di azione e specificare il nome e il valore dell'intestazione HTTP:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-527">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="e6fd7-528">Molte delle interfacce del filtro presentano attributi corrispondenti che possono essere usati come classi di base per implementazioni personalizzate.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-528">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="e6fd7-529">Attributi dei filtri:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-529">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="e6fd7-530">Ambiti dei filtri e ordine di esecuzione</span><span class="sxs-lookup"><span data-stu-id="e6fd7-530">Filter scopes and order of execution</span></span>

<span data-ttu-id="e6fd7-531">È possibile aggiungere un filtro alla pipeline in uno dei tre *ambiti*:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-531">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="e6fd7-532">Usando un attributo di un'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-532">Using an attribute on an action.</span></span>
* <span data-ttu-id="e6fd7-533">Usando un attributo di un controller.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-533">Using an attribute on a controller.</span></span>
* <span data-ttu-id="e6fd7-534">A livello globale per tutti i controller e le azioni come illustrato nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-534">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="e6fd7-535">Il codice precedente aggiunge tre filtri a livello globale tramite la raccolta [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-535">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="e6fd7-536">Ordine di esecuzione predefinito</span><span class="sxs-lookup"><span data-stu-id="e6fd7-536">Default order of execution</span></span>

<span data-ttu-id="e6fd7-537">Quando sono presenti più filtri *dello stesso tipo*, scope determina l'ordine predefinito di esecuzione del filtro.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-537">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="e6fd7-538">Filtri globali circondano i filtri di classe.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-538">Global filters surround class filters.</span></span> <span data-ttu-id="e6fd7-539">Filtri di classe filtri di metodo Racchiudi.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-539">Class filters surround method filters.</span></span>

<span data-ttu-id="e6fd7-540">Come risultato dell'annidamento dei filtri, il codice *after* dei filtri viene eseguito in ordine inverso rispetto al codice *before*.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-540">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="e6fd7-541">Sequenza di filtro:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-541">The filter sequence:</span></span>

* <span data-ttu-id="e6fd7-542">Codice *before* dei filtri globali.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-542">The *before* code of global filters.</span></span>
  * <span data-ttu-id="e6fd7-543">Codice *before* dei filtri del controller.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-543">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="e6fd7-544">Codice *before* dei filtri del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-544">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="e6fd7-545">Codice *after* dei filtri del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-545">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="e6fd7-546">Codice *after* dei filtri del controller.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-546">The *after* code of controller filters.</span></span>
* <span data-ttu-id="e6fd7-547">Codice *after* dei filtri globali.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-547">The *after* code of global filters.</span></span>
  
<span data-ttu-id="e6fd7-548">L'esempio seguente illustra l'ordine in cui i metodi dei filtri vengono chiamati per i filtri di azione sincroni.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-548">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="e6fd7-549">Sequenza</span><span class="sxs-lookup"><span data-stu-id="e6fd7-549">Sequence</span></span> | <span data-ttu-id="e6fd7-550">Ambito del filtro</span><span class="sxs-lookup"><span data-stu-id="e6fd7-550">Filter scope</span></span> | <span data-ttu-id="e6fd7-551">Metodo del filtro</span><span class="sxs-lookup"><span data-stu-id="e6fd7-551">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="e6fd7-552">1</span><span class="sxs-lookup"><span data-stu-id="e6fd7-552">1</span></span> | <span data-ttu-id="e6fd7-553">Globale</span><span class="sxs-lookup"><span data-stu-id="e6fd7-553">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="e6fd7-554">2</span><span class="sxs-lookup"><span data-stu-id="e6fd7-554">2</span></span> | <span data-ttu-id="e6fd7-555">Controller</span><span class="sxs-lookup"><span data-stu-id="e6fd7-555">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="e6fd7-556">3</span><span class="sxs-lookup"><span data-stu-id="e6fd7-556">3</span></span> | <span data-ttu-id="e6fd7-557">Metodo</span><span class="sxs-lookup"><span data-stu-id="e6fd7-557">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="e6fd7-558">4</span><span class="sxs-lookup"><span data-stu-id="e6fd7-558">4</span></span> | <span data-ttu-id="e6fd7-559">Metodo</span><span class="sxs-lookup"><span data-stu-id="e6fd7-559">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="e6fd7-560">5</span><span class="sxs-lookup"><span data-stu-id="e6fd7-560">5</span></span> | <span data-ttu-id="e6fd7-561">Controller</span><span class="sxs-lookup"><span data-stu-id="e6fd7-561">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="e6fd7-562">6</span><span class="sxs-lookup"><span data-stu-id="e6fd7-562">6</span></span> | <span data-ttu-id="e6fd7-563">Globale</span><span class="sxs-lookup"><span data-stu-id="e6fd7-563">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="e6fd7-564">Questa sequenza mostra che:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-564">This sequence shows:</span></span>

* <span data-ttu-id="e6fd7-565">Il filtro del metodo è annidato all'interno del filtro del controller.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-565">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="e6fd7-566">Il filtro del controller è annidato all'interno del filtro globale.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-566">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="e6fd7-567">Filtri a Razor livello di pagina e controller</span><span class="sxs-lookup"><span data-stu-id="e6fd7-567">Controller and Razor Page level filters</span></span>

<span data-ttu-id="e6fd7-568">Ogni controller che eredita dalla classe di base <xref:Microsoft.AspNetCore.Mvc.Controller> include i metodi [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) e [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-568">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="e6fd7-569">Questi metodi:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-569">These methods:</span></span>

* <span data-ttu-id="e6fd7-570">Eseguono il wrapping dei filtri che vengono eseguiti per una determinata azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-570">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="e6fd7-571">La chiamata di `OnActionExecuting` avviene prima di qualsiasi filtro dell'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-571">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="e6fd7-572">La chiamata di `OnActionExecuted` avviene dopo tutti i filtri dell'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-572">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="e6fd7-573">La chiamata di `OnActionExecutionAsync` avviene prima di qualsiasi filtro dell'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-573">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="e6fd7-574">Il codice del filtro dopo `next` viene eseguito dopo il metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-574">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="e6fd7-575">Ad esempio, l'applicazione di `MySampleActionFilter` nell'esempio scaricato avviene a livello globale all'avvio.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-575">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="e6fd7-576">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-576">The `TestController`:</span></span>

* <span data-ttu-id="e6fd7-577">Applica `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) all' `FilterTest2` azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-577">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="e6fd7-578">Esegue l'override di `OnActionExecuting` e `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-578">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="e6fd7-579">Passando a `https://localhost:5001/Test/FilterTest2`, viene eseguito il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-579">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="e6fd7-580">Per le Razor pagine, vedere [implementare Razor filtri di pagina eseguendo l'override dei metodi di filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-580">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="e6fd7-581">Override dell'ordine predefinito</span><span class="sxs-lookup"><span data-stu-id="e6fd7-581">Overriding the default order</span></span>

<span data-ttu-id="e6fd7-582">È possibile eseguire l'override della sequenza di esecuzione predefinita implementando <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-582">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="e6fd7-583">`IOrderedFilter` espone la proprietà <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order>, che ha la precedenza sull'ambito per determinare l'ordine di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-583">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="e6fd7-584">Un filtro con un valore di `Order` inferiore:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-584">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="e6fd7-585">Esegue il codice *before* prima di quello di un filtro con un valore di `Order` più alto.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-585">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="e6fd7-586">Esegue il codice *after* dopo quello di un filtro con un valore di `Order` più alto.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-586">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="e6fd7-587">La proprietà `Order` può essere impostata con un parametro del costruttore:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-587">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="e6fd7-588">Prendere in considerazione gli stessi tre filtri di azione illustrati nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-588">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="e6fd7-589">Se la proprietà `Order` del controller e dei filtri globali è impostata, rispettivamente, su 1 e su 2, l'ordine di esecuzione viene invertito.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-589">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="e6fd7-590">Sequenza</span><span class="sxs-lookup"><span data-stu-id="e6fd7-590">Sequence</span></span> | <span data-ttu-id="e6fd7-591">Ambito del filtro</span><span class="sxs-lookup"><span data-stu-id="e6fd7-591">Filter scope</span></span> | <span data-ttu-id="e6fd7-592">Proprietà `Order`</span><span class="sxs-lookup"><span data-stu-id="e6fd7-592">`Order` property</span></span> | <span data-ttu-id="e6fd7-593">Metodo del filtro</span><span class="sxs-lookup"><span data-stu-id="e6fd7-593">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="e6fd7-594">1</span><span class="sxs-lookup"><span data-stu-id="e6fd7-594">1</span></span> | <span data-ttu-id="e6fd7-595">Metodo</span><span class="sxs-lookup"><span data-stu-id="e6fd7-595">Method</span></span> | <span data-ttu-id="e6fd7-596">0</span><span class="sxs-lookup"><span data-stu-id="e6fd7-596">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="e6fd7-597">2</span><span class="sxs-lookup"><span data-stu-id="e6fd7-597">2</span></span> | <span data-ttu-id="e6fd7-598">Controller</span><span class="sxs-lookup"><span data-stu-id="e6fd7-598">Controller</span></span> | <span data-ttu-id="e6fd7-599">1</span><span class="sxs-lookup"><span data-stu-id="e6fd7-599">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="e6fd7-600">3</span><span class="sxs-lookup"><span data-stu-id="e6fd7-600">3</span></span> | <span data-ttu-id="e6fd7-601">Globale</span><span class="sxs-lookup"><span data-stu-id="e6fd7-601">Global</span></span> | <span data-ttu-id="e6fd7-602">2</span><span class="sxs-lookup"><span data-stu-id="e6fd7-602">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="e6fd7-603">4</span><span class="sxs-lookup"><span data-stu-id="e6fd7-603">4</span></span> | <span data-ttu-id="e6fd7-604">Globale</span><span class="sxs-lookup"><span data-stu-id="e6fd7-604">Global</span></span> | <span data-ttu-id="e6fd7-605">2</span><span class="sxs-lookup"><span data-stu-id="e6fd7-605">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="e6fd7-606">5</span><span class="sxs-lookup"><span data-stu-id="e6fd7-606">5</span></span> | <span data-ttu-id="e6fd7-607">Controller</span><span class="sxs-lookup"><span data-stu-id="e6fd7-607">Controller</span></span> | <span data-ttu-id="e6fd7-608">1</span><span class="sxs-lookup"><span data-stu-id="e6fd7-608">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="e6fd7-609">6</span><span class="sxs-lookup"><span data-stu-id="e6fd7-609">6</span></span> | <span data-ttu-id="e6fd7-610">Metodo</span><span class="sxs-lookup"><span data-stu-id="e6fd7-610">Method</span></span> | <span data-ttu-id="e6fd7-611">0</span><span class="sxs-lookup"><span data-stu-id="e6fd7-611">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="e6fd7-612">La proprietà `Order` ha la precedenza sull'ambito nel determinare l'ordine di esecuzione dei filtri.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-612">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="e6fd7-613">I filtri vengono ordinati prima in base all'ordine, poi viene usato l'ambito per interrompere i collegamenti.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-613">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="e6fd7-614">Tutti i filtri predefiniti implementano `IOrderedFilter` e impostano il valore predefinito di `Order` su 0.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-614">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="e6fd7-615">Per i filtri predefiniti, l'ambito determina l'ordine, a meno che non si imposti `Order` su un valore diverso da zero.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-615">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="e6fd7-616">Annullamento e corto circuito</span><span class="sxs-lookup"><span data-stu-id="e6fd7-616">Cancellation and short-circuiting</span></span>

<span data-ttu-id="e6fd7-617">È possibile causare il corto circuito della pipeline di filtro impostando la proprietà <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> sul parametro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fornito al metodo di filtro.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-617">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="e6fd7-618">Ad esempio, il filtro di risorse seguente impedisce l'esecuzione del resto della pipeline:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-618">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="e6fd7-619">Nel codice seguente sia il filtro `ShortCircuitingResourceFilter` che il filtro `AddHeader` hanno come destinazione il metodo di azione `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-619">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="e6fd7-620">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-620">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="e6fd7-621">Viene eseguito per primo perché è un filtro risorsa e `AddHeader` è un filtro azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-621">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="e6fd7-622">Blocca il resto della pipeline.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-622">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="e6fd7-623">Pertanto il filtro `AddHeader` non viene mai eseguito per l'azione `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-623">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="e6fd7-624">Questo comportamento sarebbe lo stesso se entrambi i filtri venissero applicati a livello di metodo di azione, a condizione che il filtro `ShortCircuitingResourceFilter` venga eseguito prima.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-624">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="e6fd7-625">`ShortCircuitingResourceFilter` viene eseguito per primo per il tipo di filtro o per l'uso esplicito della proprietà `Order`.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-625">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="e6fd7-626">Inserimento delle dipendenze</span><span class="sxs-lookup"><span data-stu-id="e6fd7-626">Dependency injection</span></span>

<span data-ttu-id="e6fd7-627">È possibile aggiungere filtri per tipo o per istanza.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-627">Filters can be added by type or by instance.</span></span> <span data-ttu-id="e6fd7-628">Se viene aggiunta un'istanza, tale istanza viene usata per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-628">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="e6fd7-629">Se viene aggiunto un tipo, il filtro è attivato dal tipo.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-629">If a type is added, it's type-activated.</span></span> <span data-ttu-id="e6fd7-630">Un filtro attivato dal tipo comporta:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-630">A type-activated filter means:</span></span>

* <span data-ttu-id="e6fd7-631">La creazione di un'istanza per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-631">An instance is created for each request.</span></span>
* <span data-ttu-id="e6fd7-632">Il popolamento di tutte le dipendenze del costruttore tramite [inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-632">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="e6fd7-633">I filtri implementati come attributi e aggiunti direttamente alle classi controller o ai metodi di azione non possono avere dipendenze costruttore specificate dall'[inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-633">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="e6fd7-634">Le dipendenze del costruttore non possono essere fornite tramite inserimento delle dipendenze in quanto:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-634">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="e6fd7-635">I parametri del costruttore devono essere forniti agli attributi nella posizione in cui vengono applicati.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-635">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="e6fd7-636">Questa è una limitazione del funzionamento degli attributi.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-636">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="e6fd7-637">I filtri seguenti supportano le dipendenze del costruttore fornite dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-637">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="e6fd7-638"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementato nell'attributo.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-638"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="e6fd7-639">I filtri precedenti possono essere applicati a un controller o a un metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-639">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="e6fd7-640">I logger sono disponibili tramite l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-640">Loggers are available from DI.</span></span> <span data-ttu-id="e6fd7-641">Evitare tuttavia di creare e usare filtri esclusivamente per scopi di registrazione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-641">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="e6fd7-642">La funzionalità di [registrazione del framework predefinita](xref:fundamentals/logging/index) in genere fornisce il necessario per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-642">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="e6fd7-643">La registrazione aggiunta ai filtri:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-643">Logging added to filters:</span></span>

* <span data-ttu-id="e6fd7-644">Deve concentrarsi su problemi del dominio di business o comportamenti specifici del filtro.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-644">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="e6fd7-645">**Non** deve registrare azioni o altri eventi del framework.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-645">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="e6fd7-646">I filtri predefiniti registrano azioni ed eventi del framework.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-646">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="e6fd7-647">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="e6fd7-647">ServiceFilterAttribute</span></span>

<span data-ttu-id="e6fd7-648">I tipi di implementazione del filtro di servizi sono registrati in `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-648">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="e6fd7-649"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera un'istanza del filtro dall'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-649">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="e6fd7-650">Il codice seguente illustra `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-650">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="e6fd7-651">Nel codice seguente l'oggetto `AddHeaderResultServiceFilter` viene aggiunto al contenitore di inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-651">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="e6fd7-652">Nel codice seguente l'attributo `ServiceFilter` recupera un'istanza del filtro `AddHeaderResultServiceFilter` dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-652">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="e6fd7-653">Quando si usa l'impostazione `ServiceFilterAttribute`, [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="e6fd7-653">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="e6fd7-654">Indica che l'istanza del filtro *può* essere riutilizzata all'esterno dell'ambito della richiesta in cui è stata creata.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-654">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="e6fd7-655">Il runtime di ASP.NET Core non garantisce:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-655">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="e6fd7-656">Che venga creata una singola istanza del filtro.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-656">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="e6fd7-657">Che il filtro non verrà richiesto di nuovo dal contenitore di inserimento delle dipendenze in un momento successivo.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-657">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="e6fd7-658">Non si deve usare con un filtro che dipende da servizi con una durata diversa da singleton.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-658">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="e6fd7-659"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-659"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="e6fd7-660">`IFilterFactory` espone il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> per creare un'istanza <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-660">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="e6fd7-661">`CreateInstance` carica il tipo specificato dall'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-661">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="e6fd7-662">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="e6fd7-662">TypeFilterAttribute</span></span>

<span data-ttu-id="e6fd7-663"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> è simile a <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, ma il relativo tipo non viene risolto direttamente dal contenitore dell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-663"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="e6fd7-664">Viene creata un'istanza del tipo tramite <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-664">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="e6fd7-665">Poiché i tipi `TypeFilterAttribute` non vengono risolti direttamente dal contenitore di inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-665">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="e6fd7-666">Non è necessario che i tipi a cui viene fatto riferimento tramite `TypeFilterAttribute` siano registrati nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-666">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="e6fd7-667">Le loro dipendenze vengono soddisfatte dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-667">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="e6fd7-668">In via facoltativa, `TypeFilterAttribute` può anche accettare gli argomenti del costruttore per il tipo.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-668">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="e6fd7-669">Quando si usa l'impostazione `TypeFilterAttribute`, [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="e6fd7-669">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="e6fd7-670">Indica che l'istanza del filtro *può* essere riutilizzata all'esterno dell'ambito della richiesta in cui è stata creata.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-670">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="e6fd7-671">Il runtime di ASP.NET Core non fornisce alcuna garanzia che venga creata una singola istanza del filtro.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-671">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="e6fd7-672">Non si deve usare con un filtro che dipende da servizi con una durata diversa da singleton.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-672">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="e6fd7-673">L'esempio seguente illustra come passare argomenti a un tipo usando `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-673">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="e6fd7-674">Filtri autorizzazione</span><span class="sxs-lookup"><span data-stu-id="e6fd7-674">Authorization filters</span></span>

<span data-ttu-id="e6fd7-675">I filtri di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-675">Authorization filters:</span></span>

* <span data-ttu-id="e6fd7-676">Sono i primi filtri a essere eseguiti nella pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-676">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="e6fd7-677">Controllano l'accesso ai metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-677">Control access to action methods.</span></span>
* <span data-ttu-id="e6fd7-678">Dispongono di un metodo precedente, ma non di un metodo successivo.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-678">Have a before method, but no after method.</span></span>

<span data-ttu-id="e6fd7-679">I filtri di autorizzazione personalizzati richiedono un framework di autorizzazione personalizzato.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-679">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="e6fd7-680">È preferibile configurare criteri di autorizzazione o scrivere criteri di autorizzazione personalizzati piuttosto che scrivere un filtro personalizzato.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-680">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="e6fd7-681">Il filtro di autorizzazione predefinito:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-681">The built-in authorization filter:</span></span>

* <span data-ttu-id="e6fd7-682">Chiama il sistema di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-682">Calls the authorization system.</span></span>
* <span data-ttu-id="e6fd7-683">Non autorizza le richieste.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-683">Does not authorize requests.</span></span>

<span data-ttu-id="e6fd7-684">**Non** generare eccezioni nei filtri di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-684">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="e6fd7-685">L'eccezione non verrà gestita.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-685">The exception will not be handled.</span></span>
* <span data-ttu-id="e6fd7-686">I filtri di eccezione non gestiranno l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-686">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="e6fd7-687">È consigliabile emettere una richiesta di verifica in caso di eccezione in un filtro di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-687">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="e6fd7-688">Altre informazioni sull'[autorizzazione](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-688">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="e6fd7-689">Filtri risorse</span><span class="sxs-lookup"><span data-stu-id="e6fd7-689">Resource filters</span></span>

<span data-ttu-id="e6fd7-690">I filtri di risorse:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-690">Resource filters:</span></span>

* <span data-ttu-id="e6fd7-691">Implementano l'interfaccia <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-691">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="e6fd7-692">L'esecuzione racchiude la maggior parte della pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-692">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="e6fd7-693">Solo i [filtri di autorizzazione](#authorization-filters) vengono eseguiti prima dei filtri di risorse.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-693">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="e6fd7-694">I filtri di risorse sono utili per causare il corto circuito della maggior parte della pipeline.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-694">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="e6fd7-695">Un filtro di memorizzazione nella cache può, ad esempio, evitare il resto della pipeline in caso di riscontro nella cache.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-695">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="e6fd7-696">Esempi di filtri di risorse:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-696">Resource filter examples:</span></span>

* <span data-ttu-id="e6fd7-697">Il [filtro di risorse di corto circuito](#short-circuiting-resource-filter) illustrato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-697">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="e6fd7-698">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="e6fd7-698">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="e6fd7-699">Impedisce all'associazione di modelli di accedere ai dati del modulo.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-699">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="e6fd7-700">Viene usato quando si caricano file di grandi dimensioni per impedire la lettura dei dati del modulo in memoria.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-700">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="e6fd7-701">Filtri azione</span><span class="sxs-lookup"><span data-stu-id="e6fd7-701">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e6fd7-702">I filtri azione **non** si applicano alle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-702">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="e6fd7-703">Pages supporta <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="e6fd7-703"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="e6fd7-704">Per ulteriori informazioni, vedere [metodi di filtro per le Razor pagine](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-704">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="e6fd7-705">I filtri di azione:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-705">Action filters:</span></span>

* <span data-ttu-id="e6fd7-706">Implementano l'interfaccia <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-706">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="e6fd7-707">La loro esecuzione circonda l'esecuzione dei metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-707">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="e6fd7-708">Il codice seguente mostra un filtro di azione di esempio:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-708">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="e6fd7-709">La classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> specifica le proprietà seguenti:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-709">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="e6fd7-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>: consente di leggere gli input per un metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="e6fd7-711"><xref:Microsoft.AspNetCore.Mvc.Controller>: consente di modificare l'istanza del controller.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-711"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="e6fd7-712"><xref:System.Web.Mvc.ActionExecutingContext.Result>: l'impostazione di `Result` causa il corto circuito del metodo di azione e dei filtri di azione successivi.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-712"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="e6fd7-713">La generazione di un'eccezione in un metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-713">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="e6fd7-714">Impedisce l'esecuzione dei filtri successivi.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-714">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="e6fd7-715">A differenza dell'impostazione di `Result`, è considerata un errore anziché un risultato positivo.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-715">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="e6fd7-716">La classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> specifica `Controller` e `Result` oltre alle proprietà seguenti:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-716">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="e6fd7-717"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>: true se un altro file ha causato il corto circuito dell'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-717"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="e6fd7-718"><xref:System.Web.Mvc.ActionExecutedContext.Exception>: non Null se l'azione o un filtro di azione eseguito in precedenza ha generato un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-718"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="e6fd7-719">Se si imposta questa proprietà su Null:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-719">Setting this property to null:</span></span>

  * <span data-ttu-id="e6fd7-720">L'eccezione viene gestita in modo efficace.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-720">Effectively handles the exception.</span></span>
  * <span data-ttu-id="e6fd7-721">L'oggetto `Result` viene eseguito come se fosse stato restituito dal metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-721">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="e6fd7-722">Per un oggetto `IAsyncActionFilter`, una chiamata a <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-722">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="e6fd7-723">Esegue qualsiasi filtro azione successivo e il metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-723">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="e6fd7-724">Restituisce `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-724">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="e6fd7-725">Per causare il corto circuito, assegnare <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a un'istanza del risultato e non chiamare `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-725">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="e6fd7-726">Il framework fornisce un oggetto <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> astratto che è possibile impostare come sottoclasse.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-726">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="e6fd7-727">Il filtro di azione `OnActionExecuting` può essere usato per:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-727">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="e6fd7-728">Convalidare lo stato del modello.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-728">Validate model state.</span></span>
* <span data-ttu-id="e6fd7-729">Restituire un errore se lo stato non è valido.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-729">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="e6fd7-730">Il metodo `OnActionExecuted` viene eseguito dopo il metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-730">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="e6fd7-731">È possibile visualizzare e modificare i risultati dell'azione tramite la proprietà <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-731">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="e6fd7-732">L'impostazione di <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> è true se un altro filtro ha causato il corto circuito dell'esecuzione dell'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-732"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="e6fd7-733">L'impostazione di <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> è un valore non Null se l'azione o un filtro di azione successivo ha generato un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="e6fd7-734">Impostazione di `Exception` su null:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-734">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="e6fd7-735">Gestisce un'eccezione in modo efficace.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-735">Effectively handles an exception.</span></span>
  * <span data-ttu-id="e6fd7-736">L'oggetto `ActionExecutedContext.Result` viene eseguito come se fosse stato restituito normalmente dal metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-736">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="e6fd7-737">Filtri eccezioni</span><span class="sxs-lookup"><span data-stu-id="e6fd7-737">Exception filters</span></span>

<span data-ttu-id="e6fd7-738">Filtri eccezioni:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-738">Exception filters:</span></span>

* <span data-ttu-id="e6fd7-739">Implementano <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-739">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="e6fd7-740">Possono essere usati per implementare criteri di gestione degli errori comuni.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-740">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="e6fd7-741">Il filtro di eccezione di esempio seguente usa una visualizzazione degli errori personalizzata per mostrare i dettagli sulle eccezioni che si verificano quando l'app è in fase di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-741">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="e6fd7-742">Filtri eccezioni:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-742">Exception filters:</span></span>

* <span data-ttu-id="e6fd7-743">Non dispone di eventi precedenti o successivi.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-743">Don't have before and after events.</span></span>
* <span data-ttu-id="e6fd7-744">Implementano <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-744">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="e6fd7-745">Gestire le eccezioni non gestite che si verificano nella Razor creazione della pagina o del controller, dell' [associazione di modelli](xref:mvc/models/model-binding), dei filtri azione o dei metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-745">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="e6fd7-746">**Non** rilevano le eccezioni che si verificano nei filtri di risorse, nei filtri dei risultati o nell'esecuzione dei risultati MVC.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-746">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="e6fd7-747">Per gestire un'eccezione, impostare la proprietà <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> su `true` o scrivere una risposta.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-747">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="e6fd7-748">In questo modo si arresta la propagazione dell'eccezione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-748">This stops propagation of the exception.</span></span> <span data-ttu-id="e6fd7-749">Un filtro di eccezione non può modificare un'eccezione in una "operazione riuscita".</span><span class="sxs-lookup"><span data-stu-id="e6fd7-749">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="e6fd7-750">Questa operazione può essere eseguita solo tramite un filtro di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-750">Only an action filter can do that.</span></span>

<span data-ttu-id="e6fd7-751">Filtri eccezioni:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-751">Exception filters:</span></span>

* <span data-ttu-id="e6fd7-752">Sono ideali per intercettare le eccezioni che si verificano nelle azioni.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-752">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="e6fd7-753">Non sono flessibili quanto il middleware di gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-753">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="e6fd7-754">Scegliere il middleware per la gestione delle eccezioni.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-754">Prefer middleware for exception handling.</span></span> <span data-ttu-id="e6fd7-755">Usare i filtri di eccezione solo quando la gestione degli errori *varia* in base al metodo di azione chiamato.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-755">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="e6fd7-756">Un'app può ad esempio avere metodi di azione sia per gli endpoint API che per le visualizzazioni/HTML.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-756">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="e6fd7-757">Gli endpoint dell'API possono restituire informazioni sull'errore come JSON, mentre le azioni basate sulla visualizzazione possono restituire una pagina di errore in formato HTML.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-757">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="e6fd7-758">Filtri risultato</span><span class="sxs-lookup"><span data-stu-id="e6fd7-758">Result filters</span></span>

<span data-ttu-id="e6fd7-759">I filtri dei risultati:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-759">Result filters:</span></span>

* <span data-ttu-id="e6fd7-760">Implementare un'interfaccia:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-760">Implement an interface:</span></span>
  * <span data-ttu-id="e6fd7-761"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="e6fd7-761"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="e6fd7-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="e6fd7-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="e6fd7-763">La loro esecuzione circonda l'esecuzione dei risultati dell'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-763">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="e6fd7-764">IResultFilter e IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="e6fd7-764">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="e6fd7-765">Il codice seguente illustra un filtro dei risultati che aggiunge un'intestazione HTTP:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-765">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="e6fd7-766">Il tipo di risultato eseguito dipende dall'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-766">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="e6fd7-767">Un'azione che restituisce una visualizzazione include tutte le elaborazioni Razor come parte dell'elemento <xref:Microsoft.AspNetCore.Mvc.ViewResult> eseguito.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-767">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="e6fd7-768">Un metodo API può eseguire la serializzazione in quanto parte dell'esecuzione del risultato.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-768">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="e6fd7-769">Altre informazioni sui [risultati dell'azione](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-769">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="e6fd7-770">I filtri dei risultati vengono eseguiti solo quando un filtro azione o azione produce un risultato di azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-770">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="e6fd7-771">I filtri dei risultati non vengono eseguiti nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-771">Result filters are not executed when:</span></span>

* <span data-ttu-id="e6fd7-772">Un filtro di autorizzazione o un filtro risorse cortocircui la pipeline.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-772">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="e6fd7-773">Un filtro di eccezione non gestisca un'eccezione producendo un risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-773">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="e6fd7-774">Il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> può causare il corto circuito dell'esecuzione del risultato dell'azione e dei filtri dei risultati successivi se si imposta <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> su `true`.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-774">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="e6fd7-775">In caso di corto circuito, scrivere nell'oggetto risposta per evitare di generare una risposta vuota.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-775">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="e6fd7-776">La generazione di un'eccezione in `IResultFilter.OnResultExecuting`:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-776">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="e6fd7-777">Impedisce l'esecuzione del risultato dell'azione e dei filtri successivi.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-777">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="e6fd7-778">È considerata un errore anziché un risultato positivo.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-778">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="e6fd7-779">Quando <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> viene eseguito il metodo, è probabile che la risposta sia già stata inviata al client.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-779">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="e6fd7-780">Se la risposta è già stata inviata al client, non è possibile modificarla ulteriormente.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-780">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="e6fd7-781">L'impostazione di `ResultExecutedContext.Canceled` è `true` se un altro filtro ha causato il corto circuito dell'esecuzione del risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-781">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="e6fd7-782">L'impostazione di `ResultExecutedContext.Exception` è un valore non Null se il risultato dell'azione o un filtro dei risultati successivo ha generato un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-782">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="e6fd7-783">Se si imposta `Exception` su Null l'eccezione viene gestita in modo efficace e si evita che venga generata nuovamente da ASP.NET Core in un punto successivo della pipeline.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-783">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="e6fd7-784">Non c'è alcun modo affidabile per scrivere i dati in una risposta quando si gestisce un'eccezione in un filtro dei risultati.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-784">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="e6fd7-785">Se le intestazioni sono state scaricate nel client quando il risultato di un'azione genera un'eccezione, non c'è alcun meccanismo affidabile per inviare un codice di errore.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-785">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="e6fd7-786">Per un oggetto <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, una chiamata a `await next` in <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> esegue tutti i filtri dei risultati successivi e il risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-786">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="e6fd7-787">Per causare il corto circuito, impostare [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) su `true` e non chiamare `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-787">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="e6fd7-788">Il framework fornisce un oggetto `ResultFilterAttribute` astratto che è possibile impostare come sottoclasse.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-788">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="e6fd7-789">La classe [AddHeaderAttribute](#add-header-attribute) illustrata in precedenza è un esempio di un attributo di filtro dei risultati.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-789">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="e6fd7-790">IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="e6fd7-790">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="e6fd7-791">Le interfacce <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> dichiarano un'implementazione di <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> eseguita per tutti i risultati dell'azione.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-791">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="e6fd7-792">Sono inclusi i risultati dell'azione prodotti da:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-792">This includes action results produced by:</span></span>

* <span data-ttu-id="e6fd7-793">Filtri di autorizzazione e filtri delle risorse che interessano il cortocircuito.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-793">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="e6fd7-794">Filtri eccezioni.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-794">Exception filters.</span></span>

<span data-ttu-id="e6fd7-795">Ad esempio, il filtro seguente viene eseguito sempre e imposta un risultato dell'azione (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) con un codice di stato *422 Entità non elaborabile* quando la negoziazione del contenuto ha esito negativo:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-795">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="e6fd7-796">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="e6fd7-796">IFilterFactory</span></span>

<span data-ttu-id="e6fd7-797"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-797"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="e6fd7-798">Pertanto, un'istanza di `IFilterFactory` può essere usata come un'istanza di `IFilterMetadata` in un punto qualsiasi della pipeline filtro.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-798">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="e6fd7-799">Quando il runtime si prepara per richiamare il filtro, cerca di eseguirne il cast a un oggetto `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-799">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="e6fd7-800">Se l'esecuzione del cast ha esito positivo, viene chiamato il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> per creare l'istanza di `IFilterMetadata` richiamata.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-800">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="e6fd7-801">In questo modo viene specificata una struttura flessibile, poiché non è necessario impostare in modo esplicito la pipeline filtro all'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-801">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="e6fd7-802">Un altro approccio alla creazione di filtri consiste nell'implementare `IFilterFactory` usando implementazioni dell'attributo personalizzate:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-802">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="e6fd7-803">Il codice precedente può essere testato eseguendo l'[esempio scaricato](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="e6fd7-803">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="e6fd7-804">Richiamare gli strumenti di sviluppo F12.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-804">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="e6fd7-805">Accedere a `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-805">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="e6fd7-806">Gli strumenti di sviluppo F12 visualizzano le intestazioni di risposta seguenti aggiunte dal codice di esempio:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-806">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="e6fd7-807">**Autore:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="e6fd7-807">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="e6fd7-808">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="e6fd7-808">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="e6fd7-809">**interno:**`My header`</span><span class="sxs-lookup"><span data-stu-id="e6fd7-809">**internal:** `My header`</span></span>

<span data-ttu-id="e6fd7-810">Il codice precedente crea l'intestazione di risposta **internal:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-810">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="e6fd7-811">Implementazione di IFilterFactory in un attributo</span><span class="sxs-lookup"><span data-stu-id="e6fd7-811">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="e6fd7-812">I filtri che implementano `IFilterFactory` sono utili per i filtri che:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-812">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="e6fd7-813">Non richiedono il passaggio di parametri.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-813">Don't require passing parameters.</span></span>
* <span data-ttu-id="e6fd7-814">Hanno dipendenze del costruttore che devono essere soddisfatte dall'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-814">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="e6fd7-815"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-815"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="e6fd7-816">`IFilterFactory` espone il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> per creare un'istanza <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-816">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="e6fd7-817">`CreateInstance` carica il tipo specificato dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-817">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="e6fd7-818">Il codice seguente illustra tre approcci all'applicazione di `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-818">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="e6fd7-819">Nel codice precedente la decorazione del metodo con `[SampleActionFilter]` è l'approccio da preferire per applicare `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-819">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="e6fd7-820">Uso di middleware nella pipeline filtro</span><span class="sxs-lookup"><span data-stu-id="e6fd7-820">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="e6fd7-821">I filtri risorse funzionano come [middleware](xref:fundamentals/middleware/index) in quanto racchiudono l'esecuzione di tutto ciò che viene dopo nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-821">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="e6fd7-822">I filtri sono diversi dal middleware in quanto fanno parte del runtime di ASP.NET Core, quindi hanno accesso al contesto e ai costrutti di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-822">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="e6fd7-823">Per usare il middleware come filtro, creare un tipo con un metodo `Configure` che specifica il middleware da inserire nella pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-823">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="e6fd7-824">L'esempio seguente usa il middleware di localizzazione per stabilire le impostazioni cultura correnti per una richiesta:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-824">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="e6fd7-825">Usare <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> per eseguire il middleware:</span><span class="sxs-lookup"><span data-stu-id="e6fd7-825">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="e6fd7-826">I filtri middleware vengono eseguiti nella stessa fase della pipeline filtro come filtri risorse, prima dell'associazione di modelli e dopo il resto della pipeline.</span><span class="sxs-lookup"><span data-stu-id="e6fd7-826">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="e6fd7-827">Azioni successive</span><span class="sxs-lookup"><span data-stu-id="e6fd7-827">Next actions</span></span>

* <span data-ttu-id="e6fd7-828">Vedere [metodi di filtro per le Razor pagine](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-828">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="e6fd7-829">Per sperimentare i filtri, [scaricare, testare e modificare l'esempio di GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="e6fd7-829">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
