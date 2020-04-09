---
title: ASP.NET Blazor ciclo di vita di ASP.NET Core
author: guardrex
description: Scopri come usare i metodi del Blazor ciclo di vita dei componenti Razor nelle app ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: 831f575afa6ce11d06c016d43ecd1bb59d09eab6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218908"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="39acd-103">ASP.NET Blazor ciclo di vita di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="39acd-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="39acd-104">Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="39acd-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="39acd-105">Il Blazor framework include metodi sincroni e asincroni del ciclo di vita.</span><span class="sxs-lookup"><span data-stu-id="39acd-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="39acd-106">Eseguire l'override dei metodi del ciclo di vita per eseguire operazioni aggiuntive sui componenti durante l'inizializzazione e il rendering dei componenti.</span><span class="sxs-lookup"><span data-stu-id="39acd-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="39acd-107">Metodi del ciclo di vita</span><span class="sxs-lookup"><span data-stu-id="39acd-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="39acd-108">Metodi di inizializzazione dei componenti</span><span class="sxs-lookup"><span data-stu-id="39acd-108">Component initialization methods</span></span>

<span data-ttu-id="39acd-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> vengono richiamati quando il componente viene inizializzato dopo aver ricevuto i parametri iniziali dal componente padre.</span><span class="sxs-lookup"><span data-stu-id="39acd-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="39acd-110">Utilizzare `OnInitializedAsync` quando il componente esegue un'operazione asincrona e deve aggiornare al termine dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="39acd-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="39acd-111">Per un'operazione `OnInitialized`sincrona, eseguire l'override di :</span><span class="sxs-lookup"><span data-stu-id="39acd-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="39acd-112">Per eseguire un'operazione `OnInitializedAsync` asincrona, eseguire l'override e utilizzare la `await` parola chiave sull'operazione:</span><span class="sxs-lookup"><span data-stu-id="39acd-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor<span data-ttu-id="39acd-113">Le app server che `OnInitializedAsync` [eseguono il prerendering](xref:blazor/hosting-model-configuration#render-mode) della chiamata al contenuto due **_volte:_**</span><span class="sxs-lookup"><span data-stu-id="39acd-113"> Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="39acd-114">Una volta quando il componente viene inizialmente sottoposto a rendering in modo statico come parte della pagina.</span><span class="sxs-lookup"><span data-stu-id="39acd-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="39acd-115">Una seconda volta quando il browser stabilisce una connessione al server.</span><span class="sxs-lookup"><span data-stu-id="39acd-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="39acd-116">Per impedire l'esecuzione `OnInitializedAsync` del codice dello sviluppatore due volte, vedere la sezione [Riconnessione con stato dopo il prerendering.](#stateful-reconnection-after-prerendering)</span><span class="sxs-lookup"><span data-stu-id="39acd-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="39acd-117">Mentre Blazor un'app Server esegue il prerendering, alcune azioni, ad esempio la chiamata in JavaScript, non sono possibili perché non è stata stabilita una connessione con il browser.</span><span class="sxs-lookup"><span data-stu-id="39acd-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="39acd-118">Potrebbe essere necessario eseguire il rendering dei componenti in modo diverso quando viene eseguito il prerendering.</span><span class="sxs-lookup"><span data-stu-id="39acd-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="39acd-119">Per altre informazioni, vedere la sezione [Rilevare il prerendering dell'app.](#detect-when-the-app-is-prerendering)</span><span class="sxs-lookup"><span data-stu-id="39acd-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="39acd-120">Se sono impostati gestori eventi, sganciarli durante l'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="39acd-120">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="39acd-121">Per altre informazioni, vedere la sezione Eliminazione dei componenti con IDisposable.For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span><span class="sxs-lookup"><span data-stu-id="39acd-121">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="39acd-122">Prima dell'impostazione dei parametri</span><span class="sxs-lookup"><span data-stu-id="39acd-122">Before parameters are set</span></span>

<span data-ttu-id="39acd-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*>imposta i parametri forniti dall'elemento padre del componente nell'albero di rendering:</span><span class="sxs-lookup"><span data-stu-id="39acd-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="39acd-124"><xref:Microsoft.AspNetCore.Components.ParameterView>contiene l'intero insieme di `SetParametersAsync` valori dei parametri ogni volta che viene chiamato.</span><span class="sxs-lookup"><span data-stu-id="39acd-124"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="39acd-125">L'implementazione `SetParametersAsync` predefinita di imposta il `[Parameter]` `[CascadingParameter]` valore di ogni proprietà `ParameterView`con l'attributo o che ha un valore corrispondente nell'oggetto .</span><span class="sxs-lookup"><span data-stu-id="39acd-125">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="39acd-126">I parametri che non hanno `ParameterView` un valore corrispondente in vengono lasciati invariati.</span><span class="sxs-lookup"><span data-stu-id="39acd-126">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="39acd-127">Se `base.SetParametersAync` non viene richiamato, il codice personalizzato può interpretare il valore dei parametri in ingresso in qualsiasi modo richiesto.</span><span class="sxs-lookup"><span data-stu-id="39acd-127">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="39acd-128">Ad esempio, non è necessario assegnare i parametri in ingresso alle proprietà della classe.</span><span class="sxs-lookup"><span data-stu-id="39acd-128">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="39acd-129">Se sono impostati gestori eventi, sganciarli durante l'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="39acd-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="39acd-130">Per altre informazioni, vedere la sezione Eliminazione dei componenti con IDisposable.For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span><span class="sxs-lookup"><span data-stu-id="39acd-130">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="39acd-131">Dopo aver impostato i parametri</span><span class="sxs-lookup"><span data-stu-id="39acd-131">After parameters are set</span></span>

<span data-ttu-id="39acd-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> sono chiamati:</span><span class="sxs-lookup"><span data-stu-id="39acd-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called:</span></span>

* <span data-ttu-id="39acd-133">Quando il componente viene inizializzato e ha ricevuto il primo set di parametri dal componente padre.</span><span class="sxs-lookup"><span data-stu-id="39acd-133">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="39acd-134">Quando il componente padre esegue nuovamente il rendering e fornisce:</span><span class="sxs-lookup"><span data-stu-id="39acd-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="39acd-135">Solo i tipi primitivi noti non modificabili di cui almeno un parametro è stato modificato.</span><span class="sxs-lookup"><span data-stu-id="39acd-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="39acd-136">Qualsiasi parametro di tipo complesso.</span><span class="sxs-lookup"><span data-stu-id="39acd-136">Any complex-typed parameters.</span></span> <span data-ttu-id="39acd-137">Il framework non può sapere se i valori di un parametro di tipo complesso sono mutati internamente, pertanto considera il set di parametri come modificato.</span><span class="sxs-lookup"><span data-stu-id="39acd-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="39acd-138">Il lavoro asincrono quando si applicano `OnParametersSetAsync` parametri e valori di proprietà deve verificarsi durante l'evento del ciclo di vita.</span><span class="sxs-lookup"><span data-stu-id="39acd-138">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="39acd-139">Se sono impostati gestori eventi, sganciarli durante l'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="39acd-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="39acd-140">Per altre informazioni, vedere la sezione Eliminazione dei componenti con IDisposable.For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span><span class="sxs-lookup"><span data-stu-id="39acd-140">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="39acd-141">Dopo il rendering del componente</span><span class="sxs-lookup"><span data-stu-id="39acd-141">After component render</span></span>

<span data-ttu-id="39acd-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> vengono chiamati al termine del rendering di un componente.</span><span class="sxs-lookup"><span data-stu-id="39acd-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="39acd-143">A questo punto i riferimenti a elementi e componenti vengono popolati.</span><span class="sxs-lookup"><span data-stu-id="39acd-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="39acd-144">Utilizzare questa fase per eseguire passaggi di inizializzazione aggiuntivi utilizzando il contenuto di cui è stato eseguito il rendering, ad esempio l'attivazione di librerie JavaScript di terze parti che operano sugli elementi DOM di cui è stato eseguito il rendering.</span><span class="sxs-lookup"><span data-stu-id="39acd-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="39acd-145">Il `firstRender` parametro per `OnAfterRenderAsync` e `OnAfterRender`:</span><span class="sxs-lookup"><span data-stu-id="39acd-145">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="39acd-146">Viene impostato `true` sulla prima volta che viene eseguito il rendering dell'istanza del componente.</span><span class="sxs-lookup"><span data-stu-id="39acd-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="39acd-147">Può essere utilizzato per garantire che il lavoro di inizializzazione venga eseguito una sola volta.</span><span class="sxs-lookup"><span data-stu-id="39acd-147">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="39acd-148">Il lavoro asincrono immediatamente `OnAfterRenderAsync` dopo il rendering deve verificarsi durante l'evento del ciclo di vita.</span><span class="sxs-lookup"><span data-stu-id="39acd-148">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="39acd-149">Anche se si <xref:System.Threading.Tasks.Task> `OnAfterRenderAsync`restituisce un from , il framework non pianifica un ulteriore ciclo di rendering per il componente una volta completata l'attività.</span><span class="sxs-lookup"><span data-stu-id="39acd-149">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="39acd-150">Questo per evitare un ciclo di rendering infinito.</span><span class="sxs-lookup"><span data-stu-id="39acd-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="39acd-151">È diverso dagli altri metodi del ciclo di vita, che pianificano un ulteriore ciclo di rendering una volta completata l'attività restituita.</span><span class="sxs-lookup"><span data-stu-id="39acd-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="39acd-152">`OnAfterRender`e `OnAfterRenderAsync` *non vengono chiamati durante il prerendering sul server.*</span><span class="sxs-lookup"><span data-stu-id="39acd-152">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="39acd-153">Se sono impostati gestori eventi, sganciarli durante l'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="39acd-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="39acd-154">Per altre informazioni, vedere la sezione Eliminazione dei componenti con IDisposable.For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span><span class="sxs-lookup"><span data-stu-id="39acd-154">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="39acd-155">Eliminazione dell'aggiornamento dell'interfaccia utente</span><span class="sxs-lookup"><span data-stu-id="39acd-155">Suppress UI refreshing</span></span>

<span data-ttu-id="39acd-156">Eseguire <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> l'override per eliminare l'aggiornamento dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="39acd-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="39acd-157">Se l'implementazione restituisce `true`, l'interfaccia utente viene aggiornata:</span><span class="sxs-lookup"><span data-stu-id="39acd-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="39acd-158">`ShouldRender`viene chiamato ogni volta che viene eseguito il rendering del componente.</span><span class="sxs-lookup"><span data-stu-id="39acd-158">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="39acd-159">Anche `ShouldRender` se viene sottoposto a override, il componente viene sempre eseguito il rendering inizialmente.</span><span class="sxs-lookup"><span data-stu-id="39acd-159">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="39acd-160">Modifiche stato</span><span class="sxs-lookup"><span data-stu-id="39acd-160">State changes</span></span>

<span data-ttu-id="39acd-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>notifica al componente che il relativo stato è stato modificato.</span><span class="sxs-lookup"><span data-stu-id="39acd-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="39acd-162">Se applicabile, `StateHasChanged` la chiamata determina il rendering del componente.</span><span class="sxs-lookup"><span data-stu-id="39acd-162">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="39acd-163">Gestire le azioni asincrone incomplete al renderingHandle incomplete async actions at render</span><span class="sxs-lookup"><span data-stu-id="39acd-163">Handle incomplete async actions at render</span></span>

<span data-ttu-id="39acd-164">Le azioni asincrone eseguite negli eventi del ciclo di vita potrebbero non essere state completate prima del rendering del componente.</span><span class="sxs-lookup"><span data-stu-id="39acd-164">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="39acd-165">Gli oggetti `null` potrebbero essere o essere incompleti con i dati durante l'esecuzione del metodo del ciclo di vita.</span><span class="sxs-lookup"><span data-stu-id="39acd-165">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="39acd-166">Fornire la logica di rendering per verificare che gli oggetti vengano inizializzati.</span><span class="sxs-lookup"><span data-stu-id="39acd-166">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="39acd-167">Esegue il rendering di elementi dell'interfaccia utente `null`segnaposto (ad esempio, un messaggio di caricamento) mentre gli oggetti sono .</span><span class="sxs-lookup"><span data-stu-id="39acd-167">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="39acd-168">Nel `FetchData` componente dei Blazor modelli, `OnInitializedAsync` viene eseguito l'override per ricevere`forecasts`in modo asincrono i dati di previsione ( ).</span><span class="sxs-lookup"><span data-stu-id="39acd-168">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="39acd-169">Quando `forecasts` `null`è , viene visualizzato un messaggio di caricamento all'utente.</span><span class="sxs-lookup"><span data-stu-id="39acd-169">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="39acd-170">Dopo `Task` il completamento restituito, `OnInitializedAsync` il componente viene nuovamente eseguito il rendering con lo stato aggiornato.</span><span class="sxs-lookup"><span data-stu-id="39acd-170">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="39acd-171">*Pages/FetchData.razor* nel Blazor modello Server:</span><span class="sxs-lookup"><span data-stu-id="39acd-171">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="39acd-172">Smaltimento dei componenti con IDisposable</span><span class="sxs-lookup"><span data-stu-id="39acd-172">Component disposal with IDisposable</span></span>

<span data-ttu-id="39acd-173">Se un <xref:System.IDisposable>componente implementa , il [metodo Dispose](/dotnet/standard/garbage-collection/implementing-dispose) viene chiamato quando il componente viene rimosso dall'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="39acd-173">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="39acd-174">Il seguente `@implements IDisposable` componente `Dispose` utilizza e il metodo:</span><span class="sxs-lookup"><span data-stu-id="39acd-174">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="39acd-175">La <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> `Dispose` chiamata non è supportata.</span><span class="sxs-lookup"><span data-stu-id="39acd-175">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="39acd-176">`StateHasChanged`potrebbe essere richiamato come parte dell'abbattimento del renderer, pertanto la richiesta di aggiornamenti dell'interfaccia utente a quel punto non è supportata.</span><span class="sxs-lookup"><span data-stu-id="39acd-176">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="39acd-177">Annullare la sottoscrizione di gestori eventi da eventi .NET.</span><span class="sxs-lookup"><span data-stu-id="39acd-177">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="39acd-178">Negli esempi di [ Blazor form](xref:blazor/forms-validation) seguenti viene illustrato come eseguire l'unhook di un gestore eventi nel metodo:The following form examples show how to unhook an event handler in the `Dispose` method:</span><span class="sxs-lookup"><span data-stu-id="39acd-178">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="39acd-179">Approccio di base e lambda privato</span><span class="sxs-lookup"><span data-stu-id="39acd-179">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="39acd-180">Approccio al metodo privato</span><span class="sxs-lookup"><span data-stu-id="39acd-180">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="39acd-181">Gestire gli errori</span><span class="sxs-lookup"><span data-stu-id="39acd-181">Handle errors</span></span>

<span data-ttu-id="39acd-182">Per informazioni sulla gestione degli errori <xref:blazor/handle-errors#lifecycle-methods>durante l'esecuzione del metodo del ciclo di vita, vedere .</span><span class="sxs-lookup"><span data-stu-id="39acd-182">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="39acd-183">Riconnessione con stato dopo il prerendering</span><span class="sxs-lookup"><span data-stu-id="39acd-183">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="39acd-184">In Blazor un'app `RenderMode` `ServerPrerendered`Server quando è , il componente viene inizialmente sottoposto a rendering in modo statico come parte della pagina.</span><span class="sxs-lookup"><span data-stu-id="39acd-184">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="39acd-185">Una volta stabilita una connessione al server, il rendering del componente viene *eseguito nuovamente*e il componente è interattivo.</span><span class="sxs-lookup"><span data-stu-id="39acd-185">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="39acd-186">Se per l'inizializzazione del componente è presente il metodo del ciclo di vita [OnInitialized,Async](xref:blazor/lifecycle#component-initialization-methods) per l'inizializzazione del componente, il metodo viene eseguito *due volte:*</span><span class="sxs-lookup"><span data-stu-id="39acd-186">If the [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="39acd-187">Quando viene eseguito il prerendering statico del componente.</span><span class="sxs-lookup"><span data-stu-id="39acd-187">When the component is prerendered statically.</span></span>
* <span data-ttu-id="39acd-188">Una volta stabilita la connessione al server.</span><span class="sxs-lookup"><span data-stu-id="39acd-188">After the server connection has been established.</span></span>

<span data-ttu-id="39acd-189">Ciò può comportare una modifica notevole nei dati visualizzati nell'interfaccia utente quando il componente viene infine eseguito il rendering.</span><span class="sxs-lookup"><span data-stu-id="39acd-189">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="39acd-190">Per evitare lo scenario Blazor a doppio rendering in un'app Server:</span><span class="sxs-lookup"><span data-stu-id="39acd-190">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="39acd-191">Passare un identificatore che può essere usato per memorizzare nella cache lo stato durante il prerendering e recuperare lo stato dopo il riavvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="39acd-191">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="39acd-192">Utilizzare l'identificatore durante il prerendering per salvare lo stato del componente.</span><span class="sxs-lookup"><span data-stu-id="39acd-192">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="39acd-193">Utilizzare l'identificatore dopo il prerendering per recuperare lo stato memorizzato nella cache.</span><span class="sxs-lookup"><span data-stu-id="39acd-193">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="39acd-194">Il codice seguente `WeatherForecastService` illustra un aggiornamento Blazor in un'app Server basata su modelli che evita il doppio rendering:The following code demonstrates an updated in a template-based Server app that avoids the double rendering:</span><span class="sxs-lookup"><span data-stu-id="39acd-194">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="39acd-195">Per ulteriori informazioni `RenderMode`su <xref:blazor/hosting-model-configuration#render-mode>, vedere .</span><span class="sxs-lookup"><span data-stu-id="39acd-195">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="39acd-196">Rilevare il prerendering dell'app</span><span class="sxs-lookup"><span data-stu-id="39acd-196">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]
