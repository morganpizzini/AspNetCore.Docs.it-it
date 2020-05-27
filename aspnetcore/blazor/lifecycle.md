---
<span data-ttu-id="4dfda-101">title: "ASP.NET Core Blazor Lifecycle" Author: Description: "informazioni su come usare i Razor metodi del ciclo di vita dei componenti nelle Blazor app ASP.NET Core."</span><span class="sxs-lookup"><span data-stu-id="4dfda-101">title: 'ASP.NET Core Blazor lifecycle' author: description: 'Learn how to use Razor component lifecycle methods in ASP.NET Core Blazor apps.'</span></span>
<span data-ttu-id="4dfda-102">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4dfda-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4dfda-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4dfda-103">'Blazor'</span></span>
- <span data-ttu-id="4dfda-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4dfda-104">'Identity'</span></span>
- <span data-ttu-id="4dfda-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4dfda-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="4dfda-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4dfda-106">'Razor'</span></span>
- <span data-ttu-id="4dfda-107">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="4dfda-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="4dfda-108">Ciclo di vita ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="4dfda-108">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="4dfda-109">Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="4dfda-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="4dfda-110">Il Blazor Framework include metodi del ciclo di vita sincroni e asincroni.</span><span class="sxs-lookup"><span data-stu-id="4dfda-110">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="4dfda-111">Eseguire l'override dei metodi Lifecycle per eseguire operazioni aggiuntive sui componenti durante l'inizializzazione e il rendering dei componenti.</span><span class="sxs-lookup"><span data-stu-id="4dfda-111">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="4dfda-112">Metodi del ciclo di vita</span><span class="sxs-lookup"><span data-stu-id="4dfda-112">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="4dfda-113">Metodi di inizializzazione componenti</span><span class="sxs-lookup"><span data-stu-id="4dfda-113">Component initialization methods</span></span>

<span data-ttu-id="4dfda-114"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> vengono richiamati quando il componente viene inizializzato dopo aver ricevuto i parametri iniziali dal componente padre.</span><span class="sxs-lookup"><span data-stu-id="4dfda-114"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="4dfda-115">Usare <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> quando il componente esegue un'operazione asincrona e deve essere aggiornato al termine dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="4dfda-115">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="4dfda-116">Per un'operazione sincrona, eseguire l'override di <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> :</span><span class="sxs-lookup"><span data-stu-id="4dfda-116">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="4dfda-117">Per eseguire un'operazione asincrona, eseguire l'override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> e usare l'operatore [await](/dotnet/csharp/language-reference/operators/await) nell'operazione:</span><span class="sxs-lookup"><span data-stu-id="4dfda-117">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [await](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor<span data-ttu-id="4dfda-118">App server che [preeseguono la](xref:blazor/hosting-model-configuration#render-mode) chiamata al contenuto per <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_due volte_**:</span><span class="sxs-lookup"><span data-stu-id="4dfda-118"> Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_twice_**:</span></span>

* <span data-ttu-id="4dfda-119">Una volta quando il componente viene inizialmente sottoposto a rendering statico come parte della pagina.</span><span class="sxs-lookup"><span data-stu-id="4dfda-119">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="4dfda-120">Una seconda volta quando il browser stabilisce una connessione al server.</span><span class="sxs-lookup"><span data-stu-id="4dfda-120">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="4dfda-121">Per impedire l'esecuzione di codice dello sviluppatore per <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> due volte, vedere la sezione [riconnessione con stato dopo il rendering](#stateful-reconnection-after-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="4dfda-121">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="4dfda-122">Mentre un' Blazor app Server è prerendering, alcune azioni, ad esempio la chiamata a JavaScript, non sono possibili perché non è stata stabilita una connessione con il browser.</span><span class="sxs-lookup"><span data-stu-id="4dfda-122">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="4dfda-123">I componenti potrebbero dover eseguire il rendering in modo diverso quando ne viene eseguito il rendering.</span><span class="sxs-lookup"><span data-stu-id="4dfda-123">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="4dfda-124">Per altre informazioni, vedere la sezione [rilevare quando l'app è prerendering](#detect-when-the-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="4dfda-124">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="4dfda-125">Se sono configurati gestori di eventi, rimuoverli a disposizione.</span><span class="sxs-lookup"><span data-stu-id="4dfda-125">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="4dfda-126">Per ulteriori informazioni, vedere la sezione relativa all' [eliminazione dei componenti con IDisposable](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="4dfda-126">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="4dfda-127">Prima dell'impostazione dei parametri</span><span class="sxs-lookup"><span data-stu-id="4dfda-127">Before parameters are set</span></span>

<span data-ttu-id="4dfda-128"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>Imposta i parametri forniti dall'elemento padre del componente nell'albero di rendering:</span><span class="sxs-lookup"><span data-stu-id="4dfda-128"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="4dfda-129"><xref:Microsoft.AspNetCore.Components.ParameterView>contiene l'intero set di valori dei parametri ogni volta che <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> viene chiamato.</span><span class="sxs-lookup"><span data-stu-id="4dfda-129"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="4dfda-130">L'implementazione predefinita di <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> imposta il valore di ogni proprietà con l' [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attributo o con [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) un valore corrispondente in <xref:Microsoft.AspNetCore.Components.ParameterView> .</span><span class="sxs-lookup"><span data-stu-id="4dfda-130">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="4dfda-131">I parametri che non hanno un valore corrispondente in <xref:Microsoft.AspNetCore.Components.ParameterView> rimangono invariati.</span><span class="sxs-lookup"><span data-stu-id="4dfda-131">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="4dfda-132">Se [base. SetParametersAync](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) non viene richiamato, il codice personalizzato può interpretare il valore dei parametri in ingresso in qualsiasi modo necessario.</span><span class="sxs-lookup"><span data-stu-id="4dfda-132">If [base.SetParametersAync](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="4dfda-133">Ad esempio, non è necessario assegnare i parametri in ingresso alle proprietà della classe.</span><span class="sxs-lookup"><span data-stu-id="4dfda-133">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="4dfda-134">Se sono configurati gestori di eventi, rimuoverli a disposizione.</span><span class="sxs-lookup"><span data-stu-id="4dfda-134">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="4dfda-135">Per ulteriori informazioni, vedere la sezione relativa all' [eliminazione dei componenti con IDisposable](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="4dfda-135">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="4dfda-136">Parametri after impostati</span><span class="sxs-lookup"><span data-stu-id="4dfda-136">After parameters are set</span></span>

<span data-ttu-id="4dfda-137"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> vengono chiamati:</span><span class="sxs-lookup"><span data-stu-id="4dfda-137"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="4dfda-138">Quando il componente viene inizializzato e ha ricevuto il primo set di parametri dal componente padre.</span><span class="sxs-lookup"><span data-stu-id="4dfda-138">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="4dfda-139">Quando il componente padre esegue nuovamente il rendering e fornisce:</span><span class="sxs-lookup"><span data-stu-id="4dfda-139">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="4dfda-140">Solo i tipi non modificabili primitivi noti di cui è stato modificato almeno un parametro.</span><span class="sxs-lookup"><span data-stu-id="4dfda-140">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="4dfda-141">Tutti i parametri tipizzati complessi.</span><span class="sxs-lookup"><span data-stu-id="4dfda-141">Any complex-typed parameters.</span></span> <span data-ttu-id="4dfda-142">Il Framework non è in grado di stabilire se i valori di un parametro tipizzato complesso sono stati modificati internamente, quindi considera il set di parametri come modificato.</span><span class="sxs-lookup"><span data-stu-id="4dfda-142">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="4dfda-143">Il lavoro asincrono quando si applicano parametri e valori di proprietà deve verificarsi durante l'evento del ciclo di vita <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="4dfda-143">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="4dfda-144">Se sono configurati gestori di eventi, rimuoverli a disposizione.</span><span class="sxs-lookup"><span data-stu-id="4dfda-144">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="4dfda-145">Per ulteriori informazioni, vedere la sezione relativa all' [eliminazione dei componenti con IDisposable](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="4dfda-145">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="4dfda-146">Rendering del componente dopo</span><span class="sxs-lookup"><span data-stu-id="4dfda-146">After component render</span></span>

<span data-ttu-id="4dfda-147"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> vengono chiamati dopo che un componente ha terminato il rendering.</span><span class="sxs-lookup"><span data-stu-id="4dfda-147"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="4dfda-148">I riferimenti a elementi e componenti vengono popolati a questo punto.</span><span class="sxs-lookup"><span data-stu-id="4dfda-148">Element and component references are populated at this point.</span></span> <span data-ttu-id="4dfda-149">Usare questa fase per eseguire passaggi di inizializzazione aggiuntivi usando il contenuto sottoposto a rendering, ad esempio l'attivazione di librerie JavaScript di terze parti che operano sugli elementi DOM sottoposti a rendering.</span><span class="sxs-lookup"><span data-stu-id="4dfda-149">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="4dfda-150">Il `firstRender` parametro per <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> :</span><span class="sxs-lookup"><span data-stu-id="4dfda-150">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="4dfda-151">Viene impostato `true` sulla prima volta che viene eseguito il rendering dell'istanza del componente.</span><span class="sxs-lookup"><span data-stu-id="4dfda-151">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="4dfda-152">Può essere usato per garantire che il lavoro di inizializzazione venga eseguito una sola volta.</span><span class="sxs-lookup"><span data-stu-id="4dfda-152">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="4dfda-153">Il lavoro asincrono immediatamente dopo il rendering deve verificarsi durante l'evento del ciclo di vita <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="4dfda-153">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="4dfda-154">Anche se si restituisce un oggetto <xref:System.Threading.Tasks.Task> da <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> , il Framework non pianifica un ulteriore ciclo di rendering per il componente al termine dell'attività.</span><span class="sxs-lookup"><span data-stu-id="4dfda-154">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="4dfda-155">In questo modo è possibile evitare un ciclo di rendering infinito.</span><span class="sxs-lookup"><span data-stu-id="4dfda-155">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="4dfda-156">È diverso dagli altri metodi del ciclo di vita, che pianificano un ulteriore ciclo di rendering dopo il completamento dell'attività restituita.</span><span class="sxs-lookup"><span data-stu-id="4dfda-156">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="4dfda-157"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *non vengono chiamati durante il prerendering sul server.*</span><span class="sxs-lookup"><span data-stu-id="4dfda-157"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="4dfda-158">Se sono configurati gestori di eventi, rimuoverli a disposizione.</span><span class="sxs-lookup"><span data-stu-id="4dfda-158">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="4dfda-159">Per ulteriori informazioni, vedere la sezione relativa all' [eliminazione dei componenti con IDisposable](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="4dfda-159">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="4dfda-160">Evita aggiornamento dell'interfaccia utente</span><span class="sxs-lookup"><span data-stu-id="4dfda-160">Suppress UI refreshing</span></span>

<span data-ttu-id="4dfda-161">Eseguire l'override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> per impedire l'aggiornamento dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="4dfda-161">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="4dfda-162">Se l'implementazione restituisce `true` , l'interfaccia utente viene aggiornata:</span><span class="sxs-lookup"><span data-stu-id="4dfda-162">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="4dfda-163"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>viene chiamato ogni volta che il componente viene sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="4dfda-163"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="4dfda-164">Anche se <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> viene sottoposto a override, il componente viene sempre sottoposto a rendering iniziale.</span><span class="sxs-lookup"><span data-stu-id="4dfda-164">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="4dfda-165">Per altre informazioni, vedere <xref:performance/blazor/webassembly-best-practices#avoid-unnecessary-component-renders>.</span><span class="sxs-lookup"><span data-stu-id="4dfda-165">For more information, see <xref:performance/blazor/webassembly-best-practices#avoid-unnecessary-component-renders>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="4dfda-166">Modifiche stato</span><span class="sxs-lookup"><span data-stu-id="4dfda-166">State changes</span></span>

<span data-ttu-id="4dfda-167"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>notifica al componente che lo stato è stato modificato.</span><span class="sxs-lookup"><span data-stu-id="4dfda-167"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="4dfda-168">Se applicabile, <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> la chiamata a comporta il rendering del componente.</span><span class="sxs-lookup"><span data-stu-id="4dfda-168">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="4dfda-169">Gestisci azioni asincrone incomplete durante il rendering</span><span class="sxs-lookup"><span data-stu-id="4dfda-169">Handle incomplete async actions at render</span></span>

<span data-ttu-id="4dfda-170">Le azioni asincrone eseguite negli eventi del ciclo di vita potrebbero non essere state completate prima del rendering del componente.</span><span class="sxs-lookup"><span data-stu-id="4dfda-170">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="4dfda-171">Gli oggetti possono essere `null` o compilati in modo non completo con i dati mentre è in esecuzione il metodo del ciclo di vita.</span><span class="sxs-lookup"><span data-stu-id="4dfda-171">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="4dfda-172">Fornire la logica di rendering per confermare che gli oggetti vengono inizializzati.</span><span class="sxs-lookup"><span data-stu-id="4dfda-172">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="4dfda-173">Esegue il rendering degli elementi dell'interfaccia utente segnaposto (ad esempio, un messaggio di caricamento) mentre gli oggetti sono `null` .</span><span class="sxs-lookup"><span data-stu-id="4dfda-173">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="4dfda-174">Nel `FetchData` componente dei Blazor modelli <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> viene eseguito l'override di asincrono Receive forecast data ( `forecasts` ).</span><span class="sxs-lookup"><span data-stu-id="4dfda-174">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="4dfda-175">Quando `forecasts` è `null` , all'utente viene visualizzato un messaggio di caricamento.</span><span class="sxs-lookup"><span data-stu-id="4dfda-175">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="4dfda-176">Quando l'oggetto `Task` restituito da <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> viene completato, viene eseguito il rendering del componente con lo stato aggiornato.</span><span class="sxs-lookup"><span data-stu-id="4dfda-176">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="4dfda-177">*Pages/fetchData. Razor* nel Blazor modello server:</span><span class="sxs-lookup"><span data-stu-id="4dfda-177">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="4dfda-178">Eliminazione di componenti con IDisposable</span><span class="sxs-lookup"><span data-stu-id="4dfda-178">Component disposal with IDisposable</span></span>

<span data-ttu-id="4dfda-179">Se un componente implementa <xref:System.IDisposable> , il [metodo Dispose](/dotnet/standard/garbage-collection/implementing-dispose) viene chiamato quando il componente viene rimosso dall'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="4dfda-179">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="4dfda-180">Il componente seguente utilizza `@implements IDisposable` e il `Dispose` Metodo:</span><span class="sxs-lookup"><span data-stu-id="4dfda-180">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="4dfda-181">La chiamata <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> di in `Dispose` non è supportata.</span><span class="sxs-lookup"><span data-stu-id="4dfda-181">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="4dfda-182"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>potrebbe essere richiamato come parte del propagazione del renderer, quindi la richiesta di aggiornamenti dell'interfaccia utente in quel momento non è supportata.</span><span class="sxs-lookup"><span data-stu-id="4dfda-182"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="4dfda-183">Annulla la sottoscrizione di gestori eventi da eventi .NET.</span><span class="sxs-lookup"><span data-stu-id="4dfda-183">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="4dfda-184">Negli esempi di [ Blazor form](xref:blazor/forms-validation) seguenti viene illustrato come scollegare un gestore eventi nel `Dispose` Metodo:</span><span class="sxs-lookup"><span data-stu-id="4dfda-184">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="4dfda-185">Approccio basato su campo privato e lambda</span><span class="sxs-lookup"><span data-stu-id="4dfda-185">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="4dfda-186">Approccio metodo privato</span><span class="sxs-lookup"><span data-stu-id="4dfda-186">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="4dfda-187">Gestire gli errori</span><span class="sxs-lookup"><span data-stu-id="4dfda-187">Handle errors</span></span>

<span data-ttu-id="4dfda-188">Per informazioni sulla gestione degli errori durante l'esecuzione del metodo del ciclo di vita, vedere <xref:blazor/handle-errors#lifecycle-methods> .</span><span class="sxs-lookup"><span data-stu-id="4dfda-188">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="4dfda-189">Riconnessione con stato dopo il rendering preliminare</span><span class="sxs-lookup"><span data-stu-id="4dfda-189">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="4dfda-190">In un' Blazor app Server quando <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> è <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> , il componente viene inizialmente sottoposto a rendering statico come parte della pagina.</span><span class="sxs-lookup"><span data-stu-id="4dfda-190">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="4dfda-191">Quando il browser stabilisce una connessione al server, viene *nuovamente*eseguito il rendering del componente e il componente è ora interattivo.</span><span class="sxs-lookup"><span data-stu-id="4dfda-191">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="4dfda-192">Se è presente il metodo [{Async} Lifecycle OnInitialized](#component-initialization-methods) per l'inizializzazione del componente, il metodo viene eseguito *due volte*:</span><span class="sxs-lookup"><span data-stu-id="4dfda-192">If the [OnInitialized{Async}](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="4dfda-193">Quando il componente viene preeseguito in modo statico.</span><span class="sxs-lookup"><span data-stu-id="4dfda-193">When the component is prerendered statically.</span></span>
* <span data-ttu-id="4dfda-194">Una volta stabilita la connessione al server.</span><span class="sxs-lookup"><span data-stu-id="4dfda-194">After the server connection has been established.</span></span>

<span data-ttu-id="4dfda-195">Ciò può comportare una modifica evidente nei dati visualizzati nell'interfaccia utente quando il componente viene sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="4dfda-195">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="4dfda-196">Per evitare lo scenario di doppio rendering in un' Blazor app Server:</span><span class="sxs-lookup"><span data-stu-id="4dfda-196">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="4dfda-197">Passare un identificatore che può essere usato per memorizzare nella cache lo stato durante il prerendering e recuperare lo stato dopo il riavvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="4dfda-197">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="4dfda-198">Utilizzare l'identificatore durante il prerendering per salvare lo stato del componente.</span><span class="sxs-lookup"><span data-stu-id="4dfda-198">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="4dfda-199">Utilizzare l'identificatore dopo il prerendering per recuperare lo stato memorizzato nella cache.</span><span class="sxs-lookup"><span data-stu-id="4dfda-199">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="4dfda-200">Il codice seguente illustra un aggiornamento `WeatherForecastService` in un'app server basata su modello Blazor che evita il doppio rendering:</span><span class="sxs-lookup"><span data-stu-id="4dfda-200">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
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
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="4dfda-201">Per ulteriori informazioni su <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> , vedere <xref:blazor/hosting-model-configuration#render-mode> .</span><span class="sxs-lookup"><span data-stu-id="4dfda-201">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="4dfda-202">Rilevare il momento in cui viene eseguito il prerendering dell'app</span><span class="sxs-lookup"><span data-stu-id="4dfda-202">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="cancelable-background-work"></a><span data-ttu-id="4dfda-203">Lavoro in background annullabile</span><span class="sxs-lookup"><span data-stu-id="4dfda-203">Cancelable background work</span></span>

<span data-ttu-id="4dfda-204">I componenti eseguono spesso attività in background con esecuzione prolungata, ad esempio la creazione di chiamate di rete ( <xref:System.Net.Http.HttpClient> ) e l'interazione con i database.</span><span class="sxs-lookup"><span data-stu-id="4dfda-204">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="4dfda-205">È consigliabile arrestare il lavoro in background per conservare le risorse di sistema in diverse situazioni.</span><span class="sxs-lookup"><span data-stu-id="4dfda-205">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="4dfda-206">Ad esempio, le operazioni asincrone in background non vengono arrestate automaticamente quando un utente si sposta da un componente.</span><span class="sxs-lookup"><span data-stu-id="4dfda-206">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="4dfda-207">Altri motivi per cui gli elementi di lavoro in background potrebbero richiedere l'annullamento includono:</span><span class="sxs-lookup"><span data-stu-id="4dfda-207">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="4dfda-208">Un'attività in background in esecuzione è stata avviata con i parametri di elaborazione o i dati di input difettosi.</span><span class="sxs-lookup"><span data-stu-id="4dfda-208">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="4dfda-209">Il set corrente di elementi di lavoro in background in esecuzione deve essere sostituito con un nuovo set di elementi di lavoro.</span><span class="sxs-lookup"><span data-stu-id="4dfda-209">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="4dfda-210">È necessario modificare la priorità delle attività attualmente in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="4dfda-210">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="4dfda-211">L'app deve essere arrestata per poterla ridistribuire nel server.</span><span class="sxs-lookup"><span data-stu-id="4dfda-211">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="4dfda-212">Le risorse del server diventano limitate, rendendo necessaria la ripianificazione degli elementi di lavoro in background.</span><span class="sxs-lookup"><span data-stu-id="4dfda-212">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="4dfda-213">Per implementare un modello di lavoro in background annullabile in un componente:</span><span class="sxs-lookup"><span data-stu-id="4dfda-213">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="4dfda-214">Utilizzare <xref:System.Threading.CancellationTokenSource> e <xref:System.Threading.CancellationToken> .</span><span class="sxs-lookup"><span data-stu-id="4dfda-214">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="4dfda-215">In caso di [eliminazione del componente](#component-disposal-with-idisposable) e in qualsiasi momento si desideri annullare l'annullamento del token, chiamare [CancellationTokenSource. Cancel](xref:System.Threading.CancellationTokenSource.Cancel%2A) per segnalare che il lavoro in background deve essere annullato.</span><span class="sxs-lookup"><span data-stu-id="4dfda-215">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [CancellationTokenSource.Cancel](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="4dfda-216">Dopo che la chiamata asincrona restituisce, chiamare <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> sul token.</span><span class="sxs-lookup"><span data-stu-id="4dfda-216">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="4dfda-217">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="4dfda-217">In the following example:</span></span>

* <span data-ttu-id="4dfda-218">`await Task.Delay(5000, cts.Token);`rappresenta il lavoro in background asincrono con esecuzione prolungata.</span><span class="sxs-lookup"><span data-stu-id="4dfda-218">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="4dfda-219">`BackgroundResourceMethod`rappresenta un metodo in background con esecuzione prolungata che non deve essere avviato se l'oggetto `Resource` viene eliminato prima della chiamata al metodo.</span><span class="sxs-lookup"><span data-stu-id="4dfda-219">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```
