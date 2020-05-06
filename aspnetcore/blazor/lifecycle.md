---
title: Ciclo Blazor di vita ASP.NET Core
author: guardrex
description: Informazioni su come usare Razor i metodi del ciclo di Blazor vita del componente nelle app ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: 87c65776684f9cc91b868b8e88926e46b25592ff
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771520"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="df277-103">Ciclo Blazor di vita ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="df277-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="df277-104">Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="df277-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="df277-105">Il Blazor Framework include metodi del ciclo di vita sincroni e asincroni.</span><span class="sxs-lookup"><span data-stu-id="df277-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="df277-106">Eseguire l'override dei metodi Lifecycle per eseguire operazioni aggiuntive sui componenti durante l'inizializzazione e il rendering dei componenti.</span><span class="sxs-lookup"><span data-stu-id="df277-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="df277-107">Metodi del ciclo di vita</span><span class="sxs-lookup"><span data-stu-id="df277-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="df277-108">Metodi di inizializzazione componenti</span><span class="sxs-lookup"><span data-stu-id="df277-108">Component initialization methods</span></span>

<span data-ttu-id="df277-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> vengono richiamati quando il componente viene inizializzato dopo aver ricevuto i parametri iniziali dal componente padre.</span><span class="sxs-lookup"><span data-stu-id="df277-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="df277-110">Usare `OnInitializedAsync` quando il componente esegue un'operazione asincrona e deve essere aggiornato al termine dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="df277-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="df277-111">Per un'operazione sincrona, `OnInitialized`eseguire l'override di:</span><span class="sxs-lookup"><span data-stu-id="df277-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="df277-112">Per eseguire un'operazione asincrona, eseguire `OnInitializedAsync` l'override di `await` e usare la parola chiave sull'operazione:</span><span class="sxs-lookup"><span data-stu-id="df277-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor<span data-ttu-id="df277-113">App server che [preeseguono la](xref:blazor/hosting-model-configuration#render-mode) chiamata `OnInitializedAsync` **_al contenuto per due volte_**:</span><span class="sxs-lookup"><span data-stu-id="df277-113"> Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="df277-114">Una volta quando il componente viene inizialmente sottoposto a rendering statico come parte della pagina.</span><span class="sxs-lookup"><span data-stu-id="df277-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="df277-115">Una seconda volta quando il browser stabilisce una connessione al server.</span><span class="sxs-lookup"><span data-stu-id="df277-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="df277-116">Per impedire l'esecuzione di `OnInitializedAsync` codice dello sviluppatore per due volte, vedere la sezione [riconnessione con stato dopo il rendering](#stateful-reconnection-after-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="df277-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="df277-117">Mentre un' Blazor app Server è prerendering, alcune azioni, ad esempio la chiamata a JavaScript, non sono possibili perché non è stata stabilita una connessione con il browser.</span><span class="sxs-lookup"><span data-stu-id="df277-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="df277-118">I componenti potrebbero dover eseguire il rendering in modo diverso quando ne viene eseguito il rendering.</span><span class="sxs-lookup"><span data-stu-id="df277-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="df277-119">Per altre informazioni, vedere la sezione [rilevare quando l'app è prerendering](#detect-when-the-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="df277-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="df277-120">Se sono configurati gestori di eventi, rimuoverli a disposizione.</span><span class="sxs-lookup"><span data-stu-id="df277-120">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="df277-121">Per ulteriori informazioni, vedere la sezione relativa all' [eliminazione dei componenti con IDisposable](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="df277-121">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="df277-122">Prima dell'impostazione dei parametri</span><span class="sxs-lookup"><span data-stu-id="df277-122">Before parameters are set</span></span>

<span data-ttu-id="df277-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*>Imposta i parametri forniti dall'elemento padre del componente nell'albero di rendering:</span><span class="sxs-lookup"><span data-stu-id="df277-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="df277-124"><xref:Microsoft.AspNetCore.Components.ParameterView>contiene l'intero set di valori dei parametri ogni `SetParametersAsync` volta che viene chiamato.</span><span class="sxs-lookup"><span data-stu-id="df277-124"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="df277-125">L'implementazione predefinita di `SetParametersAsync` imposta il valore di ogni proprietà con l' `[Parameter]` attributo `[CascadingParameter]` o con un valore corrispondente in `ParameterView`.</span><span class="sxs-lookup"><span data-stu-id="df277-125">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="df277-126">I parametri che non hanno un valore corrispondente `ParameterView` in rimangono invariati.</span><span class="sxs-lookup"><span data-stu-id="df277-126">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="df277-127">Se `base.SetParametersAync` non viene richiamato, il codice personalizzato può interpretare il valore dei parametri in ingresso in qualsiasi modo necessario.</span><span class="sxs-lookup"><span data-stu-id="df277-127">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="df277-128">Ad esempio, non è necessario assegnare i parametri in ingresso alle proprietà della classe.</span><span class="sxs-lookup"><span data-stu-id="df277-128">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="df277-129">Se sono configurati gestori di eventi, rimuoverli a disposizione.</span><span class="sxs-lookup"><span data-stu-id="df277-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="df277-130">Per ulteriori informazioni, vedere la sezione relativa all' [eliminazione dei componenti con IDisposable](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="df277-130">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="df277-131">Parametri after impostati</span><span class="sxs-lookup"><span data-stu-id="df277-131">After parameters are set</span></span>

<span data-ttu-id="df277-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> vengono chiamati:</span><span class="sxs-lookup"><span data-stu-id="df277-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called:</span></span>

* <span data-ttu-id="df277-133">Quando il componente viene inizializzato e ha ricevuto il primo set di parametri dal componente padre.</span><span class="sxs-lookup"><span data-stu-id="df277-133">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="df277-134">Quando il componente padre esegue nuovamente il rendering e fornisce:</span><span class="sxs-lookup"><span data-stu-id="df277-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="df277-135">Solo i tipi non modificabili primitivi noti di cui è stato modificato almeno un parametro.</span><span class="sxs-lookup"><span data-stu-id="df277-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="df277-136">Tutti i parametri tipizzati complessi.</span><span class="sxs-lookup"><span data-stu-id="df277-136">Any complex-typed parameters.</span></span> <span data-ttu-id="df277-137">Il Framework non è in grado di stabilire se i valori di un parametro tipizzato complesso sono stati modificati internamente, quindi considera il set di parametri come modificato.</span><span class="sxs-lookup"><span data-stu-id="df277-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="df277-138">Il lavoro asincrono quando si applicano parametri e valori di proprietà `OnParametersSetAsync` deve verificarsi durante l'evento del ciclo di vita.</span><span class="sxs-lookup"><span data-stu-id="df277-138">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="df277-139">Se sono configurati gestori di eventi, rimuoverli a disposizione.</span><span class="sxs-lookup"><span data-stu-id="df277-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="df277-140">Per ulteriori informazioni, vedere la sezione relativa all' [eliminazione dei componenti con IDisposable](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="df277-140">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="df277-141">Rendering del componente dopo</span><span class="sxs-lookup"><span data-stu-id="df277-141">After component render</span></span>

<span data-ttu-id="df277-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> vengono chiamati dopo che un componente ha terminato il rendering.</span><span class="sxs-lookup"><span data-stu-id="df277-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="df277-143">I riferimenti a elementi e componenti vengono popolati a questo punto.</span><span class="sxs-lookup"><span data-stu-id="df277-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="df277-144">Usare questa fase per eseguire passaggi di inizializzazione aggiuntivi usando il contenuto sottoposto a rendering, ad esempio l'attivazione di librerie JavaScript di terze parti che operano sugli elementi DOM sottoposti a rendering.</span><span class="sxs-lookup"><span data-stu-id="df277-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="df277-145">Il `firstRender` parametro per `OnAfterRenderAsync` e `OnAfterRender`:</span><span class="sxs-lookup"><span data-stu-id="df277-145">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="df277-146">Viene impostato `true` sulla prima volta che viene eseguito il rendering dell'istanza del componente.</span><span class="sxs-lookup"><span data-stu-id="df277-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="df277-147">Può essere usato per garantire che il lavoro di inizializzazione venga eseguito una sola volta.</span><span class="sxs-lookup"><span data-stu-id="df277-147">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="df277-148">Il lavoro asincrono immediatamente dopo il rendering deve verificarsi `OnAfterRenderAsync` durante l'evento del ciclo di vita.</span><span class="sxs-lookup"><span data-stu-id="df277-148">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="df277-149">Anche se si restituisce un <xref:System.Threading.Tasks.Task> oggetto `OnAfterRenderAsync`da, il Framework non pianifica un ulteriore ciclo di rendering per il componente al termine dell'attività.</span><span class="sxs-lookup"><span data-stu-id="df277-149">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="df277-150">In questo modo è possibile evitare un ciclo di rendering infinito.</span><span class="sxs-lookup"><span data-stu-id="df277-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="df277-151">È diverso dagli altri metodi del ciclo di vita, che pianificano un ulteriore ciclo di rendering dopo il completamento dell'attività restituita.</span><span class="sxs-lookup"><span data-stu-id="df277-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="df277-152">`OnAfterRender`e `OnAfterRenderAsync` *non vengono chiamati durante il prerendering sul server.*</span><span class="sxs-lookup"><span data-stu-id="df277-152">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="df277-153">Se sono configurati gestori di eventi, rimuoverli a disposizione.</span><span class="sxs-lookup"><span data-stu-id="df277-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="df277-154">Per ulteriori informazioni, vedere la sezione relativa all' [eliminazione dei componenti con IDisposable](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="df277-154">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="df277-155">Evita aggiornamento dell'interfaccia utente</span><span class="sxs-lookup"><span data-stu-id="df277-155">Suppress UI refreshing</span></span>

<span data-ttu-id="df277-156">Eseguire <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> l'override per impedire l'aggiornamento dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="df277-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="df277-157">Se l'implementazione restituisce `true`, l'interfaccia utente viene aggiornata:</span><span class="sxs-lookup"><span data-stu-id="df277-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="df277-158">`ShouldRender`viene chiamato ogni volta che il componente viene sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="df277-158">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="df277-159">Anche se `ShouldRender` viene sottoposto a override, il componente viene sempre sottoposto a rendering iniziale.</span><span class="sxs-lookup"><span data-stu-id="df277-159">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="df277-160">Modifiche stato</span><span class="sxs-lookup"><span data-stu-id="df277-160">State changes</span></span>

<span data-ttu-id="df277-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>notifica al componente che lo stato è stato modificato.</span><span class="sxs-lookup"><span data-stu-id="df277-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="df277-162">Se applicabile, la `StateHasChanged` chiamata a comporta il rendering del componente.</span><span class="sxs-lookup"><span data-stu-id="df277-162">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="df277-163">Gestisci azioni asincrone incomplete durante il rendering</span><span class="sxs-lookup"><span data-stu-id="df277-163">Handle incomplete async actions at render</span></span>

<span data-ttu-id="df277-164">Le azioni asincrone eseguite negli eventi del ciclo di vita potrebbero non essere state completate prima del rendering del componente.</span><span class="sxs-lookup"><span data-stu-id="df277-164">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="df277-165">Gli oggetti possono `null` essere o compilati in modo non completo con i dati mentre è in esecuzione il metodo del ciclo di vita.</span><span class="sxs-lookup"><span data-stu-id="df277-165">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="df277-166">Fornire la logica di rendering per confermare che gli oggetti vengono inizializzati.</span><span class="sxs-lookup"><span data-stu-id="df277-166">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="df277-167">Esegue il rendering degli elementi dell'interfaccia utente segnaposto (ad esempio, un `null`messaggio di caricamento) mentre gli oggetti sono.</span><span class="sxs-lookup"><span data-stu-id="df277-167">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="df277-168">Nel componente `FetchData` dei Blazor modelli `OnInitializedAsync` viene eseguito l'override di asincrono Receive forecast data (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="df277-168">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="df277-169">Quando `forecasts` è `null`, all'utente viene visualizzato un messaggio di caricamento.</span><span class="sxs-lookup"><span data-stu-id="df277-169">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="df277-170">Quando l' `Task` oggetto restituito `OnInitializedAsync` da viene completato, viene eseguito il rendering del componente con lo stato aggiornato.</span><span class="sxs-lookup"><span data-stu-id="df277-170">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="df277-171">*Pages/fetchData. Razor* nel Blazor modello server:</span><span class="sxs-lookup"><span data-stu-id="df277-171">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="df277-172">Eliminazione di componenti con IDisposable</span><span class="sxs-lookup"><span data-stu-id="df277-172">Component disposal with IDisposable</span></span>

<span data-ttu-id="df277-173">Se un componente implementa <xref:System.IDisposable>, il [metodo Dispose](/dotnet/standard/garbage-collection/implementing-dispose) viene chiamato quando il componente viene rimosso dall'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="df277-173">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="df277-174">Il componente seguente utilizza `@implements IDisposable` e il `Dispose` metodo:</span><span class="sxs-lookup"><span data-stu-id="df277-174">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="df277-175">La <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> chiamata `Dispose` di in non è supportata.</span><span class="sxs-lookup"><span data-stu-id="df277-175">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="df277-176">`StateHasChanged`potrebbe essere richiamato come parte del propagazione del renderer, quindi la richiesta di aggiornamenti dell'interfaccia utente in quel momento non è supportata.</span><span class="sxs-lookup"><span data-stu-id="df277-176">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="df277-177">Annulla la sottoscrizione di gestori eventi da eventi .NET.</span><span class="sxs-lookup"><span data-stu-id="df277-177">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="df277-178">Negli esempi di [ Blazor form](xref:blazor/forms-validation) seguenti viene illustrato come scollegare un gestore eventi nel `Dispose` metodo:</span><span class="sxs-lookup"><span data-stu-id="df277-178">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="df277-179">Approccio basato su campo privato e lambda</span><span class="sxs-lookup"><span data-stu-id="df277-179">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="df277-180">Approccio metodo privato</span><span class="sxs-lookup"><span data-stu-id="df277-180">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="df277-181">Gestire gli errori</span><span class="sxs-lookup"><span data-stu-id="df277-181">Handle errors</span></span>

<span data-ttu-id="df277-182">Per informazioni sulla gestione degli errori durante l'esecuzione del metodo <xref:blazor/handle-errors#lifecycle-methods>del ciclo di vita, vedere.</span><span class="sxs-lookup"><span data-stu-id="df277-182">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="df277-183">Riconnessione con stato dopo il rendering preliminare</span><span class="sxs-lookup"><span data-stu-id="df277-183">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="df277-184">In un' Blazor app Server quando `RenderMode` è `ServerPrerendered`, il componente viene inizialmente sottoposto a rendering statico come parte della pagina.</span><span class="sxs-lookup"><span data-stu-id="df277-184">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="df277-185">Quando il browser stabilisce una connessione al server, viene *nuovamente*eseguito il rendering del componente e il componente è ora interattivo.</span><span class="sxs-lookup"><span data-stu-id="df277-185">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="df277-186">Se è presente il metodo [{Async} Lifecycle OnInitialized](#component-initialization-methods) per l'inizializzazione del componente, il metodo viene eseguito *due volte*:</span><span class="sxs-lookup"><span data-stu-id="df277-186">If the [OnInitialized{Async}](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="df277-187">Quando il componente viene preeseguito in modo statico.</span><span class="sxs-lookup"><span data-stu-id="df277-187">When the component is prerendered statically.</span></span>
* <span data-ttu-id="df277-188">Una volta stabilita la connessione al server.</span><span class="sxs-lookup"><span data-stu-id="df277-188">After the server connection has been established.</span></span>

<span data-ttu-id="df277-189">Ciò può comportare una modifica evidente nei dati visualizzati nell'interfaccia utente quando il componente viene sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="df277-189">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="df277-190">Per evitare lo scenario di doppio rendering in un' Blazor app Server:</span><span class="sxs-lookup"><span data-stu-id="df277-190">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="df277-191">Passare un identificatore che può essere usato per memorizzare nella cache lo stato durante il prerendering e recuperare lo stato dopo il riavvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="df277-191">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="df277-192">Utilizzare l'identificatore durante il prerendering per salvare lo stato del componente.</span><span class="sxs-lookup"><span data-stu-id="df277-192">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="df277-193">Utilizzare l'identificatore dopo il prerendering per recuperare lo stato memorizzato nella cache.</span><span class="sxs-lookup"><span data-stu-id="df277-193">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="df277-194">Il codice seguente illustra un aggiornamento `WeatherForecastService` in un'app server basata Blazor su modello che evita il doppio rendering:</span><span class="sxs-lookup"><span data-stu-id="df277-194">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] _summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="df277-195">Per ulteriori informazioni su `RenderMode`, vedere. <xref:blazor/hosting-model-configuration#render-mode></span><span class="sxs-lookup"><span data-stu-id="df277-195">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="df277-196">Rilevare il momento in cui viene eseguito il prerendering dell'app</span><span class="sxs-lookup"><span data-stu-id="df277-196">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]
