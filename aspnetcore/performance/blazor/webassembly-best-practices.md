---
<span data-ttu-id="d3376-101">title:' ASP.NET Core Blazor procedure consigliate per le prestazioni dell'assembly Web ' Author: Description:' suggerimenti per migliorare le prestazioni nelle app ASP.NET Core Blazor webassembly ed evitare problemi di prestazioni comuni .'</span><span class="sxs-lookup"><span data-stu-id="d3376-101">title: 'ASP.NET Core Blazor WebAssembly performance best practices' author: description: 'Tips for increasing performance in ASP.NET Core Blazor WebAssembly apps and avoiding common performance problems.'</span></span>
<span data-ttu-id="d3376-102">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="d3376-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3376-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3376-103">'Blazor'</span></span>
- <span data-ttu-id="d3376-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3376-104">'Identity'</span></span>
- <span data-ttu-id="d3376-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3376-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3376-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3376-106">'Razor'</span></span>
- <span data-ttu-id="d3376-107">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="d3376-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a><span data-ttu-id="d3376-108">BlazorProcedure consigliate per le prestazioni di ASP.NET Core Webassembly</span><span class="sxs-lookup"><span data-stu-id="d3376-108">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="d3376-109">Di [il](https://github.com/pranavkm) suo</span><span class="sxs-lookup"><span data-stu-id="d3376-109">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="d3376-110">Questo articolo fornisce linee guida per ASP.NET Core Blazor procedure consigliate per le prestazioni di webassembly.</span><span class="sxs-lookup"><span data-stu-id="d3376-110">This article provides guidelines for ASP.NET Core Blazor WebAssembly performance best practices.</span></span>

## <a name="avoid-unnecessary-component-renders"></a><span data-ttu-id="d3376-111">Evitare il rendering di componenti superflui</span><span class="sxs-lookup"><span data-stu-id="d3376-111">Avoid unnecessary component renders</span></span>

Blazor<span data-ttu-id="d3376-112">l'algoritmo diffing evita di eseguire nuovamente il rendering di un componente quando l'algoritmo rileva che il componente non è stato modificato.</span><span class="sxs-lookup"><span data-stu-id="d3376-112">'s diffing algorithm avoids rerendering a component when the algorithm perceives that the component hasn't changed.</span></span> <span data-ttu-id="d3376-113">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> per un controllo con granularità fine sul rendering dei componenti.</span><span class="sxs-lookup"><span data-stu-id="d3376-113">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> for fine-grained control over component rendering.</span></span>

<span data-ttu-id="d3376-114">Se la creazione di un componente di solo interfaccia utente che non cambia mai dopo il rendering iniziale, configurare <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> per restituire `false` :</span><span class="sxs-lookup"><span data-stu-id="d3376-114">If authoring a UI-only component that never changes after the initial render, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="d3376-115">La maggior parte delle app non richiede un controllo con granularità fine, ma <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> può anche essere usata per eseguire il rendering selettivo di un componente che risponde a un evento dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="d3376-115">Most apps don't require fine-grained control, but <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> can also be used to selectively render a component responding to a UI event.</span></span>

<span data-ttu-id="d3376-116">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="d3376-116">In the following example:</span></span>

* <span data-ttu-id="d3376-117"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>viene sottoposto a override e impostato sul valore del <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> campo, che inizialmente si verifica `false` quando il componente viene caricato.</span><span class="sxs-lookup"><span data-stu-id="d3376-117"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden and set to the value of the <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> field, which is initially `false` when the component loads.</span></span>
* <span data-ttu-id="d3376-118">Quando il pulsante è selezionato, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> viene impostato su `true` , che forza il rendering del componente con l'oggetto aggiornato `currentCount` .</span><span class="sxs-lookup"><span data-stu-id="d3376-118">When the button is selected, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is set to `true`, which forces the component to rerender with the updated `currentCount`.</span></span>
* <span data-ttu-id="d3376-119">Subito dopo il rendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> imposta il valore di <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> nuovo su `false` per impedire un ulteriore rendering fino alla successiva selezione del pulsante.</span><span class="sxs-lookup"><span data-stu-id="d3376-119">Immediately after rerendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sets the value of <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> back to `false` to prevent further rerendering until the next time the button is selected.</span></span>

```razor
<p>Current count: @currentCount</p>

<button @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    protected override void OnAfterRender(bool first)
    {
        shouldRender = false;
    }

    private void IncrementCount()
    {
        currentCount++;
        shouldRender = true;
    }
}
```

<span data-ttu-id="d3376-120">Per altre informazioni, vedere <xref:blazor/lifecycle#after-component-render>.</span><span class="sxs-lookup"><span data-stu-id="d3376-120">For more information, see <xref:blazor/lifecycle#after-component-render>.</span></span>

## <a name="virtualize-re-usable-fragments"></a><span data-ttu-id="d3376-121">Virtualizza i frammenti riutilizzabili</span><span class="sxs-lookup"><span data-stu-id="d3376-121">Virtualize re-usable fragments</span></span>

<span data-ttu-id="d3376-122">I componenti offrono un approccio pratico per produrre frammenti riutilizzabili di codice e markup.</span><span class="sxs-lookup"><span data-stu-id="d3376-122">Components offer a convenient approach to produce re-usable fragments of code and markup.</span></span> <span data-ttu-id="d3376-123">In generale, si consiglia di creare singoli componenti che meglio si allineano ai requisiti dell'app.</span><span class="sxs-lookup"><span data-stu-id="d3376-123">In general, we recommend authoring individual components that best align with the app's requirements.</span></span> <span data-ttu-id="d3376-124">Un avvertimento è che ogni componente figlio aggiuntivo contribuisce al tempo totale necessario per eseguire il rendering di un componente padre.</span><span class="sxs-lookup"><span data-stu-id="d3376-124">One caveat is that each additional child component contributes to the total time it takes to render a parent component.</span></span> <span data-ttu-id="d3376-125">Per la maggior parte delle app, l'overhead aggiuntivo è trascurabile.</span><span class="sxs-lookup"><span data-stu-id="d3376-125">For most apps, the additional overhead is negligible.</span></span> <span data-ttu-id="d3376-126">Le app che producono un numero elevato di componenti devono considerare l'uso di strategie per ridurre l'overhead di elaborazione, ad esempio limitando il numero di componenti sottoposti a rendering.</span><span class="sxs-lookup"><span data-stu-id="d3376-126">Apps that produce a large number of components should consider using strategies to reduce processing overhead, such as limiting the number of rendered components.</span></span>

<span data-ttu-id="d3376-127">Ad esempio, una griglia o un elenco che esegue il rendering di centinaia di righe contenenti componenti comporta un utilizzo intensivo del processore per il rendering.</span><span class="sxs-lookup"><span data-stu-id="d3376-127">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="d3376-128">Si consiglia di virtualizzare un layout di griglia o elenco in modo da eseguire il rendering solo di un subset dei componenti in un determinato momento.</span><span class="sxs-lookup"><span data-stu-id="d3376-128">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="d3376-129">Per un esempio di rendering del subset di componenti, vedere i componenti seguenti nell' [app di esempio di virtualizzazione (archivio di GitHub ASPNET/Samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span><span class="sxs-lookup"><span data-stu-id="d3376-129">For an example of component subset rendering, see the following components in the [Virtualization sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="d3376-130">`Virtualize`Component ([Shared/virtualizzate. Razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): componente scritto in C# che implementa <xref:Microsoft.AspNetCore.Components.ComponentBase> per eseguire il rendering di un set di righe di dati meteo in base allo scorrimento dell'utente.</span><span class="sxs-lookup"><span data-stu-id="d3376-130">`Virtualize` component ([Shared/Virtualize.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="d3376-131">`FetchData`Component ([pages/fetchData. Razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): usa il `Virtualize` componente per visualizzare 25 righe di dati meteorologici alla volta.</span><span class="sxs-lookup"><span data-stu-id="d3376-131">`FetchData` component ([Pages/FetchData.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

## <a name="avoid-javascript-interop-to-marshal-data"></a><span data-ttu-id="d3376-132">Evitare l'interoperabilità JavaScript per il marshalling dei dati</span><span class="sxs-lookup"><span data-stu-id="d3376-132">Avoid JavaScript interop to marshal data</span></span>

<span data-ttu-id="d3376-133">In Blazor webassembly una chiamata di interoperabilità JavaScript (js) deve attraversare il limite webassembly-js.</span><span class="sxs-lookup"><span data-stu-id="d3376-133">In Blazor WebAssembly, a JavaScript (JS) interop call must traverse the WebAssembly-JS boundary.</span></span> <span data-ttu-id="d3376-134">La serializzazione e la deserializzazione del contenuto tra i due contesti crea un sovraccarico di elaborazione per l'app.</span><span class="sxs-lookup"><span data-stu-id="d3376-134">Serializing and deserializing content across the two contexts creates processing overhead for the app.</span></span> <span data-ttu-id="d3376-135">Le frequenti chiamate di interoperabilità JS spesso influiscono negativamente sulle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="d3376-135">Frequent JS interop calls often adversely affects performance.</span></span> <span data-ttu-id="d3376-136">Per ridurre il marshalling dei dati attraverso il limite, determinare se l'app può consolidare molti piccoli payload in un unico payload di grandi dimensioni per evitare il volume elevato di scambi di contesto tra webassembly e JS.</span><span class="sxs-lookup"><span data-stu-id="d3376-136">To reduce the marshalling of data across the boundary, determine if the app can consolidate many small payloads into a single large payload to avoid the high volume of context switching between WebAssembly and JS.</span></span>

## <a name="use-systemtextjson"></a><span data-ttu-id="d3376-137">Usare System. Text. JSON</span><span class="sxs-lookup"><span data-stu-id="d3376-137">Use System.Text.Json</span></span>

Blazor<span data-ttu-id="d3376-138">l'implementazione dell'interoperabilità JS di è basata su <xref:System.Text.Json> , ovvero una libreria di serializzazione JSON a prestazioni elevate con allocazione di memoria insufficiente.</span><span class="sxs-lookup"><span data-stu-id="d3376-138">'s JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="d3376-139">L'uso <xref:System.Text.Json> di non comporta dimensioni aggiuntive del payload dell'app rispetto all'aggiunta di una o più librerie JSON alternative.</span><span class="sxs-lookup"><span data-stu-id="d3376-139">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="d3376-140">Per informazioni aggiuntive sulla migrazione, vedere [How to migrate from Newtonsoft. JSON to System. Text. JSON](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="d3376-140">For migration guidance, see [How to migrate from Newtonsoft.Json to System.Text.Json](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a><span data-ttu-id="d3376-141">Usare API di interoperabilità JS sincrone e non Marshall laddove appropriato</span><span class="sxs-lookup"><span data-stu-id="d3376-141">Use synchronous and unmarshalled JS interop APIs where appropriate</span></span>

Blazor<span data-ttu-id="d3376-142">Webassembly offre due versioni aggiuntive di <xref:Microsoft.JSInterop.IJSRuntime> rispetto alla singola versione disponibile per le Blazor app Server:</span><span class="sxs-lookup"><span data-stu-id="d3376-142"> WebAssembly offers two additional versions of <xref:Microsoft.JSInterop.IJSRuntime> over the single version available to Blazor Server apps:</span></span>

* <span data-ttu-id="d3376-143"><xref:Microsoft.JSInterop.IJSInProcessRuntime>consente di richiamare in modo sincrono le chiamate di interoperabilità JS, che hanno un sovraccarico minore rispetto alle versioni asincrone:</span><span class="sxs-lookup"><span data-stu-id="d3376-143"><xref:Microsoft.JSInterop.IJSInProcessRuntime> allows invoking JS interop calls synchronously, which has less overhead than the asynchronous versions:</span></span>

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (IJSInProcessRuntime)JS;

          var value = jsInProcess.Invoke<string>("jsInteropCall");
      }
  }
  ```

* <span data-ttu-id="d3376-144"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime>consente le chiamate di interoperabilità JS non Marshall:</span><span class="sxs-lookup"><span data-stu-id="d3376-144"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permits unmarshalled JS interop calls:</span></span>

  ```javascript
  function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
  }
  ```

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (WebAssemblyJSRuntime)JS;

          var value = jsInProcess.InvokeUnmarshalled<string>("jsInteropCall");
      }
  }
  ```

  > [!WARNING]
  > <span data-ttu-id="d3376-145">Quando <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> si usa ha il minor sovraccarico degli approcci di interoperabilità js, le API JavaScript necessarie per interagire con queste API non sono attualmente documentate e sono soggette a modifiche di rilievo nelle versioni future.</span><span class="sxs-lookup"><span data-stu-id="d3376-145">While using <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

## <a name="reduce-app-size"></a><span data-ttu-id="d3376-146">Ridurre le dimensioni dell'app</span><span class="sxs-lookup"><span data-stu-id="d3376-146">Reduce app size</span></span>

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="d3376-147">Collegamento del linguaggio intermedio (IL)</span><span class="sxs-lookup"><span data-stu-id="d3376-147">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="d3376-148">[Collegamento di un Blazor L'app webassembly](xref:host-and-deploy/blazor/configure-linker) riduce le dimensioni dell'app eliminando il codice inutilizzato nei file binari dell'app.</span><span class="sxs-lookup"><span data-stu-id="d3376-148">[Linking a Blazor WebAssembly app](xref:host-and-deploy/blazor/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="d3376-149">Per impostazione predefinita, il linker viene abilitato solo quando si compila nella `Release` configurazione.</span><span class="sxs-lookup"><span data-stu-id="d3376-149">By default, the linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="d3376-150">Per trarre vantaggio da questo problema, pubblicare l'app per la distribuzione usando il comando [DotNet Publish](/dotnet/core/tools/dotnet-publish) con l'opzione [-c |--Configuration](/dotnet/core/tools/dotnet-publish#options) impostata su `Release` :</span><span class="sxs-lookup"><span data-stu-id="d3376-150">To benefit from this, publish the app for deployment using the [dotnet publish](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

```dotnetcli
dotnet publish -c Release
```

### <a name="disable-unused-features"></a><span data-ttu-id="d3376-151">Disabilitare le funzionalità inutilizzate</span><span class="sxs-lookup"><span data-stu-id="d3376-151">Disable unused features</span></span>

Blazor<span data-ttu-id="d3376-152">Il runtime di webassembly include le funzionalità .NET seguenti che possono essere disabilitate se l'app non le richiede per una dimensione di payload inferiore:</span><span class="sxs-lookup"><span data-stu-id="d3376-152"> WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="d3376-153">Un file di dati è incluso per rendere corrette le informazioni sul fuso orario.</span><span class="sxs-lookup"><span data-stu-id="d3376-153">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="d3376-154">Se l'app non richiede questa funzionalità, provare a disabilitarla impostando la `BlazorEnableTimeZoneSupport` Proprietà MSBuild nel file di progetto dell'app su `false` :</span><span class="sxs-lookup"><span data-stu-id="d3376-154">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* <span data-ttu-id="d3376-155">Le informazioni sulle regole di confronto sono incluse per fare in modo che le API <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> funzionino correttamente.</span><span class="sxs-lookup"><span data-stu-id="d3376-155">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="d3376-156">Se si è certi che l'app non richiede i dati delle regole di confronto, provare a disabilitarla impostando la `BlazorWebAssemblyPreserveCollationData` Proprietà MSBuild nel file di progetto dell'app su `false` :</span><span class="sxs-lookup"><span data-stu-id="d3376-156">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
