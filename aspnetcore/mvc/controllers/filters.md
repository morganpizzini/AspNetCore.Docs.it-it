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
ms.openlocfilehash: 7272e05b408ac6f8daeda586c6f40fcc5bd1f6eb
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776786"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="dd6a5-103">Filtri in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dd6a5-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dd6a5-104">Di [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="dd6a5-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="dd6a5-105">I *filtri* in ASP.NET Core consentono l'esecuzione del codice prima o dopo fasi specifiche della pipeline di elaborazione della richiesta.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="dd6a5-106">I filtri predefiniti gestiscono attività, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="dd6a5-107">Autorizzazione (impedire l'accesso alle risorse per cui un utente non è autorizzato).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="dd6a5-108">Memorizzazione nella cache delle risposte (blocco della pipeline delle richieste per restituire una risposta memorizzata nella cache).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="dd6a5-109">I filtri personalizzati possono essere creati per gestire problemi relativi a più settori.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="dd6a5-110">Esempi di problematiche trasversali includono la gestione degli errori, la memorizzazione nella cache, la configurazione, l'autorizzazione e la registrazione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="dd6a5-111">I filtri evitano la duplicazione del codice.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="dd6a5-112">Ad esempio, un filtro eccezioni per la gestione degli errori potrebbe consolidare la gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="dd6a5-113">Questo documento si applica a Razor Pages, controller API e controller con visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="dd6a5-114">I filtri non funzionano direttamente con i [componenti Razor](xref:blazor/components).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-114">Filters don't work directly with [Razor components](xref:blazor/components).</span></span> <span data-ttu-id="dd6a5-115">Un filtro può influire indirettamente su un componente solo quando:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="dd6a5-116">Il componente è incorporato in una pagina o in una vista.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="dd6a5-117">La pagina o il controller/Visualizzazione usa il filtro.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="dd6a5-118">[Visualizzare o scaricare un esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="dd6a5-119">Funzionamento dei filtri</span><span class="sxs-lookup"><span data-stu-id="dd6a5-119">How filters work</span></span>

<span data-ttu-id="dd6a5-120">I filtri vengono eseguiti all'interno della *pipeline di chiamata di azioni ASP.NET Core*, talvolta definita *pipeline di filtro*.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="dd6a5-121">La pipeline di filtro viene eseguita dopo che ASP.NET Core ha selezionato l'azione da eseguire.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![La richiesta viene elaborata tramite altri middleware, middleware di routing, selezione dell'azione e pipeline di chiamata dell'azione.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="dd6a5-124">Tipi di filtro</span><span class="sxs-lookup"><span data-stu-id="dd6a5-124">Filter types</span></span>

<span data-ttu-id="dd6a5-125">Ogni tipo di filtro viene eseguito in una fase diversa della pipeline di filtro:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="dd6a5-126">I [filtri di autorizzazione](#authorization-filters) vengono eseguiti per primi e consentono di determinare se l'utente è autorizzato per la richiesta.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="dd6a5-127">I filtri di autorizzazione consentono di cortocircuitare la pipeline se la richiesta non è autorizzata.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="dd6a5-128">[Filtri risorse](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="dd6a5-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="dd6a5-129">Vengono eseguiti dopo l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-129">Run after authorization.</span></span>  
  * <span data-ttu-id="dd6a5-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>esegue il codice prima del resto della pipeline filtro.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="dd6a5-131">Ad esempio, `OnResourceExecuting` esegue il codice prima dell'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="dd6a5-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>esegue il codice una volta completato il resto della pipeline.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="dd6a5-133">[Filtri azione](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="dd6a5-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="dd6a5-134">Eseguire il codice immediatamente prima e dopo la chiamata di un metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="dd6a5-135">Consente di modificare gli argomenti passati in un'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="dd6a5-136">Può modificare il risultato restituito dall'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="dd6a5-137">**Non** sono supportate in Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="dd6a5-138">I [filtri eccezioni](#exception-filters) applicano i criteri globali alle eccezioni non gestite che si verificano prima della scrittura del corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="dd6a5-139">I [filtri risultati](#result-filters) eseguono il codice immediatamente prima e dopo l'esecuzione dei risultati dell'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="dd6a5-140">Vengono eseguiti solo quando il metodo di azione è stato eseguito correttamente.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="dd6a5-141">Sono utili per la logica che deve racchiudere l'esecuzione del formattatore o di una vista.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="dd6a5-142">Il diagramma seguente illustra come interagiscono i tipi di filtro nella pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![La richiesta passa attraverso i filtri autorizzazione, i filtri risorse, l'associazione di modelli, i filtri azione, l'esecuzione dell'azione e la conversione del risultato dell'azione, i filtri eccezioni, i filtri risultato e l'esecuzione del risultato.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="dd6a5-145">Implementazione</span><span class="sxs-lookup"><span data-stu-id="dd6a5-145">Implementation</span></span>

<span data-ttu-id="dd6a5-146">I filtri supportano sia implementazioni sincrone che asincrone tramite definizioni di interfaccia diverse.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="dd6a5-147">I filtri sincroni eseguono codice prima e dopo la fase della pipeline.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="dd6a5-148">La chiamata di <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*>, ad esempio, avviene prima della chiamata del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="dd6a5-149">La chiamata di <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> avviene dopo l'esecuzione del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="dd6a5-150">I filtri asincroni definiscono `On-Stage-ExecutionAsync` un metodo.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="dd6a5-151">Ad esempio, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="dd6a5-152">Nel codice precedente, `SampleAsyncActionFilter` ha un <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) che esegue il metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="dd6a5-153">Fasi di filtro multiple</span><span class="sxs-lookup"><span data-stu-id="dd6a5-153">Multiple filter stages</span></span>

<span data-ttu-id="dd6a5-154">È possibile implementare interfacce per più fasi di filtro in una singola classe.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="dd6a5-155">Ad esempio, la <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> classe implementa:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="dd6a5-156">Sincrona <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> : e<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="dd6a5-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="dd6a5-157">Asincrono: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> e<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="dd6a5-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="dd6a5-158">Implementare la versione sincrona **oppure** la versione asincrona di un'interfaccia di filtro, **non** entrambe.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="dd6a5-159">Il runtime controlla per prima cosa se il filtro implementa l'interfaccia asincrona e, in tal caso, la chiama.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="dd6a5-160">In caso contrario, chiama i metodi dell'interfaccia sincrona.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="dd6a5-161">Se in una classe sono implementate entrambe le interfacce, sincrona e asincrona, viene chiamato solo il metodo asincrono.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="dd6a5-162">Quando si usano classi astratte come <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, eseguire l'override solo dei metodi sincroni o del metodo asincrono per ogni tipo di filtro.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="dd6a5-163">Attributi filtro predefiniti</span><span class="sxs-lookup"><span data-stu-id="dd6a5-163">Built-in filter attributes</span></span>

<span data-ttu-id="dd6a5-164">ASP.NET Core include filtri predefiniti basati su attributi che è possibile impostare come sottoclasse e personalizzare.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="dd6a5-165">Il filtro dei risultati seguente, ad esempio, aggiunge un'intestazione alla risposta:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="dd6a5-166">Gli attributi consentono ai filtri di accettare argomenti, come illustrato nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="dd6a5-167">Applicare `AddHeaderAttribute` a un controller o a un metodo di azione e specificare il nome e il valore dell'intestazione HTTP:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="dd6a5-168">Usare uno strumento come gli [strumenti di sviluppo del browser](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) per esaminare le intestazioni.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="dd6a5-169">In **intestazioni risposta** `author: Rick Anderson` viene visualizzato.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="dd6a5-170">Il codice seguente implementa un `ActionFilterAttribute` oggetto che:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="dd6a5-171">Legge il titolo e il nome dal sistema di configurazione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="dd6a5-172">A differenza dell'esempio precedente, il codice seguente non richiede l'aggiunta di parametri di filtro al codice.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="dd6a5-173">Aggiunge il titolo e il nome all'intestazione della risposta.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="dd6a5-174">Le opzioni di configurazione vengono fornite dal [sistema di configurazione](xref:fundamentals/configuration/index) usando il [modello di opzioni](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="dd6a5-175">Ad esempio, dal file *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="dd6a5-175">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="dd6a5-176">Nel `StartUp.ConfigureServices` eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="dd6a5-177">La `PositionOptions` classe viene aggiunta al contenitore del servizio con l' `"Position"` area di configurazione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="dd6a5-178">`MyActionFilterAttribute` Viene aggiunto al contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="dd6a5-179">Il codice seguente illustra la `PositionOptions` classe:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="dd6a5-180">Il codice seguente applica `MyActionFilterAttribute` al `Index2` metodo:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="dd6a5-181">In **intestazioni**di risposta `author: Rick Anderson`, e `Editor: Joe Smith` viene visualizzato quando viene `Sample/Index2` chiamato l'endpoint.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="dd6a5-182">Il codice seguente applica `MyActionFilterAttribute` e `AddHeaderAttribute` alla pagina Razor:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="dd6a5-183">Non è possibile applicare i filtri ai metodi del gestore di pagine Razor.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="dd6a5-184">Possono essere applicati al modello di pagina Razor o a livello globale.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="dd6a5-185">Molte delle interfacce del filtro presentano attributi corrispondenti che possono essere usati come classi di base per implementazioni personalizzate.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="dd6a5-186">Attributi dei filtri:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="dd6a5-187">Ambiti dei filtri e ordine di esecuzione</span><span class="sxs-lookup"><span data-stu-id="dd6a5-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="dd6a5-188">È possibile aggiungere un filtro alla pipeline in uno dei tre *ambiti*:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="dd6a5-189">Uso di un attributo in un'azione del controller.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="dd6a5-190">Non è possibile applicare attributi di filtro ai metodi del gestore Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="dd6a5-191">Uso di un attributo in un controller o in una pagina Razor.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="dd6a5-192">A livello globale per tutti i controller, le azioni e Razor Pages come illustrato nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="dd6a5-193">Ordine di esecuzione predefinito</span><span class="sxs-lookup"><span data-stu-id="dd6a5-193">Default order of execution</span></span>

<span data-ttu-id="dd6a5-194">Quando sono presenti più filtri per una particolare fase della pipeline, l'ambito determina l'ordine di esecuzione predefinito dei filtri.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="dd6a5-195">I filtri globali racchiudono i filtri di classe, che a loro volta racchiudono i filtri dei metodi.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="dd6a5-196">Come risultato dell'annidamento dei filtri, il codice *after* dei filtri viene eseguito in ordine inverso rispetto al codice *before*.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="dd6a5-197">Sequenza di filtro:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-197">The filter sequence:</span></span>

* <span data-ttu-id="dd6a5-198">Codice *before* dei filtri globali.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="dd6a5-199">Il codice *before* dei filtri della pagina controller e Razor.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="dd6a5-200">Codice *before* dei filtri del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="dd6a5-201">Codice *after* dei filtri del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="dd6a5-202">Il codice *after* dei filtri della pagina controller e Razor.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="dd6a5-203">Codice *after* dei filtri globali.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="dd6a5-204">L'esempio seguente illustra l'ordine in cui i metodi dei filtri vengono chiamati per i filtri di azione sincroni.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="dd6a5-205">Sequenza</span><span class="sxs-lookup"><span data-stu-id="dd6a5-205">Sequence</span></span> | <span data-ttu-id="dd6a5-206">Ambito del filtro</span><span class="sxs-lookup"><span data-stu-id="dd6a5-206">Filter scope</span></span> | <span data-ttu-id="dd6a5-207">Metodo del filtro</span><span class="sxs-lookup"><span data-stu-id="dd6a5-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="dd6a5-208">1</span><span class="sxs-lookup"><span data-stu-id="dd6a5-208">1</span></span> | <span data-ttu-id="dd6a5-209">Globale</span><span class="sxs-lookup"><span data-stu-id="dd6a5-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="dd6a5-210">2</span><span class="sxs-lookup"><span data-stu-id="dd6a5-210">2</span></span> | <span data-ttu-id="dd6a5-211">Controller o pagina Razor</span><span class="sxs-lookup"><span data-stu-id="dd6a5-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="dd6a5-212">3</span><span class="sxs-lookup"><span data-stu-id="dd6a5-212">3</span></span> | <span data-ttu-id="dd6a5-213">Metodo</span><span class="sxs-lookup"><span data-stu-id="dd6a5-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="dd6a5-214">4</span><span class="sxs-lookup"><span data-stu-id="dd6a5-214">4</span></span> | <span data-ttu-id="dd6a5-215">Metodo</span><span class="sxs-lookup"><span data-stu-id="dd6a5-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="dd6a5-216">5</span><span class="sxs-lookup"><span data-stu-id="dd6a5-216">5</span></span> | <span data-ttu-id="dd6a5-217">Controller o pagina Razor</span><span class="sxs-lookup"><span data-stu-id="dd6a5-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="dd6a5-218">6</span><span class="sxs-lookup"><span data-stu-id="dd6a5-218">6</span></span> | <span data-ttu-id="dd6a5-219">Globale</span><span class="sxs-lookup"><span data-stu-id="dd6a5-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="dd6a5-220">Filtri a livello di controller</span><span class="sxs-lookup"><span data-stu-id="dd6a5-220">Controller level filters</span></span>

<span data-ttu-id="dd6a5-221">Ogni controller che eredita dalla classe di base <xref:Microsoft.AspNetCore.Mvc.Controller> include i metodi [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) e [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="dd6a5-222">Questi metodi:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-222">These methods:</span></span>

* <span data-ttu-id="dd6a5-223">Eseguono il wrapping dei filtri che vengono eseguiti per una determinata azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="dd6a5-224">La chiamata di `OnActionExecuting` avviene prima di qualsiasi filtro dell'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="dd6a5-225">La chiamata di `OnActionExecuted` avviene dopo tutti i filtri dell'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="dd6a5-226">La chiamata di `OnActionExecutionAsync` avviene prima di qualsiasi filtro dell'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="dd6a5-227">Il codice del filtro dopo `next` viene eseguito dopo il metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="dd6a5-228">Ad esempio, l'applicazione di `MySampleActionFilter` nell'esempio scaricato avviene a livello globale all'avvio.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="dd6a5-229">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-229">The `TestController`:</span></span>

* <span data-ttu-id="dd6a5-230">Applica `SampleActionFilterAttribute` (`[SampleActionFilter]`) all' `FilterTest2` azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="dd6a5-231">Esegue l'override di `OnActionExecuting` e `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="dd6a5-232">Passando a `https://localhost:5001/Test2/FilterTest2`, viene eseguito il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="dd6a5-233">Filtri a livello di controller [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) impostare la proprietà `int.MinValue`Order su.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="dd6a5-234">I filtri a livello di controller **non** possono essere impostati per l'esecuzione dopo i filtri applicati ai metodi.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="dd6a5-235">L'ordine è illustrato nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-235">Order is explained in the next section.</span></span>

<span data-ttu-id="dd6a5-236">Per Razor Pages, vedere [Implementare i filtri di Razor Pages eseguendo l'override dei metodi di filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="dd6a5-237">Override dell'ordine predefinito</span><span class="sxs-lookup"><span data-stu-id="dd6a5-237">Overriding the default order</span></span>

<span data-ttu-id="dd6a5-238">È possibile eseguire l'override della sequenza di esecuzione predefinita implementando <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="dd6a5-239">`IOrderedFilter` espone la proprietà <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order>, che ha la precedenza sull'ambito per determinare l'ordine di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="dd6a5-240">Un filtro con un valore di `Order` inferiore:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="dd6a5-241">Esegue il codice *before* prima di quello di un filtro con un valore di `Order` più alto.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="dd6a5-242">Esegue il codice *after* dopo quello di un filtro con un valore di `Order` più alto.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="dd6a5-243">La `Order` proprietà viene impostata con un parametro del costruttore:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="dd6a5-244">Considerare i due filtri azione nel controller seguente:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="dd6a5-245">In `StartUp.ConfigureServices`è stato aggiunto un filtro globale:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="dd6a5-246">I 3 filtri vengono eseguiti nell'ordine seguente:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="dd6a5-247">La proprietà `Order` ha la precedenza sull'ambito nel determinare l'ordine di esecuzione dei filtri.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="dd6a5-248">I filtri vengono ordinati prima in base all'ordine, poi viene usato l'ambito per interrompere i collegamenti.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="dd6a5-249">Tutti i filtri predefiniti implementano `IOrderedFilter` e impostano il valore predefinito di `Order` su 0.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="dd6a5-250">Come indicato in precedenza, i filtri a livello di controller impostano la proprietà [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) su `int.MinValue` per i filtri incorporati `Order` , l'ambito determina l'ordine a meno che non sia impostato su un valore diverso da zero.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="dd6a5-251">Nel codice precedente, ha `MySampleActionFilter` ambito globale, quindi viene eseguito prima `MyAction2FilterAttribute`di, che ha ambito del controller.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="dd6a5-252">Per `MyAction2FilterAttribute` eseguire prima l'esecuzione, impostare l'ordine `int.MinValue`su:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="dd6a5-253">Per eseguire prima l'esecuzione `MySampleActionFilter` del filtro globale, `Order` impostare `int.MinValue`su:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="dd6a5-254">Annullamento e corto circuito</span><span class="sxs-lookup"><span data-stu-id="dd6a5-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="dd6a5-255">È possibile causare il corto circuito della pipeline di filtro impostando la proprietà <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> sul parametro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fornito al metodo di filtro.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="dd6a5-256">Ad esempio, il filtro di risorse seguente impedisce l'esecuzione del resto della pipeline:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="dd6a5-257">Nel codice seguente sia il filtro `ShortCircuitingResourceFilter` che il filtro `AddHeader` hanno come destinazione il metodo di azione `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="dd6a5-258">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="dd6a5-259">Viene eseguito per primo perché è un filtro risorsa e `AddHeader` è un filtro azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="dd6a5-260">Blocca il resto della pipeline.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="dd6a5-261">Pertanto il filtro `AddHeader` non viene mai eseguito per l'azione `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="dd6a5-262">Questo comportamento sarebbe lo stesso se entrambi i filtri venissero applicati a livello di metodo di azione, a condizione che il filtro `ShortCircuitingResourceFilter` venga eseguito prima.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="dd6a5-263">`ShortCircuitingResourceFilter` viene eseguito per primo per il tipo di filtro o per l'uso esplicito della proprietà `Order`.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="dd6a5-264">Inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="dd6a5-264">Dependency injection</span></span>

<span data-ttu-id="dd6a5-265">È possibile aggiungere filtri per tipo o per istanza.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="dd6a5-266">Se viene aggiunta un'istanza, tale istanza viene usata per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="dd6a5-267">Se viene aggiunto un tipo, il filtro è attivato dal tipo.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="dd6a5-268">Un filtro attivato dal tipo comporta:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-268">A type-activated filter means:</span></span>

* <span data-ttu-id="dd6a5-269">La creazione di un'istanza per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-269">An instance is created for each request.</span></span>
* <span data-ttu-id="dd6a5-270">Il popolamento di tutte le dipendenze del costruttore tramite [inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="dd6a5-271">I filtri implementati come attributi e aggiunti direttamente alle classi controller o ai metodi di azione non possono avere dipendenze costruttore specificate dall'[inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="dd6a5-272">Le dipendenze del costruttore non possono essere fornite tramite inserimento delle dipendenze in quanto:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="dd6a5-273">I parametri del costruttore devono essere forniti agli attributi nella posizione in cui vengono applicati.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="dd6a5-274">Questa è una limitazione del funzionamento degli attributi.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="dd6a5-275">I filtri seguenti supportano le dipendenze del costruttore fornite dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="dd6a5-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementato nell'attributo.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="dd6a5-277">I filtri precedenti possono essere applicati a un controller o a un metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="dd6a5-278">I logger sono disponibili tramite l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-278">Loggers are available from DI.</span></span> <span data-ttu-id="dd6a5-279">Evitare tuttavia di creare e usare filtri esclusivamente per scopi di registrazione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="dd6a5-280">La funzionalità di [registrazione del framework predefinita](xref:fundamentals/logging/index) in genere fornisce il necessario per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="dd6a5-281">La registrazione aggiunta ai filtri:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-281">Logging added to filters:</span></span>

* <span data-ttu-id="dd6a5-282">Deve concentrarsi su problemi del dominio di business o comportamenti specifici del filtro.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="dd6a5-283">**Non** deve registrare azioni o altri eventi del framework.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="dd6a5-284">Le azioni di log dei filtri incorporati e gli eventi del Framework.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="dd6a5-285">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="dd6a5-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="dd6a5-286">I tipi di implementazione del filtro di servizi sono registrati in `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="dd6a5-287"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera un'istanza del filtro dall'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="dd6a5-288">Il codice seguente illustra `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="dd6a5-289">Nel codice seguente l'oggetto `AddHeaderResultServiceFilter` viene aggiunto al contenitore di inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="dd6a5-290">Nel codice seguente l'attributo `ServiceFilter` recupera un'istanza del filtro `AddHeaderResultServiceFilter` dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="dd6a5-291">Quando si usa l'impostazione `ServiceFilterAttribute`, [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="dd6a5-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="dd6a5-292">Indica che l'istanza del filtro *può* essere riutilizzata all'esterno dell'ambito della richiesta in cui è stata creata.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="dd6a5-293">Il runtime di ASP.NET Core non garantisce:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="dd6a5-294">Che venga creata una singola istanza del filtro.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="dd6a5-295">Che il filtro non verrà richiesto di nuovo dal contenitore di inserimento delle dipendenze in un momento successivo.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="dd6a5-296">Non si deve usare con un filtro che dipende da servizi con una durata diversa da singleton.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="dd6a5-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="dd6a5-298">`IFilterFactory` espone il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> per creare un'istanza <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="dd6a5-299">`CreateInstance` carica il tipo specificato dall'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="dd6a5-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="dd6a5-300">TypeFilterAttribute</span></span>

<span data-ttu-id="dd6a5-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> è simile a <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, ma il relativo tipo non viene risolto direttamente dal contenitore dell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="dd6a5-302">Viene creata un'istanza del tipo tramite <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="dd6a5-303">Poiché i tipi `TypeFilterAttribute` non vengono risolti direttamente dal contenitore di inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="dd6a5-304">Non è necessario che i tipi a cui viene fatto riferimento tramite `TypeFilterAttribute` siano registrati nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="dd6a5-305">Le loro dipendenze vengono soddisfatte dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="dd6a5-306">In via facoltativa, `TypeFilterAttribute` può anche accettare gli argomenti del costruttore per il tipo.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="dd6a5-307">Quando si usa l'impostazione `TypeFilterAttribute`, [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="dd6a5-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="dd6a5-308">Indica che l'istanza del filtro *può* essere riutilizzata all'esterno dell'ambito della richiesta in cui è stata creata.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="dd6a5-309">Il runtime di ASP.NET Core non fornisce alcuna garanzia che venga creata una singola istanza del filtro.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="dd6a5-310">Non si deve usare con un filtro che dipende da servizi con una durata diversa da singleton.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="dd6a5-311">L'esempio seguente illustra come passare argomenti a un tipo usando `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="dd6a5-312">Filtri autorizzazione</span><span class="sxs-lookup"><span data-stu-id="dd6a5-312">Authorization filters</span></span>

<span data-ttu-id="dd6a5-313">I filtri di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-313">Authorization filters:</span></span>

* <span data-ttu-id="dd6a5-314">Sono i primi filtri a essere eseguiti nella pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="dd6a5-315">Controllano l'accesso ai metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-315">Control access to action methods.</span></span>
* <span data-ttu-id="dd6a5-316">Dispongono di un metodo precedente, ma non di un metodo successivo.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="dd6a5-317">I filtri di autorizzazione personalizzati richiedono un framework di autorizzazione personalizzato.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="dd6a5-318">È preferibile configurare criteri di autorizzazione o scrivere criteri di autorizzazione personalizzati piuttosto che scrivere un filtro personalizzato.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="dd6a5-319">Il filtro di autorizzazione predefinito:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="dd6a5-320">Chiama il sistema di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-320">Calls the authorization system.</span></span>
* <span data-ttu-id="dd6a5-321">Non autorizza le richieste.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-321">Does not authorize requests.</span></span>

<span data-ttu-id="dd6a5-322">**Non** generare eccezioni nei filtri di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="dd6a5-323">L'eccezione non verrà gestita.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-323">The exception will not be handled.</span></span>
* <span data-ttu-id="dd6a5-324">I filtri di eccezione non gestiranno l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="dd6a5-325">È consigliabile emettere una richiesta di verifica in caso di eccezione in un filtro di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="dd6a5-326">Altre informazioni sull'[autorizzazione](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="dd6a5-327">Filtri risorse</span><span class="sxs-lookup"><span data-stu-id="dd6a5-327">Resource filters</span></span>

<span data-ttu-id="dd6a5-328">I filtri di risorse:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-328">Resource filters:</span></span>

* <span data-ttu-id="dd6a5-329">Implementano l'interfaccia <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="dd6a5-330">L'esecuzione racchiude la maggior parte della pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="dd6a5-331">Solo i [filtri di autorizzazione](#authorization-filters) vengono eseguiti prima dei filtri di risorse.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="dd6a5-332">I filtri di risorse sono utili per causare il corto circuito della maggior parte della pipeline.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="dd6a5-333">Un filtro di memorizzazione nella cache può, ad esempio, evitare il resto della pipeline in caso di riscontro nella cache.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="dd6a5-334">Esempi di filtri di risorse:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-334">Resource filter examples:</span></span>

* <span data-ttu-id="dd6a5-335">Il [filtro di risorse di corto circuito](#short-circuiting-resource-filter) illustrato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="dd6a5-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="dd6a5-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="dd6a5-337">Impedisce all'associazione di modelli di accedere ai dati del modulo.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="dd6a5-338">Viene usato quando si caricano file di grandi dimensioni per impedire la lettura dei dati del modulo in memoria.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="dd6a5-339">Filtri azione</span><span class="sxs-lookup"><span data-stu-id="dd6a5-339">Action filters</span></span>

<span data-ttu-id="dd6a5-340">I filtri azione **non** si applicano a Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-340">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="dd6a5-341">Razor Pages supporta <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-341">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="dd6a5-342">Per altre informazioni, vedere [Modalità di filtro per pagine Razor](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="dd6a5-343">I filtri di azione:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-343">Action filters:</span></span>

* <span data-ttu-id="dd6a5-344">Implementano l'interfaccia <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="dd6a5-345">La loro esecuzione circonda l'esecuzione dei metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="dd6a5-346">Il codice seguente mostra un filtro di azione di esempio:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="dd6a5-347">La classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> specifica le proprietà seguenti:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="dd6a5-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>: consente di leggere gli input in un metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="dd6a5-349"><xref:Microsoft.AspNetCore.Mvc.Controller>: consente di modificare l'istanza del controller.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="dd6a5-350"><xref:System.Web.Mvc.ActionExecutingContext.Result>: l'impostazione di `Result` causa il corto circuito del metodo di azione e dei filtri di azione successivi.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="dd6a5-351">La generazione di un'eccezione in un metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="dd6a5-352">Impedisce l'esecuzione dei filtri successivi.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="dd6a5-353">A differenza dell'impostazione di `Result`, è considerata un errore anziché un risultato positivo.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="dd6a5-354">La classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> specifica `Controller` e `Result` oltre alle proprietà seguenti:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="dd6a5-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>: true se un altro file ha causato il corto circuito dell'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="dd6a5-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception>: non Null se l'azione o un filtro di azione eseguito in precedenza ha generato un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="dd6a5-357">Se si imposta questa proprietà su Null:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-357">Setting this property to null:</span></span>

  * <span data-ttu-id="dd6a5-358">L'eccezione viene gestita in modo efficace.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="dd6a5-359">L'oggetto `Result` viene eseguito come se fosse stato restituito dal metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="dd6a5-360">Per un oggetto `IAsyncActionFilter`, una chiamata a <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="dd6a5-361">Esegue qualsiasi filtro azione successivo e il metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="dd6a5-362">Restituisce `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="dd6a5-363">Per causare il corto circuito, assegnare <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a un'istanza del risultato e non chiamare `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="dd6a5-364">Il framework fornisce un oggetto <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> astratto che è possibile impostare come sottoclasse.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="dd6a5-365">Il filtro di azione `OnActionExecuting` può essere usato per:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="dd6a5-366">Convalidare lo stato del modello.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-366">Validate model state.</span></span>
* <span data-ttu-id="dd6a5-367">Restituire un errore se lo stato non è valido.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="dd6a5-368">Il metodo `OnActionExecuted` viene eseguito dopo il metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-368">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="dd6a5-369">È possibile visualizzare e modificare i risultati dell'azione tramite la proprietà <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-369">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="dd6a5-370">L'impostazione di <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> è true se un altro filtro ha causato il corto circuito dell'esecuzione dell'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="dd6a5-371">L'impostazione di <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> è un valore non Null se l'azione o un filtro di azione successivo ha generato un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-371"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="dd6a5-372">Impostazione di `Exception` su null:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-372">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="dd6a5-373">Gestisce un'eccezione in modo efficace.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-373">Effectively handles an exception.</span></span>
  * <span data-ttu-id="dd6a5-374">L'oggetto `ActionExecutedContext.Result` viene eseguito come se fosse stato restituito normalmente dal metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-374">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="dd6a5-375">Filtri eccezioni</span><span class="sxs-lookup"><span data-stu-id="dd6a5-375">Exception filters</span></span>

<span data-ttu-id="dd6a5-376">Filtri eccezioni:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-376">Exception filters:</span></span>

* <span data-ttu-id="dd6a5-377">Implementano <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-377">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="dd6a5-378">Possono essere usati per implementare criteri di gestione degli errori comuni.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-378">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="dd6a5-379">Il filtro di eccezione di esempio seguente usa una visualizzazione degli errori personalizzata per mostrare i dettagli sulle eccezioni che si verificano quando l'app è in fase di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-379">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="dd6a5-380">Il codice seguente verifica il filtro eccezioni:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-380">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="dd6a5-381">Filtri eccezioni:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-381">Exception filters:</span></span>

* <span data-ttu-id="dd6a5-382">Non dispone di eventi precedenti o successivi.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-382">Don't have before and after events.</span></span>
* <span data-ttu-id="dd6a5-383">Implementano <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-383">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="dd6a5-384">Gestiscono le eccezioni non gestite che si verificano nella creazione di un controller o una pagina Razor, nell'[associazione di modelli](xref:mvc/models/model-binding), nei filtri di azione o nei metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-384">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="dd6a5-385">**Non** rilevano le eccezioni che si verificano nei filtri di risorse, nei filtri dei risultati o nell'esecuzione dei risultati MVC.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-385">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="dd6a5-386">Per gestire un'eccezione, impostare la proprietà <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> su `true` o scrivere una risposta.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-386">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="dd6a5-387">In questo modo si arresta la propagazione dell'eccezione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-387">This stops propagation of the exception.</span></span> <span data-ttu-id="dd6a5-388">Un filtro di eccezione non può modificare un'eccezione in una "operazione riuscita".</span><span class="sxs-lookup"><span data-stu-id="dd6a5-388">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="dd6a5-389">Questa operazione può essere eseguita solo tramite un filtro di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-389">Only an action filter can do that.</span></span>

<span data-ttu-id="dd6a5-390">Filtri eccezioni:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-390">Exception filters:</span></span>

* <span data-ttu-id="dd6a5-391">Sono ideali per intercettare le eccezioni che si verificano nelle azioni.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-391">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="dd6a5-392">Non sono flessibili quanto il middleware di gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-392">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="dd6a5-393">Scegliere il middleware per la gestione delle eccezioni.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-393">Prefer middleware for exception handling.</span></span> <span data-ttu-id="dd6a5-394">Usare i filtri di eccezione solo quando la gestione degli errori *varia* in base al metodo di azione chiamato.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-394">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="dd6a5-395">Un'app può ad esempio avere metodi di azione sia per gli endpoint API che per le visualizzazioni/HTML.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-395">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="dd6a5-396">Gli endpoint dell'API possono restituire informazioni sull'errore come JSON, mentre le azioni basate sulla visualizzazione possono restituire una pagina di errore in formato HTML.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-396">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="dd6a5-397">Filtri risultato</span><span class="sxs-lookup"><span data-stu-id="dd6a5-397">Result filters</span></span>

<span data-ttu-id="dd6a5-398">I filtri dei risultati:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-398">Result filters:</span></span>

* <span data-ttu-id="dd6a5-399">Implementare un'interfaccia:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-399">Implement an interface:</span></span>
  * <span data-ttu-id="dd6a5-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="dd6a5-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="dd6a5-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="dd6a5-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="dd6a5-402">La loro esecuzione circonda l'esecuzione dei risultati dell'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-402">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="dd6a5-403">IResultFilter e IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="dd6a5-403">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="dd6a5-404">Il codice seguente illustra un filtro dei risultati che aggiunge un'intestazione HTTP:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-404">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="dd6a5-405">Il tipo di risultato eseguito dipende dall'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-405">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="dd6a5-406">Un'azione che restituisce una visualizzazione include tutte le operazioni di elaborazione Razor <xref:Microsoft.AspNetCore.Mvc.ViewResult> come parte dell'oggetto in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-406">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="dd6a5-407">Un metodo API può eseguire la serializzazione in quanto parte dell'esecuzione del risultato.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-407">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="dd6a5-408">Altre informazioni sui [risultati dell'azione](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-408">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="dd6a5-409">I filtri dei risultati vengono eseguiti solo quando un filtro azione o azione produce un risultato di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-409">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="dd6a5-410">I filtri dei risultati non vengono eseguiti nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-410">Result filters are not executed when:</span></span>

* <span data-ttu-id="dd6a5-411">Un filtro di autorizzazione o un filtro risorse cortocircui la pipeline.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-411">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="dd6a5-412">Un filtro di eccezione non gestisca un'eccezione producendo un risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-412">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="dd6a5-413">Il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> può causare il corto circuito dell'esecuzione del risultato dell'azione e dei filtri dei risultati successivi se si imposta <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> su `true`.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-413">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="dd6a5-414">In caso di corto circuito, scrivere nell'oggetto risposta per evitare di generare una risposta vuota.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-414">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="dd6a5-415">Generazione di un'eccezione `IResultFilter.OnResultExecuting`in:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-415">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="dd6a5-416">Impedisce l'esecuzione del risultato dell'azione e dei filtri successivi.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-416">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="dd6a5-417">Viene considerato un errore anziché un risultato positivo.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-417">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="dd6a5-418">Quando viene <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> eseguito il metodo, è probabile che la risposta sia già stata inviata al client.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-418">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="dd6a5-419">Se la risposta è già stata inviata al client, non è possibile modificarla.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-419">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="dd6a5-420">L'impostazione di `ResultExecutedContext.Canceled` è `true` se un altro filtro ha causato il corto circuito dell'esecuzione del risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-420">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="dd6a5-421">L'impostazione di `ResultExecutedContext.Exception` è un valore non Null se il risultato dell'azione o un filtro dei risultati successivo ha generato un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-421">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="dd6a5-422">L' `Exception` impostazione di su null consente di gestire in modo efficace un'eccezione e impedisce che l'eccezione venga generata nuovamente in un secondo momento nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-422">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="dd6a5-423">Non c'è alcun modo affidabile per scrivere i dati in una risposta quando si gestisce un'eccezione in un filtro dei risultati.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-423">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="dd6a5-424">Se le intestazioni sono state scaricate nel client quando il risultato di un'azione genera un'eccezione, non c'è alcun meccanismo affidabile per inviare un codice di errore.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-424">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="dd6a5-425">Per un oggetto <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, una chiamata a `await next` in <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> esegue tutti i filtri dei risultati successivi e il risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-425">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="dd6a5-426">Per causare il corto circuito, impostare [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) su `true` e non chiamare `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-426">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="dd6a5-427">Il framework fornisce un oggetto `ResultFilterAttribute` astratto che è possibile impostare come sottoclasse.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-427">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="dd6a5-428">La classe [AddHeaderAttribute](#add-header-attribute) illustrata in precedenza è un esempio di un attributo di filtro dei risultati.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-428">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="dd6a5-429">IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="dd6a5-429">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="dd6a5-430">Le interfacce <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> dichiarano un'implementazione di <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> eseguita per tutti i risultati dell'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-430">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="dd6a5-431">Sono inclusi i risultati dell'azione prodotti da:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-431">This includes action results produced by:</span></span>

* <span data-ttu-id="dd6a5-432">Filtri di autorizzazione e filtri delle risorse che interessano il cortocircuito.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-432">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="dd6a5-433">Filtri eccezioni.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-433">Exception filters.</span></span>

<span data-ttu-id="dd6a5-434">Ad esempio, il filtro seguente viene eseguito sempre e imposta un risultato dell'azione (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) con un codice di stato *422 Entità non elaborabile* quando la negoziazione del contenuto ha esito negativo:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-434">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="dd6a5-435">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="dd6a5-435">IFilterFactory</span></span>

<span data-ttu-id="dd6a5-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="dd6a5-437">Pertanto, un'istanza di `IFilterFactory` può essere usata come un'istanza di `IFilterMetadata` in un punto qualsiasi della pipeline filtro.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-437">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="dd6a5-438">Quando il runtime si prepara per richiamare il filtro, cerca di eseguirne il cast a un oggetto `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-438">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="dd6a5-439">Se l'esecuzione del cast ha esito positivo, viene chiamato il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> per creare l'istanza di `IFilterMetadata` richiamata.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-439">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="dd6a5-440">In questo modo viene specificata una struttura flessibile, poiché non è necessario impostare in modo esplicito la pipeline filtro all'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-440">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="dd6a5-441">Un altro approccio alla creazione di filtri consiste nell'implementare `IFilterFactory` usando implementazioni dell'attributo personalizzate:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-441">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="dd6a5-442">Il filtro viene applicato nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-442">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="dd6a5-443">Testare il codice precedente eseguendo l' [esempio di download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="dd6a5-443">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="dd6a5-444">Richiamare gli strumenti di sviluppo F12.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-444">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="dd6a5-445">Accedere a `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-445">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="dd6a5-446">Gli strumenti di sviluppo F12 visualizzano le intestazioni di risposta seguenti aggiunte dal codice di esempio:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-446">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="dd6a5-447">**Autore:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="dd6a5-447">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="dd6a5-448">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="dd6a5-448">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="dd6a5-449">**interno:**`My header`</span><span class="sxs-lookup"><span data-stu-id="dd6a5-449">**internal:** `My header`</span></span>

<span data-ttu-id="dd6a5-450">Il codice precedente crea l'intestazione di risposta **internal:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-450">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="dd6a5-451">Implementazione di IFilterFactory in un attributo</span><span class="sxs-lookup"><span data-stu-id="dd6a5-451">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="dd6a5-452">I filtri che implementano `IFilterFactory` sono utili per i filtri che:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-452">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="dd6a5-453">Non richiedono il passaggio di parametri.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-453">Don't require passing parameters.</span></span>
* <span data-ttu-id="dd6a5-454">Hanno dipendenze del costruttore che devono essere soddisfatte dall'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-454">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="dd6a5-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="dd6a5-456">`IFilterFactory` espone il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> per creare un'istanza <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-456">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="dd6a5-457">`CreateInstance` carica il tipo specificato dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-457">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="dd6a5-458">Il codice seguente illustra tre approcci all'applicazione di `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-458">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="dd6a5-459">Nel codice precedente la decorazione del metodo con `[SampleActionFilter]` è l'approccio da preferire per applicare `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-459">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="dd6a5-460">Uso di middleware nella pipeline filtro</span><span class="sxs-lookup"><span data-stu-id="dd6a5-460">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="dd6a5-461">I filtri risorse funzionano come [middleware](xref:fundamentals/middleware/index) in quanto racchiudono l'esecuzione di tutto ciò che viene dopo nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-461">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="dd6a5-462">Tuttavia, i filtri differiscono dal middleware in quanto fanno parte del runtime, il che significa che hanno accesso a contesto e costrutti.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-462">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="dd6a5-463">Per usare il middleware come filtro, creare un tipo con un metodo `Configure` che specifica il middleware da inserire nella pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-463">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="dd6a5-464">L'esempio seguente usa il middleware di localizzazione per stabilire le impostazioni cultura correnti per una richiesta:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-464">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="dd6a5-465">Usare <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> per eseguire il middleware:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-465">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="dd6a5-466">I filtri middleware vengono eseguiti nella stessa fase della pipeline filtro come filtri risorse, prima dell'associazione di modelli e dopo il resto della pipeline.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-466">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="dd6a5-467">Azioni successive</span><span class="sxs-lookup"><span data-stu-id="dd6a5-467">Next actions</span></span>

* <span data-ttu-id="dd6a5-468">Vedere [metodi di filtro per Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-468">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="dd6a5-469">Per sperimentare i filtri, [scaricare, testare e modificare l'esempio di GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-469">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dd6a5-470">Di [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="dd6a5-470">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="dd6a5-471">I *filtri* in ASP.NET Core consentono l'esecuzione del codice prima o dopo fasi specifiche della pipeline di elaborazione della richiesta.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-471">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="dd6a5-472">I filtri predefiniti gestiscono attività, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-472">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="dd6a5-473">Autorizzazione (impedire l'accesso alle risorse per cui un utente non è autorizzato).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-473">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="dd6a5-474">Memorizzazione nella cache delle risposte (blocco della pipeline delle richieste per restituire una risposta memorizzata nella cache).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-474">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="dd6a5-475">I filtri personalizzati possono essere creati per gestire problemi relativi a più settori.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-475">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="dd6a5-476">Esempi di problematiche trasversali includono la gestione degli errori, la memorizzazione nella cache, la configurazione, l'autorizzazione e la registrazione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-476">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="dd6a5-477">I filtri evitano la duplicazione del codice.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-477">Filters avoid duplicating code.</span></span> <span data-ttu-id="dd6a5-478">Ad esempio, un filtro eccezioni per la gestione degli errori potrebbe consolidare la gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-478">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="dd6a5-479">Questo documento si applica a Razor Pages, controller API e controller con visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-479">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="dd6a5-480">[Visualizzare o scaricare un esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-480">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="dd6a5-481">Funzionamento dei filtri</span><span class="sxs-lookup"><span data-stu-id="dd6a5-481">How filters work</span></span>

<span data-ttu-id="dd6a5-482">I filtri vengono eseguiti all'interno della *pipeline di chiamata di azioni ASP.NET Core*, talvolta definita *pipeline di filtro*.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-482">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="dd6a5-483">La pipeline di filtro viene eseguita dopo che ASP.NET Core ha selezionato l'azione da eseguire.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-483">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![La richiesta viene elaborata tramite altro middleware, il middleware di routing, la selezione dell'azione e la pipeline di chiamata di azioni ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="dd6a5-486">Tipi di filtro</span><span class="sxs-lookup"><span data-stu-id="dd6a5-486">Filter types</span></span>

<span data-ttu-id="dd6a5-487">Ogni tipo di filtro viene eseguito in una fase diversa della pipeline di filtro:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-487">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="dd6a5-488">I [filtri di autorizzazione](#authorization-filters) vengono eseguiti per primi e consentono di determinare se l'utente è autorizzato per la richiesta.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-488">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="dd6a5-489">I filtri di autorizzazione causano il corto circuito della pipeline se la richiesta non è autorizzata.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-489">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="dd6a5-490">[Filtri risorse](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="dd6a5-490">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="dd6a5-491">Vengono eseguiti dopo l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-491">Run after authorization.</span></span>  
  * <span data-ttu-id="dd6a5-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> può eseguire codice prima del resto della pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="dd6a5-493">Ad esempio, `OnResourceExecuting` può eseguire codice prima dell'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-493">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="dd6a5-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> può eseguire codice dopo che il resto della pipeline è stato completato.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="dd6a5-495">I [filtri azione](#action-filters) possono eseguire codice immediatamente prima e dopo la chiamata di un metodo di azione singolo.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-495">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="dd6a5-496">Possono essere usati per modificare gli argomenti passati a un'azione e il risultato restituito dall'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-496">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="dd6a5-497">I filtri di azione **non** sono supportati in Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-497">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="dd6a5-498">I [filtri eccezioni](#exception-filters) vengono usati per applicare i criteri globali a eccezioni non gestite che si verificano prima che qualsiasi elemento sia stato scritto nel corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-498">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="dd6a5-499">I [filtri risultato](#result-filters) possono eseguire codice immediatamente prima e dopo l'esecuzione di risultati di azioni singole.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-499">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="dd6a5-500">Vengono eseguiti solo quando il metodo di azione è stato eseguito correttamente.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-500">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="dd6a5-501">Sono utili per la logica che deve racchiudere l'esecuzione del formattatore o di una vista.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-501">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="dd6a5-502">Il diagramma seguente illustra come interagiscono i tipi di filtro nella pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-502">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![La richiesta passa attraverso i filtri autorizzazione, i filtri risorse, l'associazione di modelli, i filtri azione, l'esecuzione dell'azione e la conversione del risultato dell'azione, i filtri eccezioni, i filtri risultato e l'esecuzione del risultato.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="dd6a5-505">Implementazione</span><span class="sxs-lookup"><span data-stu-id="dd6a5-505">Implementation</span></span>

<span data-ttu-id="dd6a5-506">I filtri supportano sia implementazioni sincrone che asincrone tramite definizioni di interfaccia diverse.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-506">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="dd6a5-507">I filtri sincroni possono eseguire codice prima (`On-Stage-Executing`) e dopo (`On-Stage-Executed`) la relativa fase della pipeline.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-507">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="dd6a5-508">La chiamata di `OnActionExecuting`, ad esempio, avviene prima della chiamata del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-508">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="dd6a5-509">La chiamata di `OnActionExecuted` avviene dopo l'esecuzione del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-509">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="dd6a5-510">I filtri asincroni definiscono un metodo `On-Stage-ExecutionAsync`:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-510">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="dd6a5-511">Nel codice precedente `SampleAsyncActionFilter` ha un oggetto <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) che esegue il metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-511">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="dd6a5-512">Ognuno dei metodi `On-Stage-ExecutionAsync` accetta un oggetto `FilterType-ExecutionDelegate` che esegue la fase della pipeline del filtro.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-512">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="dd6a5-513">Fasi di filtro multiple</span><span class="sxs-lookup"><span data-stu-id="dd6a5-513">Multiple filter stages</span></span>

<span data-ttu-id="dd6a5-514">È possibile implementare interfacce per più fasi di filtro in una singola classe.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-514">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="dd6a5-515">Ad esempio, la classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementa `IActionFilter`, `IResultFilter` e i relativi equivalenti asincroni.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-515">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="dd6a5-516">Implementare la versione sincrona **oppure** la versione asincrona di un'interfaccia di filtro, **non** entrambe.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-516">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="dd6a5-517">Il runtime controlla per prima cosa se il filtro implementa l'interfaccia asincrona e, in tal caso, la chiama.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-517">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="dd6a5-518">In caso contrario, chiama i metodi dell'interfaccia sincrona.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-518">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="dd6a5-519">Se in una classe sono implementate entrambe le interfacce, sincrona e asincrona, viene chiamato solo il metodo asincrono.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-519">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="dd6a5-520">Quando si usano classi astratte come <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> eseguire l'override solo dei metodi sincroni o del metodo asincrono per ogni tipo di filtro.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-520">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="dd6a5-521">Attributi filtro predefiniti</span><span class="sxs-lookup"><span data-stu-id="dd6a5-521">Built-in filter attributes</span></span>

<span data-ttu-id="dd6a5-522">ASP.NET Core include filtri predefiniti basati su attributi che è possibile impostare come sottoclasse e personalizzare.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-522">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="dd6a5-523">Il filtro dei risultati seguente, ad esempio, aggiunge un'intestazione alla risposta:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-523">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="dd6a5-524">Gli attributi consentono ai filtri di accettare argomenti, come illustrato nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-524">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="dd6a5-525">Applicare `AddHeaderAttribute` a un controller o a un metodo di azione e specificare il nome e il valore dell'intestazione HTTP:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-525">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="dd6a5-526">Molte delle interfacce del filtro presentano attributi corrispondenti che possono essere usati come classi di base per implementazioni personalizzate.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-526">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="dd6a5-527">Attributi dei filtri:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-527">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="dd6a5-528">Ambiti dei filtri e ordine di esecuzione</span><span class="sxs-lookup"><span data-stu-id="dd6a5-528">Filter scopes and order of execution</span></span>

<span data-ttu-id="dd6a5-529">È possibile aggiungere un filtro alla pipeline in uno dei tre *ambiti*:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-529">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="dd6a5-530">Usando un attributo di un'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-530">Using an attribute on an action.</span></span>
* <span data-ttu-id="dd6a5-531">Usando un attributo di un controller.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-531">Using an attribute on a controller.</span></span>
* <span data-ttu-id="dd6a5-532">A livello globale per tutti i controller e le azioni come illustrato nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-532">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="dd6a5-533">Il codice precedente aggiunge tre filtri a livello globale tramite la raccolta [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-533">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="dd6a5-534">Ordine di esecuzione predefinito</span><span class="sxs-lookup"><span data-stu-id="dd6a5-534">Default order of execution</span></span>

<span data-ttu-id="dd6a5-535">Quando sono presenti più filtri *dello stesso tipo*, scope determina l'ordine predefinito di esecuzione del filtro.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-535">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="dd6a5-536">Filtri globali circondano i filtri di classe.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-536">Global filters surround class filters.</span></span> <span data-ttu-id="dd6a5-537">Filtri di classe filtri di metodo Racchiudi.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-537">Class filters surround method filters.</span></span>

<span data-ttu-id="dd6a5-538">Come risultato dell'annidamento dei filtri, il codice *after* dei filtri viene eseguito in ordine inverso rispetto al codice *before*.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-538">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="dd6a5-539">Sequenza di filtro:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-539">The filter sequence:</span></span>

* <span data-ttu-id="dd6a5-540">Codice *before* dei filtri globali.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-540">The *before* code of global filters.</span></span>
  * <span data-ttu-id="dd6a5-541">Codice *before* dei filtri del controller.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-541">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="dd6a5-542">Codice *before* dei filtri del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-542">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="dd6a5-543">Codice *after* dei filtri del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-543">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="dd6a5-544">Codice *after* dei filtri del controller.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-544">The *after* code of controller filters.</span></span>
* <span data-ttu-id="dd6a5-545">Codice *after* dei filtri globali.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-545">The *after* code of global filters.</span></span>
  
<span data-ttu-id="dd6a5-546">L'esempio seguente illustra l'ordine in cui i metodi dei filtri vengono chiamati per i filtri di azione sincroni.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-546">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="dd6a5-547">Sequenza</span><span class="sxs-lookup"><span data-stu-id="dd6a5-547">Sequence</span></span> | <span data-ttu-id="dd6a5-548">Ambito del filtro</span><span class="sxs-lookup"><span data-stu-id="dd6a5-548">Filter scope</span></span> | <span data-ttu-id="dd6a5-549">Metodo del filtro</span><span class="sxs-lookup"><span data-stu-id="dd6a5-549">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="dd6a5-550">1</span><span class="sxs-lookup"><span data-stu-id="dd6a5-550">1</span></span> | <span data-ttu-id="dd6a5-551">Globale</span><span class="sxs-lookup"><span data-stu-id="dd6a5-551">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="dd6a5-552">2</span><span class="sxs-lookup"><span data-stu-id="dd6a5-552">2</span></span> | <span data-ttu-id="dd6a5-553">Controller</span><span class="sxs-lookup"><span data-stu-id="dd6a5-553">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="dd6a5-554">3</span><span class="sxs-lookup"><span data-stu-id="dd6a5-554">3</span></span> | <span data-ttu-id="dd6a5-555">Metodo</span><span class="sxs-lookup"><span data-stu-id="dd6a5-555">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="dd6a5-556">4</span><span class="sxs-lookup"><span data-stu-id="dd6a5-556">4</span></span> | <span data-ttu-id="dd6a5-557">Metodo</span><span class="sxs-lookup"><span data-stu-id="dd6a5-557">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="dd6a5-558">5</span><span class="sxs-lookup"><span data-stu-id="dd6a5-558">5</span></span> | <span data-ttu-id="dd6a5-559">Controller</span><span class="sxs-lookup"><span data-stu-id="dd6a5-559">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="dd6a5-560">6</span><span class="sxs-lookup"><span data-stu-id="dd6a5-560">6</span></span> | <span data-ttu-id="dd6a5-561">Globale</span><span class="sxs-lookup"><span data-stu-id="dd6a5-561">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="dd6a5-562">Questa sequenza mostra che:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-562">This sequence shows:</span></span>

* <span data-ttu-id="dd6a5-563">Il filtro del metodo è annidato all'interno del filtro del controller.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-563">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="dd6a5-564">Il filtro del controller è annidato all'interno del filtro globale.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-564">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="dd6a5-565">Filtri a livello di controller e pagina Razor</span><span class="sxs-lookup"><span data-stu-id="dd6a5-565">Controller and Razor Page level filters</span></span>

<span data-ttu-id="dd6a5-566">Ogni controller che eredita dalla classe di base <xref:Microsoft.AspNetCore.Mvc.Controller> include i metodi [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) e [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-566">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="dd6a5-567">Questi metodi:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-567">These methods:</span></span>

* <span data-ttu-id="dd6a5-568">Eseguono il wrapping dei filtri che vengono eseguiti per una determinata azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-568">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="dd6a5-569">La chiamata di `OnActionExecuting` avviene prima di qualsiasi filtro dell'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-569">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="dd6a5-570">La chiamata di `OnActionExecuted` avviene dopo tutti i filtri dell'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-570">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="dd6a5-571">La chiamata di `OnActionExecutionAsync` avviene prima di qualsiasi filtro dell'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-571">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="dd6a5-572">Il codice del filtro dopo `next` viene eseguito dopo il metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-572">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="dd6a5-573">Ad esempio, l'applicazione di `MySampleActionFilter` nell'esempio scaricato avviene a livello globale all'avvio.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-573">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="dd6a5-574">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-574">The `TestController`:</span></span>

* <span data-ttu-id="dd6a5-575">Applica `SampleActionFilterAttribute` (`[SampleActionFilter]`) all' `FilterTest2` azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-575">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="dd6a5-576">Esegue l'override di `OnActionExecuting` e `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-576">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="dd6a5-577">Passando a `https://localhost:5001/Test/FilterTest2`, viene eseguito il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-577">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="dd6a5-578">Per Razor Pages, vedere [Implementare i filtri di Razor Pages eseguendo l'override dei metodi di filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-578">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="dd6a5-579">Override dell'ordine predefinito</span><span class="sxs-lookup"><span data-stu-id="dd6a5-579">Overriding the default order</span></span>

<span data-ttu-id="dd6a5-580">È possibile eseguire l'override della sequenza di esecuzione predefinita implementando <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-580">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="dd6a5-581">`IOrderedFilter` espone la proprietà <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order>, che ha la precedenza sull'ambito per determinare l'ordine di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-581">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="dd6a5-582">Un filtro con un valore di `Order` inferiore:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-582">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="dd6a5-583">Esegue il codice *before* prima di quello di un filtro con un valore di `Order` più alto.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-583">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="dd6a5-584">Esegue il codice *after* dopo quello di un filtro con un valore di `Order` più alto.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-584">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="dd6a5-585">La proprietà `Order` può essere impostata con un parametro del costruttore:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-585">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="dd6a5-586">Prendere in considerazione gli stessi tre filtri di azione illustrati nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-586">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="dd6a5-587">Se la proprietà `Order` del controller e dei filtri globali è impostata, rispettivamente, su 1 e su 2, l'ordine di esecuzione viene invertito.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-587">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="dd6a5-588">Sequenza</span><span class="sxs-lookup"><span data-stu-id="dd6a5-588">Sequence</span></span> | <span data-ttu-id="dd6a5-589">Ambito del filtro</span><span class="sxs-lookup"><span data-stu-id="dd6a5-589">Filter scope</span></span> | <span data-ttu-id="dd6a5-590">Proprietà `Order`</span><span class="sxs-lookup"><span data-stu-id="dd6a5-590">`Order` property</span></span> | <span data-ttu-id="dd6a5-591">Metodo del filtro</span><span class="sxs-lookup"><span data-stu-id="dd6a5-591">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="dd6a5-592">1</span><span class="sxs-lookup"><span data-stu-id="dd6a5-592">1</span></span> | <span data-ttu-id="dd6a5-593">Metodo</span><span class="sxs-lookup"><span data-stu-id="dd6a5-593">Method</span></span> | <span data-ttu-id="dd6a5-594">0</span><span class="sxs-lookup"><span data-stu-id="dd6a5-594">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="dd6a5-595">2</span><span class="sxs-lookup"><span data-stu-id="dd6a5-595">2</span></span> | <span data-ttu-id="dd6a5-596">Controller</span><span class="sxs-lookup"><span data-stu-id="dd6a5-596">Controller</span></span> | <span data-ttu-id="dd6a5-597">1</span><span class="sxs-lookup"><span data-stu-id="dd6a5-597">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="dd6a5-598">3</span><span class="sxs-lookup"><span data-stu-id="dd6a5-598">3</span></span> | <span data-ttu-id="dd6a5-599">Globale</span><span class="sxs-lookup"><span data-stu-id="dd6a5-599">Global</span></span> | <span data-ttu-id="dd6a5-600">2</span><span class="sxs-lookup"><span data-stu-id="dd6a5-600">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="dd6a5-601">4</span><span class="sxs-lookup"><span data-stu-id="dd6a5-601">4</span></span> | <span data-ttu-id="dd6a5-602">Globale</span><span class="sxs-lookup"><span data-stu-id="dd6a5-602">Global</span></span> | <span data-ttu-id="dd6a5-603">2</span><span class="sxs-lookup"><span data-stu-id="dd6a5-603">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="dd6a5-604">5</span><span class="sxs-lookup"><span data-stu-id="dd6a5-604">5</span></span> | <span data-ttu-id="dd6a5-605">Controller</span><span class="sxs-lookup"><span data-stu-id="dd6a5-605">Controller</span></span> | <span data-ttu-id="dd6a5-606">1</span><span class="sxs-lookup"><span data-stu-id="dd6a5-606">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="dd6a5-607">6</span><span class="sxs-lookup"><span data-stu-id="dd6a5-607">6</span></span> | <span data-ttu-id="dd6a5-608">Metodo</span><span class="sxs-lookup"><span data-stu-id="dd6a5-608">Method</span></span> | <span data-ttu-id="dd6a5-609">0</span><span class="sxs-lookup"><span data-stu-id="dd6a5-609">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="dd6a5-610">La proprietà `Order` ha la precedenza sull'ambito nel determinare l'ordine di esecuzione dei filtri.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-610">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="dd6a5-611">I filtri vengono ordinati prima in base all'ordine, poi viene usato l'ambito per interrompere i collegamenti.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-611">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="dd6a5-612">Tutti i filtri predefiniti implementano `IOrderedFilter` e impostano il valore predefinito di `Order` su 0.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-612">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="dd6a5-613">Per i filtri predefiniti, l'ambito determina l'ordine, a meno che non si imposti `Order` su un valore diverso da zero.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-613">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="dd6a5-614">Annullamento e corto circuito</span><span class="sxs-lookup"><span data-stu-id="dd6a5-614">Cancellation and short-circuiting</span></span>

<span data-ttu-id="dd6a5-615">È possibile causare il corto circuito della pipeline di filtro impostando la proprietà <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> sul parametro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fornito al metodo di filtro.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-615">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="dd6a5-616">Ad esempio, il filtro di risorse seguente impedisce l'esecuzione del resto della pipeline:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-616">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="dd6a5-617">Nel codice seguente sia il filtro `ShortCircuitingResourceFilter` che il filtro `AddHeader` hanno come destinazione il metodo di azione `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-617">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="dd6a5-618">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-618">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="dd6a5-619">Viene eseguito per primo perché è un filtro risorsa e `AddHeader` è un filtro azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-619">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="dd6a5-620">Blocca il resto della pipeline.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-620">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="dd6a5-621">Pertanto il filtro `AddHeader` non viene mai eseguito per l'azione `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-621">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="dd6a5-622">Questo comportamento sarebbe lo stesso se entrambi i filtri venissero applicati a livello di metodo di azione, a condizione che il filtro `ShortCircuitingResourceFilter` venga eseguito prima.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-622">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="dd6a5-623">`ShortCircuitingResourceFilter` viene eseguito per primo per il tipo di filtro o per l'uso esplicito della proprietà `Order`.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-623">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="dd6a5-624">Inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="dd6a5-624">Dependency injection</span></span>

<span data-ttu-id="dd6a5-625">È possibile aggiungere filtri per tipo o per istanza.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-625">Filters can be added by type or by instance.</span></span> <span data-ttu-id="dd6a5-626">Se viene aggiunta un'istanza, tale istanza viene usata per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-626">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="dd6a5-627">Se viene aggiunto un tipo, il filtro è attivato dal tipo.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-627">If a type is added, it's type-activated.</span></span> <span data-ttu-id="dd6a5-628">Un filtro attivato dal tipo comporta:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-628">A type-activated filter means:</span></span>

* <span data-ttu-id="dd6a5-629">La creazione di un'istanza per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-629">An instance is created for each request.</span></span>
* <span data-ttu-id="dd6a5-630">Il popolamento di tutte le dipendenze del costruttore tramite [inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-630">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="dd6a5-631">I filtri implementati come attributi e aggiunti direttamente alle classi controller o ai metodi di azione non possono avere dipendenze costruttore specificate dall'[inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-631">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="dd6a5-632">Le dipendenze del costruttore non possono essere fornite tramite inserimento delle dipendenze in quanto:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-632">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="dd6a5-633">I parametri del costruttore devono essere forniti agli attributi nella posizione in cui vengono applicati.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-633">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="dd6a5-634">Questa è una limitazione del funzionamento degli attributi.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-634">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="dd6a5-635">I filtri seguenti supportano le dipendenze del costruttore fornite dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-635">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="dd6a5-636"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementato nell'attributo.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-636"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="dd6a5-637">I filtri precedenti possono essere applicati a un controller o a un metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-637">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="dd6a5-638">I logger sono disponibili tramite l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-638">Loggers are available from DI.</span></span> <span data-ttu-id="dd6a5-639">Evitare tuttavia di creare e usare filtri esclusivamente per scopi di registrazione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-639">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="dd6a5-640">La funzionalità di [registrazione del framework predefinita](xref:fundamentals/logging/index) in genere fornisce il necessario per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-640">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="dd6a5-641">La registrazione aggiunta ai filtri:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-641">Logging added to filters:</span></span>

* <span data-ttu-id="dd6a5-642">Deve concentrarsi su problemi del dominio di business o comportamenti specifici del filtro.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-642">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="dd6a5-643">**Non** deve registrare azioni o altri eventi del framework.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-643">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="dd6a5-644">I filtri predefiniti registrano azioni ed eventi del framework.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-644">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="dd6a5-645">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="dd6a5-645">ServiceFilterAttribute</span></span>

<span data-ttu-id="dd6a5-646">I tipi di implementazione del filtro di servizi sono registrati in `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-646">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="dd6a5-647"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera un'istanza del filtro dall'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-647">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="dd6a5-648">Il codice seguente illustra `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-648">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="dd6a5-649">Nel codice seguente l'oggetto `AddHeaderResultServiceFilter` viene aggiunto al contenitore di inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-649">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="dd6a5-650">Nel codice seguente l'attributo `ServiceFilter` recupera un'istanza del filtro `AddHeaderResultServiceFilter` dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-650">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="dd6a5-651">Quando si usa l'impostazione `ServiceFilterAttribute`, [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="dd6a5-651">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="dd6a5-652">Indica che l'istanza del filtro *può* essere riutilizzata all'esterno dell'ambito della richiesta in cui è stata creata.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-652">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="dd6a5-653">Il runtime di ASP.NET Core non garantisce:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-653">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="dd6a5-654">Che venga creata una singola istanza del filtro.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-654">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="dd6a5-655">Che il filtro non verrà richiesto di nuovo dal contenitore di inserimento delle dipendenze in un momento successivo.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-655">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="dd6a5-656">Non si deve usare con un filtro che dipende da servizi con una durata diversa da singleton.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-656">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="dd6a5-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="dd6a5-658">`IFilterFactory` espone il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> per creare un'istanza <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-658">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="dd6a5-659">`CreateInstance` carica il tipo specificato dall'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-659">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="dd6a5-660">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="dd6a5-660">TypeFilterAttribute</span></span>

<span data-ttu-id="dd6a5-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> è simile a <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, ma il relativo tipo non viene risolto direttamente dal contenitore dell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="dd6a5-662">Viene creata un'istanza del tipo tramite <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-662">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="dd6a5-663">Poiché i tipi `TypeFilterAttribute` non vengono risolti direttamente dal contenitore di inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-663">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="dd6a5-664">Non è necessario che i tipi a cui viene fatto riferimento tramite `TypeFilterAttribute` siano registrati nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-664">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="dd6a5-665">Le loro dipendenze vengono soddisfatte dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-665">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="dd6a5-666">In via facoltativa, `TypeFilterAttribute` può anche accettare gli argomenti del costruttore per il tipo.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-666">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="dd6a5-667">Quando si usa l'impostazione `TypeFilterAttribute`, [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="dd6a5-667">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="dd6a5-668">Indica che l'istanza del filtro *può* essere riutilizzata all'esterno dell'ambito della richiesta in cui è stata creata.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-668">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="dd6a5-669">Il runtime di ASP.NET Core non fornisce alcuna garanzia che venga creata una singola istanza del filtro.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-669">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="dd6a5-670">Non si deve usare con un filtro che dipende da servizi con una durata diversa da singleton.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-670">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="dd6a5-671">L'esempio seguente illustra come passare argomenti a un tipo usando `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-671">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="dd6a5-672">Filtri autorizzazione</span><span class="sxs-lookup"><span data-stu-id="dd6a5-672">Authorization filters</span></span>

<span data-ttu-id="dd6a5-673">I filtri di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-673">Authorization filters:</span></span>

* <span data-ttu-id="dd6a5-674">Sono i primi filtri a essere eseguiti nella pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-674">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="dd6a5-675">Controllano l'accesso ai metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-675">Control access to action methods.</span></span>
* <span data-ttu-id="dd6a5-676">Dispongono di un metodo precedente, ma non di un metodo successivo.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-676">Have a before method, but no after method.</span></span>

<span data-ttu-id="dd6a5-677">I filtri di autorizzazione personalizzati richiedono un framework di autorizzazione personalizzato.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-677">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="dd6a5-678">È preferibile configurare criteri di autorizzazione o scrivere criteri di autorizzazione personalizzati piuttosto che scrivere un filtro personalizzato.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-678">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="dd6a5-679">Il filtro di autorizzazione predefinito:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-679">The built-in authorization filter:</span></span>

* <span data-ttu-id="dd6a5-680">Chiama il sistema di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-680">Calls the authorization system.</span></span>
* <span data-ttu-id="dd6a5-681">Non autorizza le richieste.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-681">Does not authorize requests.</span></span>

<span data-ttu-id="dd6a5-682">**Non** generare eccezioni nei filtri di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-682">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="dd6a5-683">L'eccezione non verrà gestita.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-683">The exception will not be handled.</span></span>
* <span data-ttu-id="dd6a5-684">I filtri di eccezione non gestiranno l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-684">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="dd6a5-685">È consigliabile emettere una richiesta di verifica in caso di eccezione in un filtro di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-685">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="dd6a5-686">Altre informazioni sull'[autorizzazione](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-686">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="dd6a5-687">Filtri risorse</span><span class="sxs-lookup"><span data-stu-id="dd6a5-687">Resource filters</span></span>

<span data-ttu-id="dd6a5-688">I filtri di risorse:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-688">Resource filters:</span></span>

* <span data-ttu-id="dd6a5-689">Implementano l'interfaccia <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-689">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="dd6a5-690">L'esecuzione racchiude la maggior parte della pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-690">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="dd6a5-691">Solo i [filtri di autorizzazione](#authorization-filters) vengono eseguiti prima dei filtri di risorse.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-691">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="dd6a5-692">I filtri di risorse sono utili per causare il corto circuito della maggior parte della pipeline.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-692">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="dd6a5-693">Un filtro di memorizzazione nella cache può, ad esempio, evitare il resto della pipeline in caso di riscontro nella cache.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-693">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="dd6a5-694">Esempi di filtri di risorse:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-694">Resource filter examples:</span></span>

* <span data-ttu-id="dd6a5-695">Il [filtro di risorse di corto circuito](#short-circuiting-resource-filter) illustrato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-695">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="dd6a5-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="dd6a5-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="dd6a5-697">Impedisce all'associazione di modelli di accedere ai dati del modulo.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-697">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="dd6a5-698">Viene usato quando si caricano file di grandi dimensioni per impedire la lettura dei dati del modulo in memoria.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-698">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="dd6a5-699">Filtri azione</span><span class="sxs-lookup"><span data-stu-id="dd6a5-699">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="dd6a5-700">I filtri azione **non** si applicano alle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-700">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="dd6a5-701">Pages <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> supporta <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> e.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-701"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="dd6a5-702">Per ulteriori informazioni, vedere [metodi di filtro Razor per le pagine](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-702">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="dd6a5-703">I filtri di azione:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-703">Action filters:</span></span>

* <span data-ttu-id="dd6a5-704">Implementano l'interfaccia <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-704">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="dd6a5-705">La loro esecuzione circonda l'esecuzione dei metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-705">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="dd6a5-706">Il codice seguente mostra un filtro di azione di esempio:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-706">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="dd6a5-707">La classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> specifica le proprietà seguenti:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-707">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="dd6a5-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>: consente di leggere gli input per un metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="dd6a5-709"><xref:Microsoft.AspNetCore.Mvc.Controller>: consente di modificare l'istanza del controller.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-709"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="dd6a5-710"><xref:System.Web.Mvc.ActionExecutingContext.Result>: l'impostazione di `Result` causa il corto circuito del metodo di azione e dei filtri di azione successivi.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-710"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="dd6a5-711">La generazione di un'eccezione in un metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-711">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="dd6a5-712">Impedisce l'esecuzione dei filtri successivi.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-712">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="dd6a5-713">A differenza dell'impostazione di `Result`, è considerata un errore anziché un risultato positivo.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-713">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="dd6a5-714">La classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> specifica `Controller` e `Result` oltre alle proprietà seguenti:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-714">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="dd6a5-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>: true se un altro file ha causato il corto circuito dell'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="dd6a5-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception>: non Null se l'azione o un filtro di azione eseguito in precedenza ha generato un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="dd6a5-717">Se si imposta questa proprietà su Null:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-717">Setting this property to null:</span></span>

  * <span data-ttu-id="dd6a5-718">L'eccezione viene gestita in modo efficace.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-718">Effectively handles the exception.</span></span>
  * <span data-ttu-id="dd6a5-719">L'oggetto `Result` viene eseguito come se fosse stato restituito dal metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-719">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="dd6a5-720">Per un oggetto `IAsyncActionFilter`, una chiamata a <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-720">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="dd6a5-721">Esegue qualsiasi filtro azione successivo e il metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-721">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="dd6a5-722">Restituisce `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-722">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="dd6a5-723">Per causare il corto circuito, assegnare <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a un'istanza del risultato e non chiamare `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-723">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="dd6a5-724">Il framework fornisce un oggetto <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> astratto che è possibile impostare come sottoclasse.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-724">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="dd6a5-725">Il filtro di azione `OnActionExecuting` può essere usato per:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-725">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="dd6a5-726">Convalidare lo stato del modello.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-726">Validate model state.</span></span>
* <span data-ttu-id="dd6a5-727">Restituire un errore se lo stato non è valido.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-727">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="dd6a5-728">Il metodo `OnActionExecuted` viene eseguito dopo il metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-728">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="dd6a5-729">È possibile visualizzare e modificare i risultati dell'azione tramite la proprietà <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-729">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="dd6a5-730">L'impostazione di <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> è true se un altro filtro ha causato il corto circuito dell'esecuzione dell'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="dd6a5-731">L'impostazione di <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> è un valore non Null se l'azione o un filtro di azione successivo ha generato un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-731"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="dd6a5-732">Impostazione di `Exception` su null:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-732">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="dd6a5-733">Gestisce un'eccezione in modo efficace.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-733">Effectively handles an exception.</span></span>
  * <span data-ttu-id="dd6a5-734">L'oggetto `ActionExecutedContext.Result` viene eseguito come se fosse stato restituito normalmente dal metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-734">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="dd6a5-735">Filtri eccezioni</span><span class="sxs-lookup"><span data-stu-id="dd6a5-735">Exception filters</span></span>

<span data-ttu-id="dd6a5-736">Filtri eccezioni:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-736">Exception filters:</span></span>

* <span data-ttu-id="dd6a5-737">Implementano <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-737">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="dd6a5-738">Possono essere usati per implementare criteri di gestione degli errori comuni.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-738">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="dd6a5-739">Il filtro di eccezione di esempio seguente usa una visualizzazione degli errori personalizzata per mostrare i dettagli sulle eccezioni che si verificano quando l'app è in fase di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-739">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="dd6a5-740">Filtri eccezioni:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-740">Exception filters:</span></span>

* <span data-ttu-id="dd6a5-741">Non dispone di eventi precedenti o successivi.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-741">Don't have before and after events.</span></span>
* <span data-ttu-id="dd6a5-742">Implementano <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-742">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="dd6a5-743">Gestire le eccezioni non gestite che si verificano nella creazione della pagina o del controller, dell' Razor [associazione di modelli](xref:mvc/models/model-binding), dei filtri azione o dei metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-743">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="dd6a5-744">**Non** rilevano le eccezioni che si verificano nei filtri di risorse, nei filtri dei risultati o nell'esecuzione dei risultati MVC.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-744">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="dd6a5-745">Per gestire un'eccezione, impostare la proprietà <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> su `true` o scrivere una risposta.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-745">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="dd6a5-746">In questo modo si arresta la propagazione dell'eccezione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-746">This stops propagation of the exception.</span></span> <span data-ttu-id="dd6a5-747">Un filtro di eccezione non può modificare un'eccezione in una "operazione riuscita".</span><span class="sxs-lookup"><span data-stu-id="dd6a5-747">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="dd6a5-748">Questa operazione può essere eseguita solo tramite un filtro di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-748">Only an action filter can do that.</span></span>

<span data-ttu-id="dd6a5-749">Filtri eccezioni:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-749">Exception filters:</span></span>

* <span data-ttu-id="dd6a5-750">Sono ideali per intercettare le eccezioni che si verificano nelle azioni.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-750">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="dd6a5-751">Non sono flessibili quanto il middleware di gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-751">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="dd6a5-752">Scegliere il middleware per la gestione delle eccezioni.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-752">Prefer middleware for exception handling.</span></span> <span data-ttu-id="dd6a5-753">Usare i filtri di eccezione solo quando la gestione degli errori *varia* in base al metodo di azione chiamato.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-753">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="dd6a5-754">Un'app può ad esempio avere metodi di azione sia per gli endpoint API che per le visualizzazioni/HTML.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-754">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="dd6a5-755">Gli endpoint dell'API possono restituire informazioni sull'errore come JSON, mentre le azioni basate sulla visualizzazione possono restituire una pagina di errore in formato HTML.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-755">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="dd6a5-756">Filtri risultato</span><span class="sxs-lookup"><span data-stu-id="dd6a5-756">Result filters</span></span>

<span data-ttu-id="dd6a5-757">I filtri dei risultati:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-757">Result filters:</span></span>

* <span data-ttu-id="dd6a5-758">Implementare un'interfaccia:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-758">Implement an interface:</span></span>
  * <span data-ttu-id="dd6a5-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="dd6a5-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="dd6a5-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="dd6a5-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="dd6a5-761">La loro esecuzione circonda l'esecuzione dei risultati dell'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-761">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="dd6a5-762">IResultFilter e IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="dd6a5-762">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="dd6a5-763">Il codice seguente illustra un filtro dei risultati che aggiunge un'intestazione HTTP:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-763">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="dd6a5-764">Il tipo di risultato eseguito dipende dall'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-764">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="dd6a5-765">Un'azione che restituisce una visualizzazione include tutte le elaborazioni Razor come parte dell'elemento <xref:Microsoft.AspNetCore.Mvc.ViewResult> eseguito.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-765">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="dd6a5-766">Un metodo API può eseguire la serializzazione in quanto parte dell'esecuzione del risultato.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-766">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="dd6a5-767">Altre informazioni sui [risultati dell'azione](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-767">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="dd6a5-768">I filtri dei risultati vengono eseguiti solo quando un filtro azione o azione produce un risultato di azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-768">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="dd6a5-769">I filtri dei risultati non vengono eseguiti nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-769">Result filters are not executed when:</span></span>

* <span data-ttu-id="dd6a5-770">Un filtro di autorizzazione o un filtro risorse cortocircui la pipeline.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-770">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="dd6a5-771">Un filtro di eccezione non gestisca un'eccezione producendo un risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-771">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="dd6a5-772">Il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> può causare il corto circuito dell'esecuzione del risultato dell'azione e dei filtri dei risultati successivi se si imposta <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> su `true`.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-772">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="dd6a5-773">In caso di corto circuito, scrivere nell'oggetto risposta per evitare di generare una risposta vuota.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-773">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="dd6a5-774">La generazione di un'eccezione in `IResultFilter.OnResultExecuting`:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-774">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="dd6a5-775">Impedisce l'esecuzione del risultato dell'azione e dei filtri successivi.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-775">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="dd6a5-776">È considerata un errore anziché un risultato positivo.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-776">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="dd6a5-777">Quando viene <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> eseguito il metodo, è probabile che la risposta sia già stata inviata al client.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-777">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="dd6a5-778">Se la risposta è già stata inviata al client, non è possibile modificarla ulteriormente.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-778">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="dd6a5-779">L'impostazione di `ResultExecutedContext.Canceled` è `true` se un altro filtro ha causato il corto circuito dell'esecuzione del risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-779">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="dd6a5-780">L'impostazione di `ResultExecutedContext.Exception` è un valore non Null se il risultato dell'azione o un filtro dei risultati successivo ha generato un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-780">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="dd6a5-781">Se si imposta `Exception` su Null l'eccezione viene gestita in modo efficace e si evita che venga generata nuovamente da ASP.NET Core in un punto successivo della pipeline.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-781">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="dd6a5-782">Non c'è alcun modo affidabile per scrivere i dati in una risposta quando si gestisce un'eccezione in un filtro dei risultati.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-782">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="dd6a5-783">Se le intestazioni sono state scaricate nel client quando il risultato di un'azione genera un'eccezione, non c'è alcun meccanismo affidabile per inviare un codice di errore.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-783">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="dd6a5-784">Per un oggetto <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, una chiamata a `await next` in <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> esegue tutti i filtri dei risultati successivi e il risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-784">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="dd6a5-785">Per causare il corto circuito, impostare [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) su `true` e non chiamare `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-785">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="dd6a5-786">Il framework fornisce un oggetto `ResultFilterAttribute` astratto che è possibile impostare come sottoclasse.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-786">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="dd6a5-787">La classe [AddHeaderAttribute](#add-header-attribute) illustrata in precedenza è un esempio di un attributo di filtro dei risultati.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-787">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="dd6a5-788">IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="dd6a5-788">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="dd6a5-789">Le interfacce <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> dichiarano un'implementazione di <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> eseguita per tutti i risultati dell'azione.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-789">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="dd6a5-790">Sono inclusi i risultati dell'azione prodotti da:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-790">This includes action results produced by:</span></span>

* <span data-ttu-id="dd6a5-791">Filtri di autorizzazione e filtri delle risorse che interessano il cortocircuito.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-791">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="dd6a5-792">Filtri eccezioni.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-792">Exception filters.</span></span>

<span data-ttu-id="dd6a5-793">Ad esempio, il filtro seguente viene eseguito sempre e imposta un risultato dell'azione (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) con un codice di stato *422 Entità non elaborabile* quando la negoziazione del contenuto ha esito negativo:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-793">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="dd6a5-794">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="dd6a5-794">IFilterFactory</span></span>

<span data-ttu-id="dd6a5-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="dd6a5-796">Pertanto, un'istanza di `IFilterFactory` può essere usata come un'istanza di `IFilterMetadata` in un punto qualsiasi della pipeline filtro.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-796">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="dd6a5-797">Quando il runtime si prepara per richiamare il filtro, cerca di eseguirne il cast a un oggetto `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-797">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="dd6a5-798">Se l'esecuzione del cast ha esito positivo, viene chiamato il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> per creare l'istanza di `IFilterMetadata` richiamata.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-798">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="dd6a5-799">In questo modo viene specificata una struttura flessibile, poiché non è necessario impostare in modo esplicito la pipeline filtro all'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-799">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="dd6a5-800">Un altro approccio alla creazione di filtri consiste nell'implementare `IFilterFactory` usando implementazioni dell'attributo personalizzate:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-800">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="dd6a5-801">Il codice precedente può essere testato eseguendo l'[esempio scaricato](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="dd6a5-801">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="dd6a5-802">Richiamare gli strumenti di sviluppo F12.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-802">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="dd6a5-803">Accedere a `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-803">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="dd6a5-804">Gli strumenti di sviluppo F12 visualizzano le intestazioni di risposta seguenti aggiunte dal codice di esempio:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-804">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="dd6a5-805">**Autore:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="dd6a5-805">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="dd6a5-806">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="dd6a5-806">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="dd6a5-807">**interno:**`My header`</span><span class="sxs-lookup"><span data-stu-id="dd6a5-807">**internal:** `My header`</span></span>

<span data-ttu-id="dd6a5-808">Il codice precedente crea l'intestazione di risposta **internal:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-808">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="dd6a5-809">Implementazione di IFilterFactory in un attributo</span><span class="sxs-lookup"><span data-stu-id="dd6a5-809">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="dd6a5-810">I filtri che implementano `IFilterFactory` sono utili per i filtri che:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-810">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="dd6a5-811">Non richiedono il passaggio di parametri.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-811">Don't require passing parameters.</span></span>
* <span data-ttu-id="dd6a5-812">Hanno dipendenze del costruttore che devono essere soddisfatte dall'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-812">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="dd6a5-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="dd6a5-814">`IFilterFactory` espone il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> per creare un'istanza <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-814">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="dd6a5-815">`CreateInstance` carica il tipo specificato dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-815">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="dd6a5-816">Il codice seguente illustra tre approcci all'applicazione di `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-816">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="dd6a5-817">Nel codice precedente la decorazione del metodo con `[SampleActionFilter]` è l'approccio da preferire per applicare `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-817">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="dd6a5-818">Uso di middleware nella pipeline filtro</span><span class="sxs-lookup"><span data-stu-id="dd6a5-818">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="dd6a5-819">I filtri risorse funzionano come [middleware](xref:fundamentals/middleware/index) in quanto racchiudono l'esecuzione di tutto ciò che viene dopo nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-819">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="dd6a5-820">I filtri sono diversi dal middleware in quanto fanno parte del runtime di ASP.NET Core, quindi hanno accesso al contesto e ai costrutti di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-820">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="dd6a5-821">Per usare il middleware come filtro, creare un tipo con un metodo `Configure` che specifica il middleware da inserire nella pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-821">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="dd6a5-822">L'esempio seguente usa il middleware di localizzazione per stabilire le impostazioni cultura correnti per una richiesta:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-822">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="dd6a5-823">Usare <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> per eseguire il middleware:</span><span class="sxs-lookup"><span data-stu-id="dd6a5-823">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="dd6a5-824">I filtri middleware vengono eseguiti nella stessa fase della pipeline filtro come filtri risorse, prima dell'associazione di modelli e dopo il resto della pipeline.</span><span class="sxs-lookup"><span data-stu-id="dd6a5-824">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="dd6a5-825">Azioni successive</span><span class="sxs-lookup"><span data-stu-id="dd6a5-825">Next actions</span></span>

* <span data-ttu-id="dd6a5-826">Vedere [metodi di filtro Razor per le pagine](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-826">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="dd6a5-827">Per sperimentare i filtri, [scaricare, testare e modificare l'esempio di GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="dd6a5-827">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
