---
title: BlazorProcedure consigliate per le prestazioni di ASP.NET Core Webassembly
author: pranavkm
description: Suggerimenti per migliorare le prestazioni nelle Blazor app ASP.NET Core webassembly ed evitare problemi di prestazioni comuni.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: c5169231eec67a43830f761bff7585deff774613
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103932"
---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a><span data-ttu-id="29843-103">BlazorProcedure consigliate per le prestazioni di ASP.NET Core Webassembly</span><span class="sxs-lookup"><span data-stu-id="29843-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="29843-104">Di [il](https://github.com/pranavkm) suo</span><span class="sxs-lookup"><span data-stu-id="29843-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="29843-105">Questo articolo fornisce linee guida per ASP.NET Core Blazor procedure consigliate per le prestazioni di webassembly.</span><span class="sxs-lookup"><span data-stu-id="29843-105">This article provides guidelines for ASP.NET Core Blazor WebAssembly performance best practices.</span></span>

## <a name="avoid-unnecessary-component-renders"></a><span data-ttu-id="29843-106">Evitare il rendering di componenti superflui</span><span class="sxs-lookup"><span data-stu-id="29843-106">Avoid unnecessary component renders</span></span>

Blazor<span data-ttu-id="29843-107">l'algoritmo diffing evita di eseguire nuovamente il rendering di un componente quando l'algoritmo rileva che il componente non è stato modificato.</span><span class="sxs-lookup"><span data-stu-id="29843-107">'s diffing algorithm avoids rerendering a component when the algorithm perceives that the component hasn't changed.</span></span> <span data-ttu-id="29843-108">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> per un controllo con granularità fine sul rendering dei componenti.</span><span class="sxs-lookup"><span data-stu-id="29843-108">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> for fine-grained control over component rendering.</span></span>

<span data-ttu-id="29843-109">Se la creazione di un componente di solo interfaccia utente che non cambia mai dopo il rendering iniziale, configurare <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> per restituire `false` :</span><span class="sxs-lookup"><span data-stu-id="29843-109">If authoring a UI-only component that never changes after the initial render, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="29843-110">La maggior parte delle app non richiede un controllo con granularità fine, ma <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> può anche essere usata per eseguire il rendering selettivo di un componente che risponde a un evento dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="29843-110">Most apps don't require fine-grained control, but <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> can also be used to selectively render a component responding to a UI event.</span></span>

<span data-ttu-id="29843-111">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="29843-111">In the following example:</span></span>

* <span data-ttu-id="29843-112"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>viene sottoposto a override e impostato sul valore del <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> campo, che inizialmente si verifica `false` quando il componente viene caricato.</span><span class="sxs-lookup"><span data-stu-id="29843-112"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden and set to the value of the <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> field, which is initially `false` when the component loads.</span></span>
* <span data-ttu-id="29843-113">Quando il pulsante è selezionato, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> viene impostato su `true` , che forza il rendering del componente con l'oggetto aggiornato `currentCount` .</span><span class="sxs-lookup"><span data-stu-id="29843-113">When the button is selected, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is set to `true`, which forces the component to rerender with the updated `currentCount`.</span></span>
* <span data-ttu-id="29843-114">Subito dopo il rendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> imposta il valore di <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> nuovo su `false` per impedire un ulteriore rendering fino alla successiva selezione del pulsante.</span><span class="sxs-lookup"><span data-stu-id="29843-114">Immediately after rerendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sets the value of <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> back to `false` to prevent further rerendering until the next time the button is selected.</span></span>

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

<span data-ttu-id="29843-115">Per altre informazioni, vedere <xref:blazor/components/lifecycle#after-component-render>.</span><span class="sxs-lookup"><span data-stu-id="29843-115">For more information, see <xref:blazor/components/lifecycle#after-component-render>.</span></span>

## <a name="virtualize-re-usable-fragments"></a><span data-ttu-id="29843-116">Virtualizza i frammenti riutilizzabili</span><span class="sxs-lookup"><span data-stu-id="29843-116">Virtualize re-usable fragments</span></span>

<span data-ttu-id="29843-117">I componenti offrono un approccio pratico per produrre frammenti riutilizzabili di codice e markup.</span><span class="sxs-lookup"><span data-stu-id="29843-117">Components offer a convenient approach to produce re-usable fragments of code and markup.</span></span> <span data-ttu-id="29843-118">In generale, si consiglia di creare singoli componenti che meglio si allineano ai requisiti dell'app.</span><span class="sxs-lookup"><span data-stu-id="29843-118">In general, we recommend authoring individual components that best align with the app's requirements.</span></span> <span data-ttu-id="29843-119">Un avvertimento è che ogni componente figlio aggiuntivo contribuisce al tempo totale necessario per eseguire il rendering di un componente padre.</span><span class="sxs-lookup"><span data-stu-id="29843-119">One caveat is that each additional child component contributes to the total time it takes to render a parent component.</span></span> <span data-ttu-id="29843-120">Per la maggior parte delle app, l'overhead aggiuntivo è trascurabile.</span><span class="sxs-lookup"><span data-stu-id="29843-120">For most apps, the additional overhead is negligible.</span></span> <span data-ttu-id="29843-121">Le app che producono un numero elevato di componenti devono considerare l'uso di strategie per ridurre l'overhead di elaborazione, ad esempio limitando il numero di componenti sottoposti a rendering.</span><span class="sxs-lookup"><span data-stu-id="29843-121">Apps that produce a large number of components should consider using strategies to reduce processing overhead, such as limiting the number of rendered components.</span></span>

<span data-ttu-id="29843-122">Ad esempio, una griglia o un elenco che esegue il rendering di centinaia di righe contenenti componenti comporta un utilizzo intensivo del processore per il rendering.</span><span class="sxs-lookup"><span data-stu-id="29843-122">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="29843-123">Si consiglia di virtualizzare un layout di griglia o elenco in modo da eseguire il rendering solo di un subset dei componenti in un determinato momento.</span><span class="sxs-lookup"><span data-stu-id="29843-123">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="29843-124">Per un esempio di rendering del subset di componenti, vedere i componenti seguenti nell' [app di esempio di virtualizzazione (archivio di GitHub ASPNET/Samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span><span class="sxs-lookup"><span data-stu-id="29843-124">For an example of component subset rendering, see the following components in the [Virtualization sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="29843-125">`Virtualize`Component ([Shared/virtualizzate. Razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): componente scritto in C# che implementa <xref:Microsoft.AspNetCore.Components.ComponentBase> per eseguire il rendering di un set di righe di dati meteo in base allo scorrimento dell'utente.</span><span class="sxs-lookup"><span data-stu-id="29843-125">`Virtualize` component ([Shared/Virtualize.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="29843-126">`FetchData`Component ([pages/fetchData. Razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): usa il `Virtualize` componente per visualizzare 25 righe di dati meteorologici alla volta.</span><span class="sxs-lookup"><span data-stu-id="29843-126">`FetchData` component ([Pages/FetchData.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

## <a name="avoid-javascript-interop-to-marshal-data"></a><span data-ttu-id="29843-127">Evitare l'interoperabilità JavaScript per il marshalling dei dati</span><span class="sxs-lookup"><span data-stu-id="29843-127">Avoid JavaScript interop to marshal data</span></span>

<span data-ttu-id="29843-128">In Blazor webassembly una chiamata di interoperabilità JavaScript (js) deve attraversare il limite webassembly-js.</span><span class="sxs-lookup"><span data-stu-id="29843-128">In Blazor WebAssembly, a JavaScript (JS) interop call must traverse the WebAssembly-JS boundary.</span></span> <span data-ttu-id="29843-129">La serializzazione e la deserializzazione del contenuto tra i due contesti crea un sovraccarico di elaborazione per l'app.</span><span class="sxs-lookup"><span data-stu-id="29843-129">Serializing and deserializing content across the two contexts creates processing overhead for the app.</span></span> <span data-ttu-id="29843-130">Le frequenti chiamate di interoperabilità JS spesso influiscono negativamente sulle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="29843-130">Frequent JS interop calls often adversely affects performance.</span></span> <span data-ttu-id="29843-131">Per ridurre il marshalling dei dati attraverso il limite, determinare se l'app può consolidare molti piccoli payload in un unico payload di grandi dimensioni per evitare il volume elevato di scambi di contesto tra webassembly e JS.</span><span class="sxs-lookup"><span data-stu-id="29843-131">To reduce the marshalling of data across the boundary, determine if the app can consolidate many small payloads into a single large payload to avoid the high volume of context switching between WebAssembly and JS.</span></span>

## <a name="use-systemtextjson"></a><span data-ttu-id="29843-132">USA System.Text.Js</span><span class="sxs-lookup"><span data-stu-id="29843-132">Use System.Text.Json</span></span>

Blazor<span data-ttu-id="29843-133">l'implementazione dell'interoperabilità JS di è basata su <xref:System.Text.Json> , ovvero una libreria di serializzazione JSON a prestazioni elevate con allocazione di memoria insufficiente.</span><span class="sxs-lookup"><span data-stu-id="29843-133">'s JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="29843-134">L'uso <xref:System.Text.Json> di non comporta dimensioni aggiuntive del payload dell'app rispetto all'aggiunta di una o più librerie JSON alternative.</span><span class="sxs-lookup"><span data-stu-id="29843-134">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="29843-135">Per informazioni aggiuntive sulla migrazione, vedere [How to migrate from Newtonsoft.Json to System.Text.Json](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="29843-135">For migration guidance, see [How to migrate from Newtonsoft.Json to System.Text.Json](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a><span data-ttu-id="29843-136">Usare API di interoperabilità JS sincrone e non Marshall laddove appropriato</span><span class="sxs-lookup"><span data-stu-id="29843-136">Use synchronous and unmarshalled JS interop APIs where appropriate</span></span>

Blazor<span data-ttu-id="29843-137">Webassembly offre due versioni aggiuntive di <xref:Microsoft.JSInterop.IJSRuntime> rispetto alla singola versione disponibile per le Blazor app Server:</span><span class="sxs-lookup"><span data-stu-id="29843-137"> WebAssembly offers two additional versions of <xref:Microsoft.JSInterop.IJSRuntime> over the single version available to Blazor Server apps:</span></span>

* <span data-ttu-id="29843-138"><xref:Microsoft.JSInterop.IJSInProcessRuntime>consente di richiamare in modo sincrono le chiamate di interoperabilità JS, che hanno un sovraccarico minore rispetto alle versioni asincrone:</span><span class="sxs-lookup"><span data-stu-id="29843-138"><xref:Microsoft.JSInterop.IJSInProcessRuntime> allows invoking JS interop calls synchronously, which has less overhead than the asynchronous versions:</span></span>

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

* <span data-ttu-id="29843-139"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime>consente le chiamate di interoperabilità JS non Marshall:</span><span class="sxs-lookup"><span data-stu-id="29843-139"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permits unmarshalled JS interop calls:</span></span>

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
  > <span data-ttu-id="29843-140">Quando <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> si usa ha il minor sovraccarico degli approcci di interoperabilità js, le API JavaScript necessarie per interagire con queste API non sono attualmente documentate e sono soggette a modifiche di rilievo nelle versioni future.</span><span class="sxs-lookup"><span data-stu-id="29843-140">While using <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

## <a name="reduce-app-size"></a><span data-ttu-id="29843-141">Ridurre le dimensioni dell'app</span><span class="sxs-lookup"><span data-stu-id="29843-141">Reduce app size</span></span>

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="29843-142">Collegamento del linguaggio intermedio (IL)</span><span class="sxs-lookup"><span data-stu-id="29843-142">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="29843-143">[Collegamento di un Blazor L'app webassembly](xref:blazor/host-and-deploy/configure-linker) riduce le dimensioni dell'app eliminando il codice inutilizzato nei file binari dell'app.</span><span class="sxs-lookup"><span data-stu-id="29843-143">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="29843-144">Per impostazione predefinita, il linker viene abilitato solo quando si compila nella `Release` configurazione.</span><span class="sxs-lookup"><span data-stu-id="29843-144">By default, the linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="29843-145">Per trarre vantaggio da questo problema, pubblicare l'app per la distribuzione usando il comando [DotNet Publish](/dotnet/core/tools/dotnet-publish) con l'opzione [-c |--Configuration](/dotnet/core/tools/dotnet-publish#options) impostata su `Release` :</span><span class="sxs-lookup"><span data-stu-id="29843-145">To benefit from this, publish the app for deployment using the [dotnet publish](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

```dotnetcli
dotnet publish -c Release
```

### <a name="compression"></a><span data-ttu-id="29843-146">Compressione</span><span class="sxs-lookup"><span data-stu-id="29843-146">Compression</span></span>

<span data-ttu-id="29843-147">Quando Blazor viene pubblicata un'app webassembly, l'output viene compresso in modo statico durante la pubblicazione per ridurre le dimensioni dell'app e rimuovere l'overhead per la compressione in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="29843-147">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> Blazor<span data-ttu-id="29843-148">si basa sul server per eseguire il contenuto negotation e gestire file compressi in modo statico.</span><span class="sxs-lookup"><span data-stu-id="29843-148"> relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="29843-149">Dopo che un'app è stata distribuita, verificare che l'app servi i file compressi.</span><span class="sxs-lookup"><span data-stu-id="29843-149">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="29843-150">Controllare la scheda rete nel Strumenti di sviluppo del browser e verificare che i file siano serviti con `Content-Encoding: br` o `Content-Encoding: gz` .</span><span class="sxs-lookup"><span data-stu-id="29843-150">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="29843-151">Se l'host non serve file compressi, seguire le istruzioni riportate in <xref:blazor/host-and-deploy/webassembly#compression> .</span><span class="sxs-lookup"><span data-stu-id="29843-151">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="29843-152">Disabilitare le funzionalità inutilizzate</span><span class="sxs-lookup"><span data-stu-id="29843-152">Disable unused features</span></span>

Blazor<span data-ttu-id="29843-153">Il runtime di webassembly include le funzionalità .NET seguenti che possono essere disabilitate se l'app non le richiede per una dimensione di payload inferiore:</span><span class="sxs-lookup"><span data-stu-id="29843-153"> WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="29843-154">Un file di dati è incluso per rendere corrette le informazioni sul fuso orario.</span><span class="sxs-lookup"><span data-stu-id="29843-154">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="29843-155">Se l'app non richiede questa funzionalità, provare a disabilitarla impostando la `BlazorEnableTimeZoneSupport` Proprietà MSBuild nel file di progetto dell'app su `false` :</span><span class="sxs-lookup"><span data-stu-id="29843-155">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* <span data-ttu-id="29843-156">Le informazioni sulle regole di confronto sono incluse per fare in modo che le API <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> funzionino correttamente.</span><span class="sxs-lookup"><span data-stu-id="29843-156">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="29843-157">Se si è certi che l'app non richiede i dati delle regole di confronto, provare a disabilitarla impostando la `BlazorWebAssemblyPreserveCollationData` Proprietà MSBuild nel file di progetto dell'app su `false` :</span><span class="sxs-lookup"><span data-stu-id="29843-157">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
