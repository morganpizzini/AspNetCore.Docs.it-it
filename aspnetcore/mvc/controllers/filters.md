---
title: Filtri in ASP.NET Core
author: Rick-Anderson
description: Informazioni sul funzionamento dei filtri e su come usarli in ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/filters
ms.openlocfilehash: 0141ad2df5216183424980a6ca50bf6bcd64ade5
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86213055"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="7de38-103">Filtri in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7de38-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7de38-104">Di [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="7de38-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="7de38-105">I *filtri* in ASP.NET Core consentono l'esecuzione del codice prima o dopo fasi specifiche della pipeline di elaborazione della richiesta.</span><span class="sxs-lookup"><span data-stu-id="7de38-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="7de38-106">I filtri predefiniti gestiscono attività, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7de38-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="7de38-107">Autorizzazione (impedire l'accesso alle risorse per cui un utente non è autorizzato).</span><span class="sxs-lookup"><span data-stu-id="7de38-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="7de38-108">Memorizzazione nella cache delle risposte (blocco della pipeline delle richieste per restituire una risposta memorizzata nella cache).</span><span class="sxs-lookup"><span data-stu-id="7de38-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="7de38-109">I filtri personalizzati possono essere creati per gestire problemi relativi a più settori.</span><span class="sxs-lookup"><span data-stu-id="7de38-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="7de38-110">Esempi di problematiche trasversali includono la gestione degli errori, la memorizzazione nella cache, la configurazione, l'autorizzazione e la registrazione.</span><span class="sxs-lookup"><span data-stu-id="7de38-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="7de38-111">I filtri evitano la duplicazione del codice.</span><span class="sxs-lookup"><span data-stu-id="7de38-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="7de38-112">Ad esempio, un filtro eccezioni per la gestione degli errori potrebbe consolidare la gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="7de38-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="7de38-113">Questo documento si applica a Razor pagine, controller API e controller con visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="7de38-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="7de38-114">I filtri non funzionano direttamente con i [ Razor componenti](xref:blazor/components/index).</span><span class="sxs-lookup"><span data-stu-id="7de38-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="7de38-115">Un filtro può influire indirettamente su un componente solo quando:</span><span class="sxs-lookup"><span data-stu-id="7de38-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="7de38-116">Il componente è incorporato in una pagina o in una vista.</span><span class="sxs-lookup"><span data-stu-id="7de38-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="7de38-117">La pagina o il controller/Visualizzazione usa il filtro.</span><span class="sxs-lookup"><span data-stu-id="7de38-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="7de38-118">[Visualizzare o scaricare un esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="7de38-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="7de38-119">Funzionamento dei filtri</span><span class="sxs-lookup"><span data-stu-id="7de38-119">How filters work</span></span>

<span data-ttu-id="7de38-120">I filtri vengono eseguiti all'interno della *pipeline di chiamata di azioni ASP.NET Core*, talvolta definita *pipeline di filtro*.</span><span class="sxs-lookup"><span data-stu-id="7de38-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="7de38-121">La pipeline di filtro viene eseguita dopo che ASP.NET Core ha selezionato l'azione da eseguire.</span><span class="sxs-lookup"><span data-stu-id="7de38-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![La richiesta viene elaborata tramite altri middleware, middleware di routing, selezione dell'azione e pipeline di chiamata dell'azione.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="7de38-124">Tipi di filtro</span><span class="sxs-lookup"><span data-stu-id="7de38-124">Filter types</span></span>

<span data-ttu-id="7de38-125">Ogni tipo di filtro viene eseguito in una fase diversa della pipeline di filtro:</span><span class="sxs-lookup"><span data-stu-id="7de38-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="7de38-126">I [filtri di autorizzazione](#authorization-filters) vengono eseguiti per primi e consentono di determinare se l'utente è autorizzato per la richiesta.</span><span class="sxs-lookup"><span data-stu-id="7de38-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="7de38-127">I filtri di autorizzazione consentono di cortocircuitare la pipeline se la richiesta non è autorizzata.</span><span class="sxs-lookup"><span data-stu-id="7de38-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="7de38-128">[Filtri risorse](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="7de38-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="7de38-129">Vengono eseguiti dopo l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="7de38-129">Run after authorization.</span></span>  
  * <span data-ttu-id="7de38-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>esegue il codice prima del resto della pipeline filtro.</span><span class="sxs-lookup"><span data-stu-id="7de38-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="7de38-131">Ad esempio, `OnResourceExecuting` esegue il codice prima dell'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="7de38-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="7de38-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>esegue il codice una volta completato il resto della pipeline.</span><span class="sxs-lookup"><span data-stu-id="7de38-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="7de38-133">[Filtri azione](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="7de38-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="7de38-134">Eseguire il codice immediatamente prima e dopo la chiamata di un metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="7de38-135">Consente di modificare gli argomenti passati in un'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="7de38-136">Può modificare il risultato restituito dall'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="7de38-137">**Non** sono supportate nelle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="7de38-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="7de38-138">I [filtri eccezioni](#exception-filters) applicano i criteri globali alle eccezioni non gestite che si verificano prima della scrittura del corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="7de38-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="7de38-139">I [filtri risultati](#result-filters) eseguono il codice immediatamente prima e dopo l'esecuzione dei risultati dell'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="7de38-140">Vengono eseguiti solo quando il metodo di azione è stato eseguito correttamente.</span><span class="sxs-lookup"><span data-stu-id="7de38-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="7de38-141">Sono utili per la logica che deve racchiudere l'esecuzione del formattatore o di una vista.</span><span class="sxs-lookup"><span data-stu-id="7de38-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="7de38-142">Il diagramma seguente illustra come interagiscono i tipi di filtro nella pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="7de38-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![La richiesta passa attraverso i filtri autorizzazione, i filtri risorse, l'associazione di modelli, i filtri azione, l'esecuzione dell'azione e la conversione del risultato dell'azione, i filtri eccezioni, i filtri risultato e l'esecuzione del risultato.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="7de38-145">Implementazione</span><span class="sxs-lookup"><span data-stu-id="7de38-145">Implementation</span></span>

<span data-ttu-id="7de38-146">I filtri supportano sia implementazioni sincrone che asincrone tramite definizioni di interfaccia diverse.</span><span class="sxs-lookup"><span data-stu-id="7de38-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="7de38-147">I filtri sincroni eseguono codice prima e dopo la fase della pipeline.</span><span class="sxs-lookup"><span data-stu-id="7de38-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="7de38-148">La chiamata di <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*>, ad esempio, avviene prima della chiamata del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="7de38-149">La chiamata di <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> avviene dopo l'esecuzione del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="7de38-150">I filtri asincroni definiscono un `On-Stage-ExecutionAsync` metodo.</span><span class="sxs-lookup"><span data-stu-id="7de38-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="7de38-151">Ad esempio, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="7de38-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="7de38-152">Nel codice precedente, `SampleAsyncActionFilter` ha un <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ) che esegue il metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="7de38-153">Fasi di filtro multiple</span><span class="sxs-lookup"><span data-stu-id="7de38-153">Multiple filter stages</span></span>

<span data-ttu-id="7de38-154">È possibile implementare interfacce per più fasi di filtro in una singola classe.</span><span class="sxs-lookup"><span data-stu-id="7de38-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="7de38-155">Ad esempio, la <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> classe implementa:</span><span class="sxs-lookup"><span data-stu-id="7de38-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="7de38-156">Sincrona: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> e<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="7de38-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="7de38-157">Asincrono: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> e<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="7de38-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="7de38-158">Implementare la versione sincrona **oppure** la versione asincrona di un'interfaccia di filtro, **non** entrambe.</span><span class="sxs-lookup"><span data-stu-id="7de38-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="7de38-159">Il runtime controlla per prima cosa se il filtro implementa l'interfaccia asincrona e, in tal caso, la chiama.</span><span class="sxs-lookup"><span data-stu-id="7de38-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="7de38-160">In caso contrario, chiama i metodi dell'interfaccia sincrona.</span><span class="sxs-lookup"><span data-stu-id="7de38-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="7de38-161">Se in una classe sono implementate entrambe le interfacce, sincrona e asincrona, viene chiamato solo il metodo asincrono.</span><span class="sxs-lookup"><span data-stu-id="7de38-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="7de38-162">Quando si usano classi astratte come <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , eseguire l'override solo dei metodi sincroni o del metodo asincrono per ogni tipo di filtro.</span><span class="sxs-lookup"><span data-stu-id="7de38-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="7de38-163">Attributi filtro predefiniti</span><span class="sxs-lookup"><span data-stu-id="7de38-163">Built-in filter attributes</span></span>

<span data-ttu-id="7de38-164">ASP.NET Core include filtri predefiniti basati su attributi che è possibile impostare come sottoclasse e personalizzare.</span><span class="sxs-lookup"><span data-stu-id="7de38-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="7de38-165">Il filtro dei risultati seguente, ad esempio, aggiunge un'intestazione alla risposta:</span><span class="sxs-lookup"><span data-stu-id="7de38-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="7de38-166">Gli attributi consentono ai filtri di accettare argomenti, come illustrato nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="7de38-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="7de38-167">Applicare `AddHeaderAttribute` a un controller o a un metodo di azione e specificare il nome e il valore dell'intestazione HTTP:</span><span class="sxs-lookup"><span data-stu-id="7de38-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="7de38-168">Usare uno strumento come gli [strumenti di sviluppo del browser](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) per esaminare le intestazioni.</span><span class="sxs-lookup"><span data-stu-id="7de38-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="7de38-169">In **intestazioni risposta** `author: Rick Anderson` viene visualizzato.</span><span class="sxs-lookup"><span data-stu-id="7de38-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="7de38-170">Il codice seguente implementa un oggetto `ActionFilterAttribute` che:</span><span class="sxs-lookup"><span data-stu-id="7de38-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="7de38-171">Legge il titolo e il nome dal sistema di configurazione.</span><span class="sxs-lookup"><span data-stu-id="7de38-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="7de38-172">A differenza dell'esempio precedente, il codice seguente non richiede l'aggiunta di parametri di filtro al codice.</span><span class="sxs-lookup"><span data-stu-id="7de38-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="7de38-173">Aggiunge il titolo e il nome all'intestazione della risposta.</span><span class="sxs-lookup"><span data-stu-id="7de38-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="7de38-174">Le opzioni di configurazione vengono fornite dal [sistema di configurazione](xref:fundamentals/configuration/index) usando il [modello di opzioni](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="7de38-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="7de38-175">Ad esempio, dal *appsettings.jssu* file:</span><span class="sxs-lookup"><span data-stu-id="7de38-175">For example, from the *appsettings.json* file:</span></span>

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="7de38-176">Nel `StartUp.ConfigureServices` eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="7de38-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="7de38-177">La `PositionOptions` classe viene aggiunta al contenitore del servizio con l' `"Position"` area di configurazione.</span><span class="sxs-lookup"><span data-stu-id="7de38-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="7de38-178">`MyActionFilterAttribute`Viene aggiunto al contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="7de38-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="7de38-179">Il codice seguente illustra la `PositionOptions` classe:</span><span class="sxs-lookup"><span data-stu-id="7de38-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="7de38-180">Il codice seguente applica al `MyActionFilterAttribute` `Index2` Metodo:</span><span class="sxs-lookup"><span data-stu-id="7de38-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="7de38-181">In **intestazioni di risposta**, `author: Rick Anderson` e `Editor: Joe Smith` viene visualizzato quando `Sample/Index2` viene chiamato l'endpoint.</span><span class="sxs-lookup"><span data-stu-id="7de38-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="7de38-182">Il codice seguente applica `MyActionFilterAttribute` e alla `AddHeaderAttribute` Razor pagina:</span><span class="sxs-lookup"><span data-stu-id="7de38-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="7de38-183">Non è possibile applicare i filtri ai Razor metodi del gestore di pagina.</span><span class="sxs-lookup"><span data-stu-id="7de38-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="7de38-184">Possono essere applicati al Razor modello di pagina o a livello globale.</span><span class="sxs-lookup"><span data-stu-id="7de38-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="7de38-185">Molte delle interfacce del filtro presentano attributi corrispondenti che possono essere usati come classi di base per implementazioni personalizzate.</span><span class="sxs-lookup"><span data-stu-id="7de38-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="7de38-186">Attributi dei filtri:</span><span class="sxs-lookup"><span data-stu-id="7de38-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="7de38-187">Ambiti dei filtri e ordine di esecuzione</span><span class="sxs-lookup"><span data-stu-id="7de38-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="7de38-188">È possibile aggiungere un filtro alla pipeline in uno dei tre *ambiti*:</span><span class="sxs-lookup"><span data-stu-id="7de38-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="7de38-189">Uso di un attributo in un'azione del controller.</span><span class="sxs-lookup"><span data-stu-id="7de38-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="7de38-190">Gli attributi di filtro non possono essere applicati ai Razor metodi del gestore di pagine.</span><span class="sxs-lookup"><span data-stu-id="7de38-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="7de38-191">Uso di un attributo in un controller o in una Razor pagina.</span><span class="sxs-lookup"><span data-stu-id="7de38-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="7de38-192">A livello globale per tutti i controller, le azioni e le Razor pagine, come illustrato nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="7de38-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="7de38-193">Ordine di esecuzione predefinito</span><span class="sxs-lookup"><span data-stu-id="7de38-193">Default order of execution</span></span>

<span data-ttu-id="7de38-194">Quando sono presenti più filtri per una particolare fase della pipeline, l'ambito determina l'ordine di esecuzione predefinito dei filtri.</span><span class="sxs-lookup"><span data-stu-id="7de38-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="7de38-195">I filtri globali racchiudono i filtri di classe, che a loro volta racchiudono i filtri dei metodi.</span><span class="sxs-lookup"><span data-stu-id="7de38-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="7de38-196">Come risultato dell'annidamento dei filtri, il codice *after* dei filtri viene eseguito in ordine inverso rispetto al codice *before*.</span><span class="sxs-lookup"><span data-stu-id="7de38-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="7de38-197">Sequenza di filtro:</span><span class="sxs-lookup"><span data-stu-id="7de38-197">The filter sequence:</span></span>

* <span data-ttu-id="7de38-198">Codice *before* dei filtri globali.</span><span class="sxs-lookup"><span data-stu-id="7de38-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="7de38-199">Codice *prima* dei filtri di pagina e controller Razor .</span><span class="sxs-lookup"><span data-stu-id="7de38-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="7de38-200">Codice *before* dei filtri del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="7de38-201">Codice *after* dei filtri del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="7de38-202">Codice *dopo* i filtri del controller e della Razor pagina.</span><span class="sxs-lookup"><span data-stu-id="7de38-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="7de38-203">Codice *after* dei filtri globali.</span><span class="sxs-lookup"><span data-stu-id="7de38-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="7de38-204">L'esempio seguente illustra l'ordine in cui i metodi dei filtri vengono chiamati per i filtri di azione sincroni.</span><span class="sxs-lookup"><span data-stu-id="7de38-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="7de38-205">Sequenza</span><span class="sxs-lookup"><span data-stu-id="7de38-205">Sequence</span></span> | <span data-ttu-id="7de38-206">Ambito del filtro</span><span class="sxs-lookup"><span data-stu-id="7de38-206">Filter scope</span></span> | <span data-ttu-id="7de38-207">Metodo del filtro</span><span class="sxs-lookup"><span data-stu-id="7de38-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="7de38-208">1</span><span class="sxs-lookup"><span data-stu-id="7de38-208">1</span></span> | <span data-ttu-id="7de38-209">Globale</span><span class="sxs-lookup"><span data-stu-id="7de38-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7de38-210">2</span><span class="sxs-lookup"><span data-stu-id="7de38-210">2</span></span> | <span data-ttu-id="7de38-211">Controller o Razor pagina</span><span class="sxs-lookup"><span data-stu-id="7de38-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="7de38-212">3</span><span class="sxs-lookup"><span data-stu-id="7de38-212">3</span></span> | <span data-ttu-id="7de38-213">Metodo</span><span class="sxs-lookup"><span data-stu-id="7de38-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7de38-214">4</span><span class="sxs-lookup"><span data-stu-id="7de38-214">4</span></span> | <span data-ttu-id="7de38-215">Metodo</span><span class="sxs-lookup"><span data-stu-id="7de38-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="7de38-216">5</span><span class="sxs-lookup"><span data-stu-id="7de38-216">5</span></span> | <span data-ttu-id="7de38-217">Controller o Razor pagina</span><span class="sxs-lookup"><span data-stu-id="7de38-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="7de38-218">6</span><span class="sxs-lookup"><span data-stu-id="7de38-218">6</span></span> | <span data-ttu-id="7de38-219">Globale</span><span class="sxs-lookup"><span data-stu-id="7de38-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="7de38-220">Filtri a livello di controller</span><span class="sxs-lookup"><span data-stu-id="7de38-220">Controller level filters</span></span>

<span data-ttu-id="7de38-221">Ogni controller che eredita dalla classe di base <xref:Microsoft.AspNetCore.Mvc.Controller> include i metodi [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) e [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="7de38-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="7de38-222">Questi metodi:</span><span class="sxs-lookup"><span data-stu-id="7de38-222">These methods:</span></span>

* <span data-ttu-id="7de38-223">Eseguono il wrapping dei filtri che vengono eseguiti per una determinata azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="7de38-224">La chiamata di `OnActionExecuting` avviene prima di qualsiasi filtro dell'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="7de38-225">La chiamata di `OnActionExecuted` avviene dopo tutti i filtri dell'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="7de38-226">La chiamata di `OnActionExecutionAsync` avviene prima di qualsiasi filtro dell'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="7de38-227">Il codice del filtro dopo `next` viene eseguito dopo il metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="7de38-228">Ad esempio, l'applicazione di `MySampleActionFilter` nell'esempio scaricato avviene a livello globale all'avvio.</span><span class="sxs-lookup"><span data-stu-id="7de38-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="7de38-229">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="7de38-229">The `TestController`:</span></span>

* <span data-ttu-id="7de38-230">Applica `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) all' `FilterTest2` azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="7de38-231">Esegue l'override di `OnActionExecuting` e `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="7de38-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="7de38-232">Passando a `https://localhost:5001/Test2/FilterTest2`, viene eseguito il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="7de38-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="7de38-233">Filtri a livello di controller impostare la proprietà [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) su `int.MinValue` .</span><span class="sxs-lookup"><span data-stu-id="7de38-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="7de38-234">I filtri a livello di controller **non** possono essere impostati per l'esecuzione dopo i filtri applicati ai metodi.</span><span class="sxs-lookup"><span data-stu-id="7de38-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="7de38-235">L'ordine è illustrato nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="7de38-235">Order is explained in the next section.</span></span>

<span data-ttu-id="7de38-236">Per le Razor pagine, vedere [implementare Razor filtri di pagina eseguendo l'override dei metodi di filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="7de38-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="7de38-237">Override dell'ordine predefinito</span><span class="sxs-lookup"><span data-stu-id="7de38-237">Overriding the default order</span></span>

<span data-ttu-id="7de38-238">È possibile eseguire l'override della sequenza di esecuzione predefinita implementando <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="7de38-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="7de38-239">`IOrderedFilter` espone la proprietà <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order>, che ha la precedenza sull'ambito per determinare l'ordine di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="7de38-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="7de38-240">Un filtro con un valore di `Order` inferiore:</span><span class="sxs-lookup"><span data-stu-id="7de38-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="7de38-241">Esegue il codice *before* prima di quello di un filtro con un valore di `Order` più alto.</span><span class="sxs-lookup"><span data-stu-id="7de38-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="7de38-242">Esegue il codice *after* dopo quello di un filtro con un valore di `Order` più alto.</span><span class="sxs-lookup"><span data-stu-id="7de38-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="7de38-243">La `Order` proprietà viene impostata con un parametro del costruttore:</span><span class="sxs-lookup"><span data-stu-id="7de38-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="7de38-244">Considerare i due filtri azione nel controller seguente:</span><span class="sxs-lookup"><span data-stu-id="7de38-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="7de38-245">In è stato aggiunto un filtro globale `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7de38-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="7de38-246">I 3 filtri vengono eseguiti nell'ordine seguente:</span><span class="sxs-lookup"><span data-stu-id="7de38-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="7de38-247">La proprietà `Order` ha la precedenza sull'ambito nel determinare l'ordine di esecuzione dei filtri.</span><span class="sxs-lookup"><span data-stu-id="7de38-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="7de38-248">I filtri vengono ordinati prima in base all'ordine, poi viene usato l'ambito per interrompere i collegamenti.</span><span class="sxs-lookup"><span data-stu-id="7de38-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="7de38-249">Tutti i filtri predefiniti implementano `IOrderedFilter` e impostano il valore predefinito di `Order` su 0.</span><span class="sxs-lookup"><span data-stu-id="7de38-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="7de38-250">Come indicato in precedenza, i filtri a livello di controller impostano la proprietà [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) su `int.MinValue` per i filtri incorporati, l'ambito determina l'ordine a meno che non `Order` sia impostato su un valore diverso da zero.</span><span class="sxs-lookup"><span data-stu-id="7de38-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="7de38-251">Nel codice precedente, `MySampleActionFilter` ha ambito globale, quindi viene eseguito prima di `MyAction2FilterAttribute` , che ha ambito del controller.</span><span class="sxs-lookup"><span data-stu-id="7de38-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="7de38-252">Per `MyAction2FilterAttribute` eseguire prima l'esecuzione, impostare l'ordine su `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="7de38-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="7de38-253">Per eseguire prima l'esecuzione del filtro globale `MySampleActionFilter` , impostare `Order` su `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="7de38-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="7de38-254">Annullamento e corto circuito</span><span class="sxs-lookup"><span data-stu-id="7de38-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="7de38-255">È possibile causare il corto circuito della pipeline di filtro impostando la proprietà <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> sul parametro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fornito al metodo di filtro.</span><span class="sxs-lookup"><span data-stu-id="7de38-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="7de38-256">Ad esempio, il filtro di risorse seguente impedisce l'esecuzione del resto della pipeline:</span><span class="sxs-lookup"><span data-stu-id="7de38-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="7de38-257">Nel codice seguente sia il filtro `ShortCircuitingResourceFilter` che il filtro `AddHeader` hanno come destinazione il metodo di azione `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="7de38-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="7de38-258">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="7de38-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="7de38-259">Viene eseguito per primo perché è un filtro risorsa e `AddHeader` è un filtro azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="7de38-260">Blocca il resto della pipeline.</span><span class="sxs-lookup"><span data-stu-id="7de38-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="7de38-261">Pertanto il filtro `AddHeader` non viene mai eseguito per l'azione `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="7de38-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="7de38-262">Questo comportamento sarebbe lo stesso se entrambi i filtri venissero applicati a livello di metodo di azione, a condizione che il filtro `ShortCircuitingResourceFilter` venga eseguito prima.</span><span class="sxs-lookup"><span data-stu-id="7de38-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="7de38-263">`ShortCircuitingResourceFilter` viene eseguito per primo per il tipo di filtro o per l'uso esplicito della proprietà `Order`.</span><span class="sxs-lookup"><span data-stu-id="7de38-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="7de38-264">Inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="7de38-264">Dependency injection</span></span>

<span data-ttu-id="7de38-265">È possibile aggiungere filtri per tipo o per istanza.</span><span class="sxs-lookup"><span data-stu-id="7de38-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="7de38-266">Se viene aggiunta un'istanza, tale istanza viene usata per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="7de38-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="7de38-267">Se viene aggiunto un tipo, il filtro è attivato dal tipo.</span><span class="sxs-lookup"><span data-stu-id="7de38-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="7de38-268">Un filtro attivato dal tipo comporta:</span><span class="sxs-lookup"><span data-stu-id="7de38-268">A type-activated filter means:</span></span>

* <span data-ttu-id="7de38-269">La creazione di un'istanza per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="7de38-269">An instance is created for each request.</span></span>
* <span data-ttu-id="7de38-270">Il popolamento di tutte le dipendenze del costruttore tramite [inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7de38-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="7de38-271">I filtri implementati come attributi e aggiunti direttamente alle classi controller o ai metodi di azione non possono avere dipendenze costruttore specificate dall'[inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7de38-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="7de38-272">Le dipendenze del costruttore non possono essere fornite tramite inserimento delle dipendenze in quanto:</span><span class="sxs-lookup"><span data-stu-id="7de38-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="7de38-273">I parametri del costruttore devono essere forniti agli attributi nella posizione in cui vengono applicati.</span><span class="sxs-lookup"><span data-stu-id="7de38-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="7de38-274">Questa è una limitazione del funzionamento degli attributi.</span><span class="sxs-lookup"><span data-stu-id="7de38-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="7de38-275">I filtri seguenti supportano le dipendenze del costruttore fornite dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="7de38-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="7de38-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementato nell'attributo.</span><span class="sxs-lookup"><span data-stu-id="7de38-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="7de38-277">I filtri precedenti possono essere applicati a un controller o a un metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="7de38-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="7de38-278">I logger sono disponibili tramite l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="7de38-278">Loggers are available from DI.</span></span> <span data-ttu-id="7de38-279">Evitare tuttavia di creare e usare filtri esclusivamente per scopi di registrazione.</span><span class="sxs-lookup"><span data-stu-id="7de38-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="7de38-280">La funzionalità di [registrazione del framework predefinita](xref:fundamentals/logging/index) in genere fornisce il necessario per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="7de38-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="7de38-281">La registrazione aggiunta ai filtri:</span><span class="sxs-lookup"><span data-stu-id="7de38-281">Logging added to filters:</span></span>

* <span data-ttu-id="7de38-282">Deve concentrarsi su problemi del dominio di business o comportamenti specifici del filtro.</span><span class="sxs-lookup"><span data-stu-id="7de38-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="7de38-283">**Non** deve registrare azioni o altri eventi del framework.</span><span class="sxs-lookup"><span data-stu-id="7de38-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="7de38-284">Le azioni di log dei filtri incorporati e gli eventi del Framework.</span><span class="sxs-lookup"><span data-stu-id="7de38-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="7de38-285">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="7de38-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="7de38-286">I tipi di implementazione del filtro di servizi sono registrati in `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7de38-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="7de38-287"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera un'istanza del filtro dall'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="7de38-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="7de38-288">Il codice seguente illustra `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="7de38-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="7de38-289">Nel codice seguente l'oggetto `AddHeaderResultServiceFilter` viene aggiunto al contenitore di inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="7de38-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="7de38-290">Nel codice seguente l'attributo `ServiceFilter` recupera un'istanza del filtro `AddHeaderResultServiceFilter` dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="7de38-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="7de38-291">Quando si usa l'impostazione `ServiceFilterAttribute`, [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="7de38-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="7de38-292">Indica che l'istanza del filtro *può* essere riutilizzata all'esterno dell'ambito della richiesta in cui è stata creata.</span><span class="sxs-lookup"><span data-stu-id="7de38-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="7de38-293">Il runtime di ASP.NET Core non garantisce:</span><span class="sxs-lookup"><span data-stu-id="7de38-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="7de38-294">Che venga creata una singola istanza del filtro.</span><span class="sxs-lookup"><span data-stu-id="7de38-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="7de38-295">Che il filtro non verrà richiesto di nuovo dal contenitore di inserimento delle dipendenze in un momento successivo.</span><span class="sxs-lookup"><span data-stu-id="7de38-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="7de38-296">Non si deve usare con un filtro che dipende da servizi con una durata diversa da singleton.</span><span class="sxs-lookup"><span data-stu-id="7de38-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="7de38-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="7de38-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="7de38-298">`IFilterFactory` espone il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> per creare un'istanza <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="7de38-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="7de38-299">`CreateInstance` carica il tipo specificato dall'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="7de38-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="7de38-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="7de38-300">TypeFilterAttribute</span></span>

<span data-ttu-id="7de38-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> è simile a <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, ma il relativo tipo non viene risolto direttamente dal contenitore dell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="7de38-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="7de38-302">Viene creata un'istanza del tipo tramite <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="7de38-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="7de38-303">Poiché i tipi `TypeFilterAttribute` non vengono risolti direttamente dal contenitore di inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="7de38-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="7de38-304">Non è necessario che i tipi a cui viene fatto riferimento tramite `TypeFilterAttribute` siano registrati nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="7de38-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="7de38-305">Le loro dipendenze vengono soddisfatte dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="7de38-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="7de38-306">In via facoltativa, `TypeFilterAttribute` può anche accettare gli argomenti del costruttore per il tipo.</span><span class="sxs-lookup"><span data-stu-id="7de38-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="7de38-307">Quando si usa l'impostazione `TypeFilterAttribute`, [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="7de38-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="7de38-308">Indica che l'istanza del filtro *può* essere riutilizzata all'esterno dell'ambito della richiesta in cui è stata creata.</span><span class="sxs-lookup"><span data-stu-id="7de38-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="7de38-309">Il runtime di ASP.NET Core non fornisce alcuna garanzia che venga creata una singola istanza del filtro.</span><span class="sxs-lookup"><span data-stu-id="7de38-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="7de38-310">Non si deve usare con un filtro che dipende da servizi con una durata diversa da singleton.</span><span class="sxs-lookup"><span data-stu-id="7de38-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="7de38-311">L'esempio seguente illustra come passare argomenti a un tipo usando `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="7de38-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="7de38-312">Filtri autorizzazione</span><span class="sxs-lookup"><span data-stu-id="7de38-312">Authorization filters</span></span>

<span data-ttu-id="7de38-313">I filtri di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="7de38-313">Authorization filters:</span></span>

* <span data-ttu-id="7de38-314">Sono i primi filtri a essere eseguiti nella pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="7de38-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="7de38-315">Controllano l'accesso ai metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-315">Control access to action methods.</span></span>
* <span data-ttu-id="7de38-316">Dispongono di un metodo precedente, ma non di un metodo successivo.</span><span class="sxs-lookup"><span data-stu-id="7de38-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="7de38-317">I filtri di autorizzazione personalizzati richiedono un framework di autorizzazione personalizzato.</span><span class="sxs-lookup"><span data-stu-id="7de38-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="7de38-318">È preferibile configurare criteri di autorizzazione o scrivere criteri di autorizzazione personalizzati piuttosto che scrivere un filtro personalizzato.</span><span class="sxs-lookup"><span data-stu-id="7de38-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="7de38-319">Il filtro di autorizzazione predefinito:</span><span class="sxs-lookup"><span data-stu-id="7de38-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="7de38-320">Chiama il sistema di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="7de38-320">Calls the authorization system.</span></span>
* <span data-ttu-id="7de38-321">Non autorizza le richieste.</span><span class="sxs-lookup"><span data-stu-id="7de38-321">Does not authorize requests.</span></span>

<span data-ttu-id="7de38-322">**Non** generare eccezioni nei filtri di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="7de38-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="7de38-323">L'eccezione non verrà gestita.</span><span class="sxs-lookup"><span data-stu-id="7de38-323">The exception will not be handled.</span></span>
* <span data-ttu-id="7de38-324">I filtri di eccezione non gestiranno l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="7de38-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="7de38-325">È consigliabile emettere una richiesta di verifica in caso di eccezione in un filtro di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="7de38-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="7de38-326">Altre informazioni sull'[autorizzazione](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="7de38-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="7de38-327">Filtri risorse</span><span class="sxs-lookup"><span data-stu-id="7de38-327">Resource filters</span></span>

<span data-ttu-id="7de38-328">I filtri di risorse:</span><span class="sxs-lookup"><span data-stu-id="7de38-328">Resource filters:</span></span>

* <span data-ttu-id="7de38-329">Implementano l'interfaccia <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="7de38-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="7de38-330">L'esecuzione racchiude la maggior parte della pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="7de38-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="7de38-331">Solo i [filtri di autorizzazione](#authorization-filters) vengono eseguiti prima dei filtri di risorse.</span><span class="sxs-lookup"><span data-stu-id="7de38-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="7de38-332">I filtri di risorse sono utili per causare il corto circuito della maggior parte della pipeline.</span><span class="sxs-lookup"><span data-stu-id="7de38-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="7de38-333">Un filtro di memorizzazione nella cache può, ad esempio, evitare il resto della pipeline in caso di riscontro nella cache.</span><span class="sxs-lookup"><span data-stu-id="7de38-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="7de38-334">Esempi di filtri di risorse:</span><span class="sxs-lookup"><span data-stu-id="7de38-334">Resource filter examples:</span></span>

* <span data-ttu-id="7de38-335">Il [filtro di risorse di corto circuito](#short-circuiting-resource-filter) illustrato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="7de38-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="7de38-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="7de38-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="7de38-337">Impedisce all'associazione di modelli di accedere ai dati del modulo.</span><span class="sxs-lookup"><span data-stu-id="7de38-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="7de38-338">Viene usato quando si caricano file di grandi dimensioni per impedire la lettura dei dati del modulo in memoria.</span><span class="sxs-lookup"><span data-stu-id="7de38-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="7de38-339">Filtri azione</span><span class="sxs-lookup"><span data-stu-id="7de38-339">Action filters</span></span>

<span data-ttu-id="7de38-340">I filtri azione **non** si applicano alle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="7de38-340">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="7de38-341">Pages supporta <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="7de38-341"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="7de38-342">Per ulteriori informazioni, vedere [metodi di filtro per le Razor pagine](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="7de38-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="7de38-343">I filtri di azione:</span><span class="sxs-lookup"><span data-stu-id="7de38-343">Action filters:</span></span>

* <span data-ttu-id="7de38-344">Implementano l'interfaccia <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="7de38-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="7de38-345">La loro esecuzione circonda l'esecuzione dei metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="7de38-346">Il codice seguente mostra un filtro di azione di esempio:</span><span class="sxs-lookup"><span data-stu-id="7de38-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="7de38-347">La classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> specifica le proprietà seguenti:</span><span class="sxs-lookup"><span data-stu-id="7de38-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="7de38-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>: consente di leggere gli input in un metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="7de38-349"><xref:Microsoft.AspNetCore.Mvc.Controller>: consente di modificare l'istanza del controller.</span><span class="sxs-lookup"><span data-stu-id="7de38-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="7de38-350"><xref:System.Web.Mvc.ActionExecutingContext.Result>: l'impostazione di `Result` causa il corto circuito del metodo di azione e dei filtri di azione successivi.</span><span class="sxs-lookup"><span data-stu-id="7de38-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="7de38-351">La generazione di un'eccezione in un metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="7de38-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="7de38-352">Impedisce l'esecuzione dei filtri successivi.</span><span class="sxs-lookup"><span data-stu-id="7de38-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="7de38-353">A differenza dell'impostazione di `Result`, è considerata un errore anziché un risultato positivo.</span><span class="sxs-lookup"><span data-stu-id="7de38-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="7de38-354">La classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> specifica `Controller` e `Result` oltre alle proprietà seguenti:</span><span class="sxs-lookup"><span data-stu-id="7de38-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="7de38-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>: true se un altro file ha causato il corto circuito dell'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="7de38-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception>: non Null se l'azione o un filtro di azione eseguito in precedenza ha generato un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="7de38-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="7de38-357">Se si imposta questa proprietà su Null:</span><span class="sxs-lookup"><span data-stu-id="7de38-357">Setting this property to null:</span></span>

  * <span data-ttu-id="7de38-358">L'eccezione viene gestita in modo efficace.</span><span class="sxs-lookup"><span data-stu-id="7de38-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="7de38-359">L'oggetto `Result` viene eseguito come se fosse stato restituito dal metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="7de38-360">Per un oggetto `IAsyncActionFilter`, una chiamata a <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="7de38-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="7de38-361">Esegue qualsiasi filtro azione successivo e il metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="7de38-362">Restituisce `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="7de38-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="7de38-363">Per causare il corto circuito, assegnare <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a un'istanza del risultato e non chiamare `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="7de38-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="7de38-364">Il framework fornisce un oggetto <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> astratto che è possibile impostare come sottoclasse.</span><span class="sxs-lookup"><span data-stu-id="7de38-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="7de38-365">Il filtro di azione `OnActionExecuting` può essere usato per:</span><span class="sxs-lookup"><span data-stu-id="7de38-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="7de38-366">Convalidare lo stato del modello.</span><span class="sxs-lookup"><span data-stu-id="7de38-366">Validate model state.</span></span>
* <span data-ttu-id="7de38-367">Restituire un errore se lo stato non è valido.</span><span class="sxs-lookup"><span data-stu-id="7de38-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="7de38-368">I controller annotati con l' `[ApiController]` attributo convalidano automaticamente lo stato del modello e restituiscono una risposta 400.</span><span class="sxs-lookup"><span data-stu-id="7de38-368">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="7de38-369">Per altre informazioni, vedere [Risposte HTTP 400 automatiche](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="7de38-369">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="7de38-370">Il metodo `OnActionExecuted` viene eseguito dopo il metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="7de38-370">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="7de38-371">È possibile visualizzare e modificare i risultati dell'azione tramite la proprietà <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="7de38-371">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="7de38-372">L'impostazione di <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> è true se un altro filtro ha causato il corto circuito dell'esecuzione dell'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-372"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="7de38-373">L'impostazione di <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> è un valore non Null se l'azione o un filtro di azione successivo ha generato un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="7de38-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="7de38-374">Impostazione di `Exception` su null:</span><span class="sxs-lookup"><span data-stu-id="7de38-374">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="7de38-375">Gestisce un'eccezione in modo efficace.</span><span class="sxs-lookup"><span data-stu-id="7de38-375">Effectively handles an exception.</span></span>
  * <span data-ttu-id="7de38-376">L'oggetto `ActionExecutedContext.Result` viene eseguito come se fosse stato restituito normalmente dal metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-376">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="7de38-377">Filtri eccezioni</span><span class="sxs-lookup"><span data-stu-id="7de38-377">Exception filters</span></span>

<span data-ttu-id="7de38-378">Filtri eccezioni:</span><span class="sxs-lookup"><span data-stu-id="7de38-378">Exception filters:</span></span>

* <span data-ttu-id="7de38-379">Implementano <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="7de38-379">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="7de38-380">Possono essere usati per implementare criteri di gestione degli errori comuni.</span><span class="sxs-lookup"><span data-stu-id="7de38-380">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="7de38-381">Il filtro di eccezione di esempio seguente usa una visualizzazione degli errori personalizzata per mostrare i dettagli sulle eccezioni che si verificano quando l'app è in fase di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="7de38-381">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="7de38-382">Il codice seguente verifica il filtro eccezioni:</span><span class="sxs-lookup"><span data-stu-id="7de38-382">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="7de38-383">Filtri eccezioni:</span><span class="sxs-lookup"><span data-stu-id="7de38-383">Exception filters:</span></span>

* <span data-ttu-id="7de38-384">Non dispone di eventi precedenti o successivi.</span><span class="sxs-lookup"><span data-stu-id="7de38-384">Don't have before and after events.</span></span>
* <span data-ttu-id="7de38-385">Implementano <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="7de38-385">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="7de38-386">Gestire le eccezioni non gestite che si verificano nella Razor creazione della pagina o del controller, dell' [associazione di modelli](xref:mvc/models/model-binding), dei filtri azione o dei metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-386">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="7de38-387">**Non** rilevano le eccezioni che si verificano nei filtri di risorse, nei filtri dei risultati o nell'esecuzione dei risultati MVC.</span><span class="sxs-lookup"><span data-stu-id="7de38-387">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="7de38-388">Per gestire un'eccezione, impostare la proprietà <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> su `true` o scrivere una risposta.</span><span class="sxs-lookup"><span data-stu-id="7de38-388">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="7de38-389">In questo modo si arresta la propagazione dell'eccezione.</span><span class="sxs-lookup"><span data-stu-id="7de38-389">This stops propagation of the exception.</span></span> <span data-ttu-id="7de38-390">Un filtro di eccezione non può modificare un'eccezione in una "operazione riuscita".</span><span class="sxs-lookup"><span data-stu-id="7de38-390">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="7de38-391">Questa operazione può essere eseguita solo tramite un filtro di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-391">Only an action filter can do that.</span></span>

<span data-ttu-id="7de38-392">Filtri eccezioni:</span><span class="sxs-lookup"><span data-stu-id="7de38-392">Exception filters:</span></span>

* <span data-ttu-id="7de38-393">Sono ideali per intercettare le eccezioni che si verificano nelle azioni.</span><span class="sxs-lookup"><span data-stu-id="7de38-393">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="7de38-394">Non sono flessibili quanto il middleware di gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="7de38-394">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="7de38-395">Scegliere il middleware per la gestione delle eccezioni.</span><span class="sxs-lookup"><span data-stu-id="7de38-395">Prefer middleware for exception handling.</span></span> <span data-ttu-id="7de38-396">Usare i filtri di eccezione solo quando la gestione degli errori *varia* in base al metodo di azione chiamato.</span><span class="sxs-lookup"><span data-stu-id="7de38-396">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="7de38-397">Un'app può ad esempio avere metodi di azione sia per gli endpoint API che per le visualizzazioni/HTML.</span><span class="sxs-lookup"><span data-stu-id="7de38-397">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="7de38-398">Gli endpoint dell'API possono restituire informazioni sull'errore come JSON, mentre le azioni basate sulla visualizzazione possono restituire una pagina di errore in formato HTML.</span><span class="sxs-lookup"><span data-stu-id="7de38-398">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="7de38-399">Filtri risultato</span><span class="sxs-lookup"><span data-stu-id="7de38-399">Result filters</span></span>

<span data-ttu-id="7de38-400">I filtri dei risultati:</span><span class="sxs-lookup"><span data-stu-id="7de38-400">Result filters:</span></span>

* <span data-ttu-id="7de38-401">Implementare un'interfaccia:</span><span class="sxs-lookup"><span data-stu-id="7de38-401">Implement an interface:</span></span>
  * <span data-ttu-id="7de38-402"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="7de38-402"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="7de38-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="7de38-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="7de38-404">La loro esecuzione circonda l'esecuzione dei risultati dell'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-404">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="7de38-405">IResultFilter e IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="7de38-405">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="7de38-406">Il codice seguente illustra un filtro dei risultati che aggiunge un'intestazione HTTP:</span><span class="sxs-lookup"><span data-stu-id="7de38-406">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="7de38-407">Il tipo di risultato eseguito dipende dall'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-407">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="7de38-408">Un'azione che restituisce una visualizzazione include tutte le operazioni di elaborazione Razor come parte dell' <xref:Microsoft.AspNetCore.Mvc.ViewResult> oggetto in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="7de38-408">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="7de38-409">Un metodo API può eseguire la serializzazione in quanto parte dell'esecuzione del risultato.</span><span class="sxs-lookup"><span data-stu-id="7de38-409">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="7de38-410">Altre informazioni sui [risultati dell'azione](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="7de38-410">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="7de38-411">I filtri dei risultati vengono eseguiti solo quando un filtro azione o azione produce un risultato di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-411">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="7de38-412">I filtri dei risultati non vengono eseguiti nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="7de38-412">Result filters are not executed when:</span></span>

* <span data-ttu-id="7de38-413">Un filtro di autorizzazione o un filtro risorse cortocircui la pipeline.</span><span class="sxs-lookup"><span data-stu-id="7de38-413">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="7de38-414">Un filtro di eccezione non gestisca un'eccezione producendo un risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-414">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="7de38-415">Il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> può causare il corto circuito dell'esecuzione del risultato dell'azione e dei filtri dei risultati successivi se si imposta <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> su `true`.</span><span class="sxs-lookup"><span data-stu-id="7de38-415">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="7de38-416">In caso di corto circuito, scrivere nell'oggetto risposta per evitare di generare una risposta vuota.</span><span class="sxs-lookup"><span data-stu-id="7de38-416">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="7de38-417">Generazione di un'eccezione in `IResultFilter.OnResultExecuting` :</span><span class="sxs-lookup"><span data-stu-id="7de38-417">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="7de38-418">Impedisce l'esecuzione del risultato dell'azione e dei filtri successivi.</span><span class="sxs-lookup"><span data-stu-id="7de38-418">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="7de38-419">Viene considerato un errore anziché un risultato positivo.</span><span class="sxs-lookup"><span data-stu-id="7de38-419">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="7de38-420">Quando <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> viene eseguito il metodo, è probabile che la risposta sia già stata inviata al client.</span><span class="sxs-lookup"><span data-stu-id="7de38-420">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="7de38-421">Se la risposta è già stata inviata al client, non è possibile modificarla.</span><span class="sxs-lookup"><span data-stu-id="7de38-421">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="7de38-422">L'impostazione di `ResultExecutedContext.Canceled` è `true` se un altro filtro ha causato il corto circuito dell'esecuzione del risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-422">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="7de38-423">L'impostazione di `ResultExecutedContext.Exception` è un valore non Null se il risultato dell'azione o un filtro dei risultati successivo ha generato un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="7de38-423">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="7de38-424">`Exception`L'impostazione di su null consente di gestire in modo efficace un'eccezione e impedisce che l'eccezione venga generata nuovamente in un secondo momento nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="7de38-424">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="7de38-425">Non c'è alcun modo affidabile per scrivere i dati in una risposta quando si gestisce un'eccezione in un filtro dei risultati.</span><span class="sxs-lookup"><span data-stu-id="7de38-425">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="7de38-426">Se le intestazioni sono state scaricate nel client quando il risultato di un'azione genera un'eccezione, non c'è alcun meccanismo affidabile per inviare un codice di errore.</span><span class="sxs-lookup"><span data-stu-id="7de38-426">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="7de38-427">Per un oggetto <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, una chiamata a `await next` in <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> esegue tutti i filtri dei risultati successivi e il risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-427">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="7de38-428">Per causare il corto circuito, impostare [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) su `true` e non chiamare `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="7de38-428">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="7de38-429">Il framework fornisce un oggetto `ResultFilterAttribute` astratto che è possibile impostare come sottoclasse.</span><span class="sxs-lookup"><span data-stu-id="7de38-429">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="7de38-430">La classe [AddHeaderAttribute](#add-header-attribute) illustrata in precedenza è un esempio di un attributo di filtro dei risultati.</span><span class="sxs-lookup"><span data-stu-id="7de38-430">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="7de38-431">IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="7de38-431">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="7de38-432">Le interfacce <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> dichiarano un'implementazione di <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> eseguita per tutti i risultati dell'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-432">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="7de38-433">Sono inclusi i risultati dell'azione prodotti da:</span><span class="sxs-lookup"><span data-stu-id="7de38-433">This includes action results produced by:</span></span>

* <span data-ttu-id="7de38-434">Filtri di autorizzazione e filtri delle risorse che interessano il cortocircuito.</span><span class="sxs-lookup"><span data-stu-id="7de38-434">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="7de38-435">Filtri eccezioni.</span><span class="sxs-lookup"><span data-stu-id="7de38-435">Exception filters.</span></span>

<span data-ttu-id="7de38-436">Ad esempio, il filtro seguente viene eseguito sempre e imposta un risultato dell'azione (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) con un codice di stato *422 Entità non elaborabile* quando la negoziazione del contenuto ha esito negativo:</span><span class="sxs-lookup"><span data-stu-id="7de38-436">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="7de38-437">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="7de38-437">IFilterFactory</span></span>

<span data-ttu-id="7de38-438"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="7de38-438"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="7de38-439">Pertanto, un'istanza di `IFilterFactory` può essere usata come un'istanza di `IFilterMetadata` in un punto qualsiasi della pipeline filtro.</span><span class="sxs-lookup"><span data-stu-id="7de38-439">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="7de38-440">Quando il runtime si prepara per richiamare il filtro, cerca di eseguirne il cast a un oggetto `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="7de38-440">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="7de38-441">Se l'esecuzione del cast ha esito positivo, viene chiamato il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> per creare l'istanza di `IFilterMetadata` richiamata.</span><span class="sxs-lookup"><span data-stu-id="7de38-441">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="7de38-442">In questo modo viene specificata una struttura flessibile, poiché non è necessario impostare in modo esplicito la pipeline filtro all'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="7de38-442">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="7de38-443">Un altro approccio alla creazione di filtri consiste nell'implementare `IFilterFactory` usando implementazioni dell'attributo personalizzate:</span><span class="sxs-lookup"><span data-stu-id="7de38-443">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="7de38-444">Il filtro viene applicato nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="7de38-444">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="7de38-445">Testare il codice precedente eseguendo l' [esempio di download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="7de38-445">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="7de38-446">Richiamare gli strumenti di sviluppo F12.</span><span class="sxs-lookup"><span data-stu-id="7de38-446">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="7de38-447">Accedere a `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="7de38-447">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="7de38-448">Gli strumenti di sviluppo F12 visualizzano le intestazioni di risposta seguenti aggiunte dal codice di esempio:</span><span class="sxs-lookup"><span data-stu-id="7de38-448">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="7de38-449">**Autore:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="7de38-449">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="7de38-450">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="7de38-450">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="7de38-451">**interno:**`My header`</span><span class="sxs-lookup"><span data-stu-id="7de38-451">**internal:** `My header`</span></span>

<span data-ttu-id="7de38-452">Il codice precedente crea l'intestazione di risposta **internal:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="7de38-452">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="7de38-453">Implementazione di IFilterFactory in un attributo</span><span class="sxs-lookup"><span data-stu-id="7de38-453">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="7de38-454">I filtri che implementano `IFilterFactory` sono utili per i filtri che:</span><span class="sxs-lookup"><span data-stu-id="7de38-454">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="7de38-455">Non richiedono il passaggio di parametri.</span><span class="sxs-lookup"><span data-stu-id="7de38-455">Don't require passing parameters.</span></span>
* <span data-ttu-id="7de38-456">Hanno dipendenze del costruttore che devono essere soddisfatte dall'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="7de38-456">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="7de38-457"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="7de38-457"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="7de38-458">`IFilterFactory` espone il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> per creare un'istanza <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="7de38-458">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="7de38-459">`CreateInstance` carica il tipo specificato dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="7de38-459">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="7de38-460">Il codice seguente illustra tre approcci all'applicazione di `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="7de38-460">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="7de38-461">Nel codice precedente la decorazione del metodo con `[SampleActionFilter]` è l'approccio da preferire per applicare `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="7de38-461">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="7de38-462">Uso di middleware nella pipeline filtro</span><span class="sxs-lookup"><span data-stu-id="7de38-462">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="7de38-463">I filtri risorse funzionano come [middleware](xref:fundamentals/middleware/index) in quanto racchiudono l'esecuzione di tutto ciò che viene dopo nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="7de38-463">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="7de38-464">Tuttavia, i filtri differiscono dal middleware in quanto fanno parte del runtime, il che significa che hanno accesso a contesto e costrutti.</span><span class="sxs-lookup"><span data-stu-id="7de38-464">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="7de38-465">Per usare il middleware come filtro, creare un tipo con un metodo `Configure` che specifica il middleware da inserire nella pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="7de38-465">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="7de38-466">L'esempio seguente usa il middleware di localizzazione per stabilire le impostazioni cultura correnti per una richiesta:</span><span class="sxs-lookup"><span data-stu-id="7de38-466">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="7de38-467">Usare <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> per eseguire il middleware:</span><span class="sxs-lookup"><span data-stu-id="7de38-467">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="7de38-468">I filtri middleware vengono eseguiti nella stessa fase della pipeline filtro come filtri risorse, prima dell'associazione di modelli e dopo il resto della pipeline.</span><span class="sxs-lookup"><span data-stu-id="7de38-468">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="7de38-469">Azioni successive</span><span class="sxs-lookup"><span data-stu-id="7de38-469">Next actions</span></span>

* <span data-ttu-id="7de38-470">Vedere [metodi di filtro per le Razor pagine](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="7de38-470">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="7de38-471">Per sperimentare i filtri, [scaricare, testare e modificare l'esempio di GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="7de38-471">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7de38-472">Di [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="7de38-472">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="7de38-473">I *filtri* in ASP.NET Core consentono l'esecuzione del codice prima o dopo fasi specifiche della pipeline di elaborazione della richiesta.</span><span class="sxs-lookup"><span data-stu-id="7de38-473">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="7de38-474">I filtri predefiniti gestiscono attività, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7de38-474">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="7de38-475">Autorizzazione (impedire l'accesso alle risorse per cui un utente non è autorizzato).</span><span class="sxs-lookup"><span data-stu-id="7de38-475">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="7de38-476">Memorizzazione nella cache delle risposte (blocco della pipeline delle richieste per restituire una risposta memorizzata nella cache).</span><span class="sxs-lookup"><span data-stu-id="7de38-476">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="7de38-477">I filtri personalizzati possono essere creati per gestire problemi relativi a più settori.</span><span class="sxs-lookup"><span data-stu-id="7de38-477">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="7de38-478">Esempi di problematiche trasversali includono la gestione degli errori, la memorizzazione nella cache, la configurazione, l'autorizzazione e la registrazione.</span><span class="sxs-lookup"><span data-stu-id="7de38-478">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="7de38-479">I filtri evitano la duplicazione del codice.</span><span class="sxs-lookup"><span data-stu-id="7de38-479">Filters avoid duplicating code.</span></span> <span data-ttu-id="7de38-480">Ad esempio, un filtro eccezioni per la gestione degli errori potrebbe consolidare la gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="7de38-480">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="7de38-481">Questo documento si applica a Razor pagine, controller API e controller con visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="7de38-481">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="7de38-482">[Visualizzare o scaricare un esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="7de38-482">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="7de38-483">Funzionamento dei filtri</span><span class="sxs-lookup"><span data-stu-id="7de38-483">How filters work</span></span>

<span data-ttu-id="7de38-484">I filtri vengono eseguiti all'interno della *pipeline di chiamata di azioni ASP.NET Core*, talvolta definita *pipeline di filtro*.</span><span class="sxs-lookup"><span data-stu-id="7de38-484">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="7de38-485">La pipeline di filtro viene eseguita dopo che ASP.NET Core ha selezionato l'azione da eseguire.</span><span class="sxs-lookup"><span data-stu-id="7de38-485">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![La richiesta viene elaborata tramite altro middleware, il middleware di routing, la selezione dell'azione e la pipeline di chiamata di azioni ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="7de38-488">Tipi di filtro</span><span class="sxs-lookup"><span data-stu-id="7de38-488">Filter types</span></span>

<span data-ttu-id="7de38-489">Ogni tipo di filtro viene eseguito in una fase diversa della pipeline di filtro:</span><span class="sxs-lookup"><span data-stu-id="7de38-489">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="7de38-490">I [filtri di autorizzazione](#authorization-filters) vengono eseguiti per primi e consentono di determinare se l'utente è autorizzato per la richiesta.</span><span class="sxs-lookup"><span data-stu-id="7de38-490">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="7de38-491">I filtri di autorizzazione causano il corto circuito della pipeline se la richiesta non è autorizzata.</span><span class="sxs-lookup"><span data-stu-id="7de38-491">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="7de38-492">[Filtri risorse](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="7de38-492">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="7de38-493">Vengono eseguiti dopo l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="7de38-493">Run after authorization.</span></span>  
  * <span data-ttu-id="7de38-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> può eseguire codice prima del resto della pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="7de38-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="7de38-495">Ad esempio, `OnResourceExecuting` può eseguire codice prima dell'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="7de38-495">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="7de38-496"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> può eseguire codice dopo che il resto della pipeline è stato completato.</span><span class="sxs-lookup"><span data-stu-id="7de38-496"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="7de38-497">I [filtri azione](#action-filters) possono eseguire codice immediatamente prima e dopo la chiamata di un metodo di azione singolo.</span><span class="sxs-lookup"><span data-stu-id="7de38-497">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="7de38-498">Possono essere usati per modificare gli argomenti passati a un'azione e il risultato restituito dall'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-498">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="7de38-499">I filtri azione **non** sono supportati nelle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="7de38-499">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="7de38-500">I [filtri eccezioni](#exception-filters) vengono usati per applicare i criteri globali a eccezioni non gestite che si verificano prima che qualsiasi elemento sia stato scritto nel corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="7de38-500">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="7de38-501">I [filtri risultato](#result-filters) possono eseguire codice immediatamente prima e dopo l'esecuzione di risultati di azioni singole.</span><span class="sxs-lookup"><span data-stu-id="7de38-501">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="7de38-502">Vengono eseguiti solo quando il metodo di azione è stato eseguito correttamente.</span><span class="sxs-lookup"><span data-stu-id="7de38-502">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="7de38-503">Sono utili per la logica che deve racchiudere l'esecuzione del formattatore o di una vista.</span><span class="sxs-lookup"><span data-stu-id="7de38-503">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="7de38-504">Il diagramma seguente illustra come interagiscono i tipi di filtro nella pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="7de38-504">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![La richiesta passa attraverso i filtri autorizzazione, i filtri risorse, l'associazione di modelli, i filtri azione, l'esecuzione dell'azione e la conversione del risultato dell'azione, i filtri eccezioni, i filtri risultato e l'esecuzione del risultato.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="7de38-507">Implementazione</span><span class="sxs-lookup"><span data-stu-id="7de38-507">Implementation</span></span>

<span data-ttu-id="7de38-508">I filtri supportano sia implementazioni sincrone che asincrone tramite definizioni di interfaccia diverse.</span><span class="sxs-lookup"><span data-stu-id="7de38-508">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="7de38-509">I filtri sincroni possono eseguire codice prima (`On-Stage-Executing`) e dopo (`On-Stage-Executed`) la relativa fase della pipeline.</span><span class="sxs-lookup"><span data-stu-id="7de38-509">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="7de38-510">La chiamata di `OnActionExecuting`, ad esempio, avviene prima della chiamata del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-510">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="7de38-511">La chiamata di `OnActionExecuted` avviene dopo l'esecuzione del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-511">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="7de38-512">I filtri asincroni definiscono un metodo `On-Stage-ExecutionAsync`:</span><span class="sxs-lookup"><span data-stu-id="7de38-512">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="7de38-513">Nel codice precedente `SampleAsyncActionFilter` ha un oggetto <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) che esegue il metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-513">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="7de38-514">Ognuno dei metodi `On-Stage-ExecutionAsync` accetta un oggetto `FilterType-ExecutionDelegate` che esegue la fase della pipeline del filtro.</span><span class="sxs-lookup"><span data-stu-id="7de38-514">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="7de38-515">Fasi di filtro multiple</span><span class="sxs-lookup"><span data-stu-id="7de38-515">Multiple filter stages</span></span>

<span data-ttu-id="7de38-516">È possibile implementare interfacce per più fasi di filtro in una singola classe.</span><span class="sxs-lookup"><span data-stu-id="7de38-516">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="7de38-517">Ad esempio, la classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementa `IActionFilter`, `IResultFilter` e i relativi equivalenti asincroni.</span><span class="sxs-lookup"><span data-stu-id="7de38-517">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="7de38-518">Implementare la versione sincrona **oppure** la versione asincrona di un'interfaccia di filtro, **non** entrambe.</span><span class="sxs-lookup"><span data-stu-id="7de38-518">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="7de38-519">Il runtime controlla per prima cosa se il filtro implementa l'interfaccia asincrona e, in tal caso, la chiama.</span><span class="sxs-lookup"><span data-stu-id="7de38-519">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="7de38-520">In caso contrario, chiama i metodi dell'interfaccia sincrona.</span><span class="sxs-lookup"><span data-stu-id="7de38-520">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="7de38-521">Se in una classe sono implementate entrambe le interfacce, sincrona e asincrona, viene chiamato solo il metodo asincrono.</span><span class="sxs-lookup"><span data-stu-id="7de38-521">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="7de38-522">Quando si usano classi astratte come <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> eseguire l'override solo dei metodi sincroni o del metodo asincrono per ogni tipo di filtro.</span><span class="sxs-lookup"><span data-stu-id="7de38-522">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="7de38-523">Attributi filtro predefiniti</span><span class="sxs-lookup"><span data-stu-id="7de38-523">Built-in filter attributes</span></span>

<span data-ttu-id="7de38-524">ASP.NET Core include filtri predefiniti basati su attributi che è possibile impostare come sottoclasse e personalizzare.</span><span class="sxs-lookup"><span data-stu-id="7de38-524">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="7de38-525">Il filtro dei risultati seguente, ad esempio, aggiunge un'intestazione alla risposta:</span><span class="sxs-lookup"><span data-stu-id="7de38-525">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="7de38-526">Gli attributi consentono ai filtri di accettare argomenti, come illustrato nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="7de38-526">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="7de38-527">Applicare `AddHeaderAttribute` a un controller o a un metodo di azione e specificare il nome e il valore dell'intestazione HTTP:</span><span class="sxs-lookup"><span data-stu-id="7de38-527">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="7de38-528">Molte delle interfacce del filtro presentano attributi corrispondenti che possono essere usati come classi di base per implementazioni personalizzate.</span><span class="sxs-lookup"><span data-stu-id="7de38-528">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="7de38-529">Attributi dei filtri:</span><span class="sxs-lookup"><span data-stu-id="7de38-529">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="7de38-530">Ambiti dei filtri e ordine di esecuzione</span><span class="sxs-lookup"><span data-stu-id="7de38-530">Filter scopes and order of execution</span></span>

<span data-ttu-id="7de38-531">È possibile aggiungere un filtro alla pipeline in uno dei tre *ambiti*:</span><span class="sxs-lookup"><span data-stu-id="7de38-531">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="7de38-532">Usando un attributo di un'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-532">Using an attribute on an action.</span></span>
* <span data-ttu-id="7de38-533">Usando un attributo di un controller.</span><span class="sxs-lookup"><span data-stu-id="7de38-533">Using an attribute on a controller.</span></span>
* <span data-ttu-id="7de38-534">A livello globale per tutti i controller e le azioni come illustrato nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="7de38-534">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="7de38-535">Il codice precedente aggiunge tre filtri a livello globale tramite la raccolta [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters).</span><span class="sxs-lookup"><span data-stu-id="7de38-535">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="7de38-536">Ordine di esecuzione predefinito</span><span class="sxs-lookup"><span data-stu-id="7de38-536">Default order of execution</span></span>

<span data-ttu-id="7de38-537">Quando sono presenti più filtri *dello stesso tipo*, scope determina l'ordine predefinito di esecuzione del filtro.</span><span class="sxs-lookup"><span data-stu-id="7de38-537">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="7de38-538">Filtri globali circondano i filtri di classe.</span><span class="sxs-lookup"><span data-stu-id="7de38-538">Global filters surround class filters.</span></span> <span data-ttu-id="7de38-539">Filtri di classe filtri di metodo Racchiudi.</span><span class="sxs-lookup"><span data-stu-id="7de38-539">Class filters surround method filters.</span></span>

<span data-ttu-id="7de38-540">Come risultato dell'annidamento dei filtri, il codice *after* dei filtri viene eseguito in ordine inverso rispetto al codice *before*.</span><span class="sxs-lookup"><span data-stu-id="7de38-540">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="7de38-541">Sequenza di filtro:</span><span class="sxs-lookup"><span data-stu-id="7de38-541">The filter sequence:</span></span>

* <span data-ttu-id="7de38-542">Codice *before* dei filtri globali.</span><span class="sxs-lookup"><span data-stu-id="7de38-542">The *before* code of global filters.</span></span>
  * <span data-ttu-id="7de38-543">Codice *before* dei filtri del controller.</span><span class="sxs-lookup"><span data-stu-id="7de38-543">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="7de38-544">Codice *before* dei filtri del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-544">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="7de38-545">Codice *after* dei filtri del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-545">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="7de38-546">Codice *after* dei filtri del controller.</span><span class="sxs-lookup"><span data-stu-id="7de38-546">The *after* code of controller filters.</span></span>
* <span data-ttu-id="7de38-547">Codice *after* dei filtri globali.</span><span class="sxs-lookup"><span data-stu-id="7de38-547">The *after* code of global filters.</span></span>
  
<span data-ttu-id="7de38-548">L'esempio seguente illustra l'ordine in cui i metodi dei filtri vengono chiamati per i filtri di azione sincroni.</span><span class="sxs-lookup"><span data-stu-id="7de38-548">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="7de38-549">Sequenza</span><span class="sxs-lookup"><span data-stu-id="7de38-549">Sequence</span></span> | <span data-ttu-id="7de38-550">Ambito del filtro</span><span class="sxs-lookup"><span data-stu-id="7de38-550">Filter scope</span></span> | <span data-ttu-id="7de38-551">Metodo del filtro</span><span class="sxs-lookup"><span data-stu-id="7de38-551">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="7de38-552">1</span><span class="sxs-lookup"><span data-stu-id="7de38-552">1</span></span> | <span data-ttu-id="7de38-553">Globale</span><span class="sxs-lookup"><span data-stu-id="7de38-553">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7de38-554">2</span><span class="sxs-lookup"><span data-stu-id="7de38-554">2</span></span> | <span data-ttu-id="7de38-555">Controller</span><span class="sxs-lookup"><span data-stu-id="7de38-555">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7de38-556">3</span><span class="sxs-lookup"><span data-stu-id="7de38-556">3</span></span> | <span data-ttu-id="7de38-557">Metodo</span><span class="sxs-lookup"><span data-stu-id="7de38-557">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7de38-558">4</span><span class="sxs-lookup"><span data-stu-id="7de38-558">4</span></span> | <span data-ttu-id="7de38-559">Metodo</span><span class="sxs-lookup"><span data-stu-id="7de38-559">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="7de38-560">5</span><span class="sxs-lookup"><span data-stu-id="7de38-560">5</span></span> | <span data-ttu-id="7de38-561">Controller</span><span class="sxs-lookup"><span data-stu-id="7de38-561">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="7de38-562">6</span><span class="sxs-lookup"><span data-stu-id="7de38-562">6</span></span> | <span data-ttu-id="7de38-563">Globale</span><span class="sxs-lookup"><span data-stu-id="7de38-563">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="7de38-564">Questa sequenza mostra che:</span><span class="sxs-lookup"><span data-stu-id="7de38-564">This sequence shows:</span></span>

* <span data-ttu-id="7de38-565">Il filtro del metodo è annidato all'interno del filtro del controller.</span><span class="sxs-lookup"><span data-stu-id="7de38-565">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="7de38-566">Il filtro del controller è annidato all'interno del filtro globale.</span><span class="sxs-lookup"><span data-stu-id="7de38-566">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="7de38-567">Filtri a Razor livello di pagina e controller</span><span class="sxs-lookup"><span data-stu-id="7de38-567">Controller and Razor Page level filters</span></span>

<span data-ttu-id="7de38-568">Ogni controller che eredita dalla classe di base <xref:Microsoft.AspNetCore.Mvc.Controller> include i metodi [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) e [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="7de38-568">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="7de38-569">Questi metodi:</span><span class="sxs-lookup"><span data-stu-id="7de38-569">These methods:</span></span>

* <span data-ttu-id="7de38-570">Eseguono il wrapping dei filtri che vengono eseguiti per una determinata azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-570">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="7de38-571">La chiamata di `OnActionExecuting` avviene prima di qualsiasi filtro dell'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-571">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="7de38-572">La chiamata di `OnActionExecuted` avviene dopo tutti i filtri dell'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-572">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="7de38-573">La chiamata di `OnActionExecutionAsync` avviene prima di qualsiasi filtro dell'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-573">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="7de38-574">Il codice del filtro dopo `next` viene eseguito dopo il metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-574">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="7de38-575">Ad esempio, l'applicazione di `MySampleActionFilter` nell'esempio scaricato avviene a livello globale all'avvio.</span><span class="sxs-lookup"><span data-stu-id="7de38-575">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="7de38-576">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="7de38-576">The `TestController`:</span></span>

* <span data-ttu-id="7de38-577">Applica `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) all' `FilterTest2` azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-577">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="7de38-578">Esegue l'override di `OnActionExecuting` e `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="7de38-578">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="7de38-579">Passando a `https://localhost:5001/Test/FilterTest2`, viene eseguito il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="7de38-579">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="7de38-580">Per le Razor pagine, vedere [implementare Razor filtri di pagina eseguendo l'override dei metodi di filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="7de38-580">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="7de38-581">Override dell'ordine predefinito</span><span class="sxs-lookup"><span data-stu-id="7de38-581">Overriding the default order</span></span>

<span data-ttu-id="7de38-582">È possibile eseguire l'override della sequenza di esecuzione predefinita implementando <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="7de38-582">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="7de38-583">`IOrderedFilter` espone la proprietà <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order>, che ha la precedenza sull'ambito per determinare l'ordine di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="7de38-583">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="7de38-584">Un filtro con un valore di `Order` inferiore:</span><span class="sxs-lookup"><span data-stu-id="7de38-584">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="7de38-585">Esegue il codice *before* prima di quello di un filtro con un valore di `Order` più alto.</span><span class="sxs-lookup"><span data-stu-id="7de38-585">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="7de38-586">Esegue il codice *after* dopo quello di un filtro con un valore di `Order` più alto.</span><span class="sxs-lookup"><span data-stu-id="7de38-586">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="7de38-587">La proprietà `Order` può essere impostata con un parametro del costruttore:</span><span class="sxs-lookup"><span data-stu-id="7de38-587">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="7de38-588">Prendere in considerazione gli stessi tre filtri di azione illustrati nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="7de38-588">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="7de38-589">Se la proprietà `Order` del controller e dei filtri globali è impostata, rispettivamente, su 1 e su 2, l'ordine di esecuzione viene invertito.</span><span class="sxs-lookup"><span data-stu-id="7de38-589">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="7de38-590">Sequenza</span><span class="sxs-lookup"><span data-stu-id="7de38-590">Sequence</span></span> | <span data-ttu-id="7de38-591">Ambito del filtro</span><span class="sxs-lookup"><span data-stu-id="7de38-591">Filter scope</span></span> | <span data-ttu-id="7de38-592">Proprietà `Order`</span><span class="sxs-lookup"><span data-stu-id="7de38-592">`Order` property</span></span> | <span data-ttu-id="7de38-593">Metodo del filtro</span><span class="sxs-lookup"><span data-stu-id="7de38-593">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="7de38-594">1</span><span class="sxs-lookup"><span data-stu-id="7de38-594">1</span></span> | <span data-ttu-id="7de38-595">Metodo</span><span class="sxs-lookup"><span data-stu-id="7de38-595">Method</span></span> | <span data-ttu-id="7de38-596">0</span><span class="sxs-lookup"><span data-stu-id="7de38-596">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7de38-597">2</span><span class="sxs-lookup"><span data-stu-id="7de38-597">2</span></span> | <span data-ttu-id="7de38-598">Controller</span><span class="sxs-lookup"><span data-stu-id="7de38-598">Controller</span></span> | <span data-ttu-id="7de38-599">1</span><span class="sxs-lookup"><span data-stu-id="7de38-599">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="7de38-600">3</span><span class="sxs-lookup"><span data-stu-id="7de38-600">3</span></span> | <span data-ttu-id="7de38-601">Globale</span><span class="sxs-lookup"><span data-stu-id="7de38-601">Global</span></span> | <span data-ttu-id="7de38-602">2</span><span class="sxs-lookup"><span data-stu-id="7de38-602">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="7de38-603">4</span><span class="sxs-lookup"><span data-stu-id="7de38-603">4</span></span> | <span data-ttu-id="7de38-604">Globale</span><span class="sxs-lookup"><span data-stu-id="7de38-604">Global</span></span> | <span data-ttu-id="7de38-605">2</span><span class="sxs-lookup"><span data-stu-id="7de38-605">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="7de38-606">5</span><span class="sxs-lookup"><span data-stu-id="7de38-606">5</span></span> | <span data-ttu-id="7de38-607">Controller</span><span class="sxs-lookup"><span data-stu-id="7de38-607">Controller</span></span> | <span data-ttu-id="7de38-608">1</span><span class="sxs-lookup"><span data-stu-id="7de38-608">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="7de38-609">6</span><span class="sxs-lookup"><span data-stu-id="7de38-609">6</span></span> | <span data-ttu-id="7de38-610">Metodo</span><span class="sxs-lookup"><span data-stu-id="7de38-610">Method</span></span> | <span data-ttu-id="7de38-611">0</span><span class="sxs-lookup"><span data-stu-id="7de38-611">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="7de38-612">La proprietà `Order` ha la precedenza sull'ambito nel determinare l'ordine di esecuzione dei filtri.</span><span class="sxs-lookup"><span data-stu-id="7de38-612">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="7de38-613">I filtri vengono ordinati prima in base all'ordine, poi viene usato l'ambito per interrompere i collegamenti.</span><span class="sxs-lookup"><span data-stu-id="7de38-613">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="7de38-614">Tutti i filtri predefiniti implementano `IOrderedFilter` e impostano il valore predefinito di `Order` su 0.</span><span class="sxs-lookup"><span data-stu-id="7de38-614">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="7de38-615">Per i filtri predefiniti, l'ambito determina l'ordine, a meno che non si imposti `Order` su un valore diverso da zero.</span><span class="sxs-lookup"><span data-stu-id="7de38-615">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="7de38-616">Annullamento e corto circuito</span><span class="sxs-lookup"><span data-stu-id="7de38-616">Cancellation and short-circuiting</span></span>

<span data-ttu-id="7de38-617">È possibile causare il corto circuito della pipeline di filtro impostando la proprietà <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> sul parametro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fornito al metodo di filtro.</span><span class="sxs-lookup"><span data-stu-id="7de38-617">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="7de38-618">Ad esempio, il filtro di risorse seguente impedisce l'esecuzione del resto della pipeline:</span><span class="sxs-lookup"><span data-stu-id="7de38-618">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="7de38-619">Nel codice seguente sia il filtro `ShortCircuitingResourceFilter` che il filtro `AddHeader` hanno come destinazione il metodo di azione `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="7de38-619">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="7de38-620">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="7de38-620">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="7de38-621">Viene eseguito per primo perché è un filtro risorsa e `AddHeader` è un filtro azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-621">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="7de38-622">Blocca il resto della pipeline.</span><span class="sxs-lookup"><span data-stu-id="7de38-622">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="7de38-623">Pertanto il filtro `AddHeader` non viene mai eseguito per l'azione `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="7de38-623">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="7de38-624">Questo comportamento sarebbe lo stesso se entrambi i filtri venissero applicati a livello di metodo di azione, a condizione che il filtro `ShortCircuitingResourceFilter` venga eseguito prima.</span><span class="sxs-lookup"><span data-stu-id="7de38-624">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="7de38-625">`ShortCircuitingResourceFilter` viene eseguito per primo per il tipo di filtro o per l'uso esplicito della proprietà `Order`.</span><span class="sxs-lookup"><span data-stu-id="7de38-625">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="7de38-626">Inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="7de38-626">Dependency injection</span></span>

<span data-ttu-id="7de38-627">È possibile aggiungere filtri per tipo o per istanza.</span><span class="sxs-lookup"><span data-stu-id="7de38-627">Filters can be added by type or by instance.</span></span> <span data-ttu-id="7de38-628">Se viene aggiunta un'istanza, tale istanza viene usata per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="7de38-628">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="7de38-629">Se viene aggiunto un tipo, il filtro è attivato dal tipo.</span><span class="sxs-lookup"><span data-stu-id="7de38-629">If a type is added, it's type-activated.</span></span> <span data-ttu-id="7de38-630">Un filtro attivato dal tipo comporta:</span><span class="sxs-lookup"><span data-stu-id="7de38-630">A type-activated filter means:</span></span>

* <span data-ttu-id="7de38-631">La creazione di un'istanza per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="7de38-631">An instance is created for each request.</span></span>
* <span data-ttu-id="7de38-632">Il popolamento di tutte le dipendenze del costruttore tramite [inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7de38-632">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="7de38-633">I filtri implementati come attributi e aggiunti direttamente alle classi controller o ai metodi di azione non possono avere dipendenze costruttore specificate dall'[inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7de38-633">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="7de38-634">Le dipendenze del costruttore non possono essere fornite tramite inserimento delle dipendenze in quanto:</span><span class="sxs-lookup"><span data-stu-id="7de38-634">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="7de38-635">I parametri del costruttore devono essere forniti agli attributi nella posizione in cui vengono applicati.</span><span class="sxs-lookup"><span data-stu-id="7de38-635">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="7de38-636">Questa è una limitazione del funzionamento degli attributi.</span><span class="sxs-lookup"><span data-stu-id="7de38-636">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="7de38-637">I filtri seguenti supportano le dipendenze del costruttore fornite dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="7de38-637">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="7de38-638"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementato nell'attributo.</span><span class="sxs-lookup"><span data-stu-id="7de38-638"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="7de38-639">I filtri precedenti possono essere applicati a un controller o a un metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="7de38-639">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="7de38-640">I logger sono disponibili tramite l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="7de38-640">Loggers are available from DI.</span></span> <span data-ttu-id="7de38-641">Evitare tuttavia di creare e usare filtri esclusivamente per scopi di registrazione.</span><span class="sxs-lookup"><span data-stu-id="7de38-641">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="7de38-642">La funzionalità di [registrazione del framework predefinita](xref:fundamentals/logging/index) in genere fornisce il necessario per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="7de38-642">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="7de38-643">La registrazione aggiunta ai filtri:</span><span class="sxs-lookup"><span data-stu-id="7de38-643">Logging added to filters:</span></span>

* <span data-ttu-id="7de38-644">Deve concentrarsi su problemi del dominio di business o comportamenti specifici del filtro.</span><span class="sxs-lookup"><span data-stu-id="7de38-644">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="7de38-645">**Non** deve registrare azioni o altri eventi del framework.</span><span class="sxs-lookup"><span data-stu-id="7de38-645">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="7de38-646">I filtri predefiniti registrano azioni ed eventi del framework.</span><span class="sxs-lookup"><span data-stu-id="7de38-646">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="7de38-647">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="7de38-647">ServiceFilterAttribute</span></span>

<span data-ttu-id="7de38-648">I tipi di implementazione del filtro di servizi sono registrati in `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7de38-648">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="7de38-649"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera un'istanza del filtro dall'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="7de38-649">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="7de38-650">Il codice seguente illustra `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="7de38-650">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="7de38-651">Nel codice seguente l'oggetto `AddHeaderResultServiceFilter` viene aggiunto al contenitore di inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="7de38-651">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="7de38-652">Nel codice seguente l'attributo `ServiceFilter` recupera un'istanza del filtro `AddHeaderResultServiceFilter` dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="7de38-652">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="7de38-653">Quando si usa l'impostazione `ServiceFilterAttribute`, [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="7de38-653">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="7de38-654">Indica che l'istanza del filtro *può* essere riutilizzata all'esterno dell'ambito della richiesta in cui è stata creata.</span><span class="sxs-lookup"><span data-stu-id="7de38-654">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="7de38-655">Il runtime di ASP.NET Core non garantisce:</span><span class="sxs-lookup"><span data-stu-id="7de38-655">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="7de38-656">Che venga creata una singola istanza del filtro.</span><span class="sxs-lookup"><span data-stu-id="7de38-656">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="7de38-657">Che il filtro non verrà richiesto di nuovo dal contenitore di inserimento delle dipendenze in un momento successivo.</span><span class="sxs-lookup"><span data-stu-id="7de38-657">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="7de38-658">Non si deve usare con un filtro che dipende da servizi con una durata diversa da singleton.</span><span class="sxs-lookup"><span data-stu-id="7de38-658">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="7de38-659"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="7de38-659"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="7de38-660">`IFilterFactory` espone il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> per creare un'istanza <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="7de38-660">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="7de38-661">`CreateInstance` carica il tipo specificato dall'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="7de38-661">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="7de38-662">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="7de38-662">TypeFilterAttribute</span></span>

<span data-ttu-id="7de38-663"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> è simile a <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, ma il relativo tipo non viene risolto direttamente dal contenitore dell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="7de38-663"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="7de38-664">Viene creata un'istanza del tipo tramite <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="7de38-664">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="7de38-665">Poiché i tipi `TypeFilterAttribute` non vengono risolti direttamente dal contenitore di inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="7de38-665">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="7de38-666">Non è necessario che i tipi a cui viene fatto riferimento tramite `TypeFilterAttribute` siano registrati nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="7de38-666">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="7de38-667">Le loro dipendenze vengono soddisfatte dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="7de38-667">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="7de38-668">In via facoltativa, `TypeFilterAttribute` può anche accettare gli argomenti del costruttore per il tipo.</span><span class="sxs-lookup"><span data-stu-id="7de38-668">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="7de38-669">Quando si usa l'impostazione `TypeFilterAttribute`, [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="7de38-669">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="7de38-670">Indica che l'istanza del filtro *può* essere riutilizzata all'esterno dell'ambito della richiesta in cui è stata creata.</span><span class="sxs-lookup"><span data-stu-id="7de38-670">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="7de38-671">Il runtime di ASP.NET Core non fornisce alcuna garanzia che venga creata una singola istanza del filtro.</span><span class="sxs-lookup"><span data-stu-id="7de38-671">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="7de38-672">Non si deve usare con un filtro che dipende da servizi con una durata diversa da singleton.</span><span class="sxs-lookup"><span data-stu-id="7de38-672">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="7de38-673">L'esempio seguente illustra come passare argomenti a un tipo usando `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="7de38-673">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="7de38-674">Filtri autorizzazione</span><span class="sxs-lookup"><span data-stu-id="7de38-674">Authorization filters</span></span>

<span data-ttu-id="7de38-675">I filtri di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="7de38-675">Authorization filters:</span></span>

* <span data-ttu-id="7de38-676">Sono i primi filtri a essere eseguiti nella pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="7de38-676">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="7de38-677">Controllano l'accesso ai metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-677">Control access to action methods.</span></span>
* <span data-ttu-id="7de38-678">Dispongono di un metodo precedente, ma non di un metodo successivo.</span><span class="sxs-lookup"><span data-stu-id="7de38-678">Have a before method, but no after method.</span></span>

<span data-ttu-id="7de38-679">I filtri di autorizzazione personalizzati richiedono un framework di autorizzazione personalizzato.</span><span class="sxs-lookup"><span data-stu-id="7de38-679">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="7de38-680">È preferibile configurare criteri di autorizzazione o scrivere criteri di autorizzazione personalizzati piuttosto che scrivere un filtro personalizzato.</span><span class="sxs-lookup"><span data-stu-id="7de38-680">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="7de38-681">Il filtro di autorizzazione predefinito:</span><span class="sxs-lookup"><span data-stu-id="7de38-681">The built-in authorization filter:</span></span>

* <span data-ttu-id="7de38-682">Chiama il sistema di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="7de38-682">Calls the authorization system.</span></span>
* <span data-ttu-id="7de38-683">Non autorizza le richieste.</span><span class="sxs-lookup"><span data-stu-id="7de38-683">Does not authorize requests.</span></span>

<span data-ttu-id="7de38-684">**Non** generare eccezioni nei filtri di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="7de38-684">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="7de38-685">L'eccezione non verrà gestita.</span><span class="sxs-lookup"><span data-stu-id="7de38-685">The exception will not be handled.</span></span>
* <span data-ttu-id="7de38-686">I filtri di eccezione non gestiranno l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="7de38-686">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="7de38-687">È consigliabile emettere una richiesta di verifica in caso di eccezione in un filtro di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="7de38-687">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="7de38-688">Altre informazioni sull'[autorizzazione](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="7de38-688">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="7de38-689">Filtri risorse</span><span class="sxs-lookup"><span data-stu-id="7de38-689">Resource filters</span></span>

<span data-ttu-id="7de38-690">I filtri di risorse:</span><span class="sxs-lookup"><span data-stu-id="7de38-690">Resource filters:</span></span>

* <span data-ttu-id="7de38-691">Implementano l'interfaccia <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="7de38-691">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="7de38-692">L'esecuzione racchiude la maggior parte della pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="7de38-692">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="7de38-693">Solo i [filtri di autorizzazione](#authorization-filters) vengono eseguiti prima dei filtri di risorse.</span><span class="sxs-lookup"><span data-stu-id="7de38-693">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="7de38-694">I filtri di risorse sono utili per causare il corto circuito della maggior parte della pipeline.</span><span class="sxs-lookup"><span data-stu-id="7de38-694">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="7de38-695">Un filtro di memorizzazione nella cache può, ad esempio, evitare il resto della pipeline in caso di riscontro nella cache.</span><span class="sxs-lookup"><span data-stu-id="7de38-695">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="7de38-696">Esempi di filtri di risorse:</span><span class="sxs-lookup"><span data-stu-id="7de38-696">Resource filter examples:</span></span>

* <span data-ttu-id="7de38-697">Il [filtro di risorse di corto circuito](#short-circuiting-resource-filter) illustrato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="7de38-697">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="7de38-698">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="7de38-698">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="7de38-699">Impedisce all'associazione di modelli di accedere ai dati del modulo.</span><span class="sxs-lookup"><span data-stu-id="7de38-699">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="7de38-700">Viene usato quando si caricano file di grandi dimensioni per impedire la lettura dei dati del modulo in memoria.</span><span class="sxs-lookup"><span data-stu-id="7de38-700">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="7de38-701">Filtri azione</span><span class="sxs-lookup"><span data-stu-id="7de38-701">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7de38-702">I filtri azione **non** si applicano alle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="7de38-702">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="7de38-703">Pages supporta <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="7de38-703"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="7de38-704">Per ulteriori informazioni, vedere [metodi di filtro per le Razor pagine](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="7de38-704">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="7de38-705">I filtri di azione:</span><span class="sxs-lookup"><span data-stu-id="7de38-705">Action filters:</span></span>

* <span data-ttu-id="7de38-706">Implementano l'interfaccia <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="7de38-706">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="7de38-707">La loro esecuzione circonda l'esecuzione dei metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-707">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="7de38-708">Il codice seguente mostra un filtro di azione di esempio:</span><span class="sxs-lookup"><span data-stu-id="7de38-708">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="7de38-709">La classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> specifica le proprietà seguenti:</span><span class="sxs-lookup"><span data-stu-id="7de38-709">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="7de38-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>: consente di leggere gli input per un metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="7de38-711"><xref:Microsoft.AspNetCore.Mvc.Controller>: consente di modificare l'istanza del controller.</span><span class="sxs-lookup"><span data-stu-id="7de38-711"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="7de38-712"><xref:System.Web.Mvc.ActionExecutingContext.Result>: l'impostazione di `Result` causa il corto circuito del metodo di azione e dei filtri di azione successivi.</span><span class="sxs-lookup"><span data-stu-id="7de38-712"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="7de38-713">La generazione di un'eccezione in un metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="7de38-713">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="7de38-714">Impedisce l'esecuzione dei filtri successivi.</span><span class="sxs-lookup"><span data-stu-id="7de38-714">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="7de38-715">A differenza dell'impostazione di `Result`, è considerata un errore anziché un risultato positivo.</span><span class="sxs-lookup"><span data-stu-id="7de38-715">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="7de38-716">La classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> specifica `Controller` e `Result` oltre alle proprietà seguenti:</span><span class="sxs-lookup"><span data-stu-id="7de38-716">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="7de38-717"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>: true se un altro file ha causato il corto circuito dell'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-717"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="7de38-718"><xref:System.Web.Mvc.ActionExecutedContext.Exception>: non Null se l'azione o un filtro di azione eseguito in precedenza ha generato un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="7de38-718"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="7de38-719">Se si imposta questa proprietà su Null:</span><span class="sxs-lookup"><span data-stu-id="7de38-719">Setting this property to null:</span></span>

  * <span data-ttu-id="7de38-720">L'eccezione viene gestita in modo efficace.</span><span class="sxs-lookup"><span data-stu-id="7de38-720">Effectively handles the exception.</span></span>
  * <span data-ttu-id="7de38-721">L'oggetto `Result` viene eseguito come se fosse stato restituito dal metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-721">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="7de38-722">Per un oggetto `IAsyncActionFilter`, una chiamata a <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="7de38-722">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="7de38-723">Esegue qualsiasi filtro azione successivo e il metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-723">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="7de38-724">Restituisce `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="7de38-724">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="7de38-725">Per causare il corto circuito, assegnare <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a un'istanza del risultato e non chiamare `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="7de38-725">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="7de38-726">Il framework fornisce un oggetto <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> astratto che è possibile impostare come sottoclasse.</span><span class="sxs-lookup"><span data-stu-id="7de38-726">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="7de38-727">Il filtro di azione `OnActionExecuting` può essere usato per:</span><span class="sxs-lookup"><span data-stu-id="7de38-727">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="7de38-728">Convalidare lo stato del modello.</span><span class="sxs-lookup"><span data-stu-id="7de38-728">Validate model state.</span></span>
* <span data-ttu-id="7de38-729">Restituire un errore se lo stato non è valido.</span><span class="sxs-lookup"><span data-stu-id="7de38-729">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="7de38-730">Il metodo `OnActionExecuted` viene eseguito dopo il metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="7de38-730">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="7de38-731">È possibile visualizzare e modificare i risultati dell'azione tramite la proprietà <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="7de38-731">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="7de38-732">L'impostazione di <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> è true se un altro filtro ha causato il corto circuito dell'esecuzione dell'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-732"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="7de38-733">L'impostazione di <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> è un valore non Null se l'azione o un filtro di azione successivo ha generato un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="7de38-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="7de38-734">Impostazione di `Exception` su null:</span><span class="sxs-lookup"><span data-stu-id="7de38-734">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="7de38-735">Gestisce un'eccezione in modo efficace.</span><span class="sxs-lookup"><span data-stu-id="7de38-735">Effectively handles an exception.</span></span>
  * <span data-ttu-id="7de38-736">L'oggetto `ActionExecutedContext.Result` viene eseguito come se fosse stato restituito normalmente dal metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-736">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="7de38-737">Filtri eccezioni</span><span class="sxs-lookup"><span data-stu-id="7de38-737">Exception filters</span></span>

<span data-ttu-id="7de38-738">Filtri eccezioni:</span><span class="sxs-lookup"><span data-stu-id="7de38-738">Exception filters:</span></span>

* <span data-ttu-id="7de38-739">Implementano <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="7de38-739">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="7de38-740">Possono essere usati per implementare criteri di gestione degli errori comuni.</span><span class="sxs-lookup"><span data-stu-id="7de38-740">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="7de38-741">Il filtro di eccezione di esempio seguente usa una visualizzazione degli errori personalizzata per mostrare i dettagli sulle eccezioni che si verificano quando l'app è in fase di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="7de38-741">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="7de38-742">Filtri eccezioni:</span><span class="sxs-lookup"><span data-stu-id="7de38-742">Exception filters:</span></span>

* <span data-ttu-id="7de38-743">Non dispone di eventi precedenti o successivi.</span><span class="sxs-lookup"><span data-stu-id="7de38-743">Don't have before and after events.</span></span>
* <span data-ttu-id="7de38-744">Implementano <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="7de38-744">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="7de38-745">Gestire le eccezioni non gestite che si verificano nella Razor creazione della pagina o del controller, dell' [associazione di modelli](xref:mvc/models/model-binding), dei filtri azione o dei metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-745">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="7de38-746">**Non** rilevano le eccezioni che si verificano nei filtri di risorse, nei filtri dei risultati o nell'esecuzione dei risultati MVC.</span><span class="sxs-lookup"><span data-stu-id="7de38-746">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="7de38-747">Per gestire un'eccezione, impostare la proprietà <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> su `true` o scrivere una risposta.</span><span class="sxs-lookup"><span data-stu-id="7de38-747">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="7de38-748">In questo modo si arresta la propagazione dell'eccezione.</span><span class="sxs-lookup"><span data-stu-id="7de38-748">This stops propagation of the exception.</span></span> <span data-ttu-id="7de38-749">Un filtro di eccezione non può modificare un'eccezione in una "operazione riuscita".</span><span class="sxs-lookup"><span data-stu-id="7de38-749">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="7de38-750">Questa operazione può essere eseguita solo tramite un filtro di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-750">Only an action filter can do that.</span></span>

<span data-ttu-id="7de38-751">Filtri eccezioni:</span><span class="sxs-lookup"><span data-stu-id="7de38-751">Exception filters:</span></span>

* <span data-ttu-id="7de38-752">Sono ideali per intercettare le eccezioni che si verificano nelle azioni.</span><span class="sxs-lookup"><span data-stu-id="7de38-752">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="7de38-753">Non sono flessibili quanto il middleware di gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="7de38-753">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="7de38-754">Scegliere il middleware per la gestione delle eccezioni.</span><span class="sxs-lookup"><span data-stu-id="7de38-754">Prefer middleware for exception handling.</span></span> <span data-ttu-id="7de38-755">Usare i filtri di eccezione solo quando la gestione degli errori *varia* in base al metodo di azione chiamato.</span><span class="sxs-lookup"><span data-stu-id="7de38-755">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="7de38-756">Un'app può ad esempio avere metodi di azione sia per gli endpoint API che per le visualizzazioni/HTML.</span><span class="sxs-lookup"><span data-stu-id="7de38-756">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="7de38-757">Gli endpoint dell'API possono restituire informazioni sull'errore come JSON, mentre le azioni basate sulla visualizzazione possono restituire una pagina di errore in formato HTML.</span><span class="sxs-lookup"><span data-stu-id="7de38-757">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="7de38-758">Filtri risultato</span><span class="sxs-lookup"><span data-stu-id="7de38-758">Result filters</span></span>

<span data-ttu-id="7de38-759">I filtri dei risultati:</span><span class="sxs-lookup"><span data-stu-id="7de38-759">Result filters:</span></span>

* <span data-ttu-id="7de38-760">Implementare un'interfaccia:</span><span class="sxs-lookup"><span data-stu-id="7de38-760">Implement an interface:</span></span>
  * <span data-ttu-id="7de38-761"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="7de38-761"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="7de38-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="7de38-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="7de38-763">La loro esecuzione circonda l'esecuzione dei risultati dell'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-763">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="7de38-764">IResultFilter e IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="7de38-764">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="7de38-765">Il codice seguente illustra un filtro dei risultati che aggiunge un'intestazione HTTP:</span><span class="sxs-lookup"><span data-stu-id="7de38-765">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="7de38-766">Il tipo di risultato eseguito dipende dall'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-766">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="7de38-767">Un'azione che restituisce una visualizzazione include tutte le elaborazioni Razor come parte dell'elemento <xref:Microsoft.AspNetCore.Mvc.ViewResult> eseguito.</span><span class="sxs-lookup"><span data-stu-id="7de38-767">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="7de38-768">Un metodo API può eseguire la serializzazione in quanto parte dell'esecuzione del risultato.</span><span class="sxs-lookup"><span data-stu-id="7de38-768">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="7de38-769">Altre informazioni sui [risultati dell'azione](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="7de38-769">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="7de38-770">I filtri dei risultati vengono eseguiti solo quando un filtro azione o azione produce un risultato di azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-770">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="7de38-771">I filtri dei risultati non vengono eseguiti nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="7de38-771">Result filters are not executed when:</span></span>

* <span data-ttu-id="7de38-772">Un filtro di autorizzazione o un filtro risorse cortocircui la pipeline.</span><span class="sxs-lookup"><span data-stu-id="7de38-772">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="7de38-773">Un filtro di eccezione non gestisca un'eccezione producendo un risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-773">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="7de38-774">Il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> può causare il corto circuito dell'esecuzione del risultato dell'azione e dei filtri dei risultati successivi se si imposta <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> su `true`.</span><span class="sxs-lookup"><span data-stu-id="7de38-774">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="7de38-775">In caso di corto circuito, scrivere nell'oggetto risposta per evitare di generare una risposta vuota.</span><span class="sxs-lookup"><span data-stu-id="7de38-775">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="7de38-776">La generazione di un'eccezione in `IResultFilter.OnResultExecuting`:</span><span class="sxs-lookup"><span data-stu-id="7de38-776">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="7de38-777">Impedisce l'esecuzione del risultato dell'azione e dei filtri successivi.</span><span class="sxs-lookup"><span data-stu-id="7de38-777">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="7de38-778">È considerata un errore anziché un risultato positivo.</span><span class="sxs-lookup"><span data-stu-id="7de38-778">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="7de38-779">Quando <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> viene eseguito il metodo, è probabile che la risposta sia già stata inviata al client.</span><span class="sxs-lookup"><span data-stu-id="7de38-779">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="7de38-780">Se la risposta è già stata inviata al client, non è possibile modificarla ulteriormente.</span><span class="sxs-lookup"><span data-stu-id="7de38-780">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="7de38-781">L'impostazione di `ResultExecutedContext.Canceled` è `true` se un altro filtro ha causato il corto circuito dell'esecuzione del risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-781">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="7de38-782">L'impostazione di `ResultExecutedContext.Exception` è un valore non Null se il risultato dell'azione o un filtro dei risultati successivo ha generato un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="7de38-782">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="7de38-783">Se si imposta `Exception` su Null l'eccezione viene gestita in modo efficace e si evita che venga generata nuovamente da ASP.NET Core in un punto successivo della pipeline.</span><span class="sxs-lookup"><span data-stu-id="7de38-783">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="7de38-784">Non c'è alcun modo affidabile per scrivere i dati in una risposta quando si gestisce un'eccezione in un filtro dei risultati.</span><span class="sxs-lookup"><span data-stu-id="7de38-784">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="7de38-785">Se le intestazioni sono state scaricate nel client quando il risultato di un'azione genera un'eccezione, non c'è alcun meccanismo affidabile per inviare un codice di errore.</span><span class="sxs-lookup"><span data-stu-id="7de38-785">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="7de38-786">Per un oggetto <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, una chiamata a `await next` in <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> esegue tutti i filtri dei risultati successivi e il risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-786">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="7de38-787">Per causare il corto circuito, impostare [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) su `true` e non chiamare `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="7de38-787">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="7de38-788">Il framework fornisce un oggetto `ResultFilterAttribute` astratto che è possibile impostare come sottoclasse.</span><span class="sxs-lookup"><span data-stu-id="7de38-788">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="7de38-789">La classe [AddHeaderAttribute](#add-header-attribute) illustrata in precedenza è un esempio di un attributo di filtro dei risultati.</span><span class="sxs-lookup"><span data-stu-id="7de38-789">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="7de38-790">IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="7de38-790">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="7de38-791">Le interfacce <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> dichiarano un'implementazione di <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> eseguita per tutti i risultati dell'azione.</span><span class="sxs-lookup"><span data-stu-id="7de38-791">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="7de38-792">Sono inclusi i risultati dell'azione prodotti da:</span><span class="sxs-lookup"><span data-stu-id="7de38-792">This includes action results produced by:</span></span>

* <span data-ttu-id="7de38-793">Filtri di autorizzazione e filtri delle risorse che interessano il cortocircuito.</span><span class="sxs-lookup"><span data-stu-id="7de38-793">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="7de38-794">Filtri eccezioni.</span><span class="sxs-lookup"><span data-stu-id="7de38-794">Exception filters.</span></span>

<span data-ttu-id="7de38-795">Ad esempio, il filtro seguente viene eseguito sempre e imposta un risultato dell'azione (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) con un codice di stato *422 Entità non elaborabile* quando la negoziazione del contenuto ha esito negativo:</span><span class="sxs-lookup"><span data-stu-id="7de38-795">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="7de38-796">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="7de38-796">IFilterFactory</span></span>

<span data-ttu-id="7de38-797"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="7de38-797"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="7de38-798">Pertanto, un'istanza di `IFilterFactory` può essere usata come un'istanza di `IFilterMetadata` in un punto qualsiasi della pipeline filtro.</span><span class="sxs-lookup"><span data-stu-id="7de38-798">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="7de38-799">Quando il runtime si prepara per richiamare il filtro, cerca di eseguirne il cast a un oggetto `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="7de38-799">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="7de38-800">Se l'esecuzione del cast ha esito positivo, viene chiamato il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> per creare l'istanza di `IFilterMetadata` richiamata.</span><span class="sxs-lookup"><span data-stu-id="7de38-800">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="7de38-801">In questo modo viene specificata una struttura flessibile, poiché non è necessario impostare in modo esplicito la pipeline filtro all'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="7de38-801">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="7de38-802">Un altro approccio alla creazione di filtri consiste nell'implementare `IFilterFactory` usando implementazioni dell'attributo personalizzate:</span><span class="sxs-lookup"><span data-stu-id="7de38-802">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="7de38-803">Il codice precedente può essere testato eseguendo l'[esempio scaricato](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="7de38-803">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="7de38-804">Richiamare gli strumenti di sviluppo F12.</span><span class="sxs-lookup"><span data-stu-id="7de38-804">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="7de38-805">Accedere a `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="7de38-805">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="7de38-806">Gli strumenti di sviluppo F12 visualizzano le intestazioni di risposta seguenti aggiunte dal codice di esempio:</span><span class="sxs-lookup"><span data-stu-id="7de38-806">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="7de38-807">**Autore:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="7de38-807">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="7de38-808">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="7de38-808">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="7de38-809">**interno:**`My header`</span><span class="sxs-lookup"><span data-stu-id="7de38-809">**internal:** `My header`</span></span>

<span data-ttu-id="7de38-810">Il codice precedente crea l'intestazione di risposta **internal:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="7de38-810">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="7de38-811">Implementazione di IFilterFactory in un attributo</span><span class="sxs-lookup"><span data-stu-id="7de38-811">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="7de38-812">I filtri che implementano `IFilterFactory` sono utili per i filtri che:</span><span class="sxs-lookup"><span data-stu-id="7de38-812">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="7de38-813">Non richiedono il passaggio di parametri.</span><span class="sxs-lookup"><span data-stu-id="7de38-813">Don't require passing parameters.</span></span>
* <span data-ttu-id="7de38-814">Hanno dipendenze del costruttore che devono essere soddisfatte dall'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="7de38-814">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="7de38-815"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="7de38-815"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="7de38-816">`IFilterFactory` espone il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> per creare un'istanza <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="7de38-816">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="7de38-817">`CreateInstance` carica il tipo specificato dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="7de38-817">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="7de38-818">Il codice seguente illustra tre approcci all'applicazione di `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="7de38-818">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="7de38-819">Nel codice precedente la decorazione del metodo con `[SampleActionFilter]` è l'approccio da preferire per applicare `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="7de38-819">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="7de38-820">Uso di middleware nella pipeline filtro</span><span class="sxs-lookup"><span data-stu-id="7de38-820">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="7de38-821">I filtri risorse funzionano come [middleware](xref:fundamentals/middleware/index) in quanto racchiudono l'esecuzione di tutto ciò che viene dopo nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="7de38-821">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="7de38-822">I filtri sono diversi dal middleware in quanto fanno parte del runtime di ASP.NET Core, quindi hanno accesso al contesto e ai costrutti di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7de38-822">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="7de38-823">Per usare il middleware come filtro, creare un tipo con un metodo `Configure` che specifica il middleware da inserire nella pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="7de38-823">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="7de38-824">L'esempio seguente usa il middleware di localizzazione per stabilire le impostazioni cultura correnti per una richiesta:</span><span class="sxs-lookup"><span data-stu-id="7de38-824">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="7de38-825">Usare <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> per eseguire il middleware:</span><span class="sxs-lookup"><span data-stu-id="7de38-825">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="7de38-826">I filtri middleware vengono eseguiti nella stessa fase della pipeline filtro come filtri risorse, prima dell'associazione di modelli e dopo il resto della pipeline.</span><span class="sxs-lookup"><span data-stu-id="7de38-826">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="7de38-827">Azioni successive</span><span class="sxs-lookup"><span data-stu-id="7de38-827">Next actions</span></span>

* <span data-ttu-id="7de38-828">Vedere [metodi di filtro per le Razor pagine](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="7de38-828">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="7de38-829">Per sperimentare i filtri, [scaricare, testare e modificare l'esempio di GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="7de38-829">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
