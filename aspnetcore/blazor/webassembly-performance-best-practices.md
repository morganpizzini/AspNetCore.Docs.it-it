---
title: Blazor WebAssemblyProcedure consigliate per le prestazioni ASP.NET Core
author: pranavkm
description: Suggerimenti per migliorare le prestazioni in ASP.NET Core Blazor WebAssembly app ed evitare problemi comuni relativi alle prestazioni.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/09/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: d1ad646f82e5c9ba611a60fc9be8378bedef8dee
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2020
ms.locfileid: "90721723"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a><span data-ttu-id="7446a-103">Blazor WebAssemblyProcedure consigliate per le prestazioni ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7446a-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="7446a-104">Di [il](https://github.com/pranavkm) suo</span><span class="sxs-lookup"><span data-stu-id="7446a-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="7446a-105">Questo articolo fornisce linee guida per ASP.NET Core Blazor WebAssembly procedure consigliate per le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="7446a-105">This article provides guidelines for ASP.NET Core Blazor WebAssembly performance best practices.</span></span>

## <a name="avoid-unnecessary-component-renders"></a><span data-ttu-id="7446a-106">Evitare il rendering di componenti superflui</span><span class="sxs-lookup"><span data-stu-id="7446a-106">Avoid unnecessary component renders</span></span>

<span data-ttu-id="7446a-107">Blazorl'algoritmo diffing evita di eseguire nuovamente il rendering di un componente quando l'algoritmo rileva che il componente non è stato modificato.</span><span class="sxs-lookup"><span data-stu-id="7446a-107">Blazor's diffing algorithm avoids rerendering a component when the algorithm perceives that the component hasn't changed.</span></span> <span data-ttu-id="7446a-108">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> per un controllo con granularità fine sul rendering dei componenti.</span><span class="sxs-lookup"><span data-stu-id="7446a-108">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> for fine-grained control over component rendering.</span></span>

<span data-ttu-id="7446a-109">Se la creazione di un componente di solo interfaccia utente che non cambia mai dopo il rendering iniziale, configurare <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> per restituire `false` :</span><span class="sxs-lookup"><span data-stu-id="7446a-109">If authoring a UI-only component that never changes after the initial render, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="7446a-110">La maggior parte delle app non richiede un controllo con granularità fine, ma <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> può essere usata per eseguire il rendering selettivo di un componente che risponde a un evento dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="7446a-110">Most apps don't require fine-grained control, but <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> can be used to selectively render a component responding to a UI event.</span></span> <span data-ttu-id="7446a-111">L'utilizzo di <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> può essere importante anche negli scenari in cui viene eseguito il rendering di un numero elevato di componenti.</span><span class="sxs-lookup"><span data-stu-id="7446a-111">Using <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> might also be important in scenarios where a large number of components are rendered.</span></span> <span data-ttu-id="7446a-112">Si consideri una griglia in cui l'utilizzo di <xref:Microsoft.AspNetCore.Components.EventCallback> in un componente di una cella della griglia chiama <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> sulla griglia.</span><span class="sxs-lookup"><span data-stu-id="7446a-112">Consider a grid, where use of <xref:Microsoft.AspNetCore.Components.EventCallback> in one component in one cell of the grid calls <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> on the grid.</span></span> <span data-ttu-id="7446a-113"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>La chiamata a genera un nuovo rendering di ogni componente figlio.</span><span class="sxs-lookup"><span data-stu-id="7446a-113">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes a re-render of every child component.</span></span> <span data-ttu-id="7446a-114">Se solo un numero ridotto di celle richiede il rirendering, utilizzare <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> per evitare la riduzione delle prestazioni dei rendering non necessari.</span><span class="sxs-lookup"><span data-stu-id="7446a-114">If only a small number of cells require rerendering, use <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to avoid the performance penalty of unnecessary renders.</span></span>

<span data-ttu-id="7446a-115">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="7446a-115">In the following example:</span></span>

* <span data-ttu-id="7446a-116"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> viene sottoposto a override e impostato sul valore del <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> campo, che inizialmente si verifica `false` quando il componente viene caricato.</span><span class="sxs-lookup"><span data-stu-id="7446a-116"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden and set to the value of the <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> field, which is initially `false` when the component loads.</span></span>
* <span data-ttu-id="7446a-117">Quando il pulsante è selezionato, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> viene impostato su `true` , che forza il rendering del componente con l'oggetto aggiornato `currentCount` .</span><span class="sxs-lookup"><span data-stu-id="7446a-117">When the button is selected, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is set to `true`, which forces the component to rerender with the updated `currentCount`.</span></span>
* <span data-ttu-id="7446a-118">Subito dopo il rendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> imposta il valore di <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> nuovo su `false` per impedire un ulteriore rendering fino alla successiva selezione del pulsante.</span><span class="sxs-lookup"><span data-stu-id="7446a-118">Immediately after rerendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sets the value of <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> back to `false` to prevent further rerendering until the next time the button is selected.</span></span>

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

<span data-ttu-id="7446a-119">Per altre informazioni, vedere <xref:blazor/components/lifecycle#after-component-render>.</span><span class="sxs-lookup"><span data-stu-id="7446a-119">For more information, see <xref:blazor/components/lifecycle#after-component-render>.</span></span>

## <a name="virtualize-re-usable-fragments"></a><span data-ttu-id="7446a-120">Virtualizza i frammenti riutilizzabili</span><span class="sxs-lookup"><span data-stu-id="7446a-120">Virtualize re-usable fragments</span></span>

<span data-ttu-id="7446a-121">I componenti offrono un approccio pratico per produrre frammenti riutilizzabili di codice e markup.</span><span class="sxs-lookup"><span data-stu-id="7446a-121">Components offer a convenient approach to produce re-usable fragments of code and markup.</span></span> <span data-ttu-id="7446a-122">In generale, si consiglia di creare singoli componenti che meglio si allineano ai requisiti dell'app.</span><span class="sxs-lookup"><span data-stu-id="7446a-122">In general, we recommend authoring individual components that best align with the app's requirements.</span></span> <span data-ttu-id="7446a-123">Un avvertimento è che ogni componente figlio aggiuntivo contribuisce al tempo totale necessario per eseguire il rendering di un componente padre.</span><span class="sxs-lookup"><span data-stu-id="7446a-123">One caveat is that each additional child component contributes to the total time it takes to render a parent component.</span></span> <span data-ttu-id="7446a-124">Per la maggior parte delle app, l'overhead aggiuntivo è trascurabile.</span><span class="sxs-lookup"><span data-stu-id="7446a-124">For most apps, the additional overhead is negligible.</span></span> <span data-ttu-id="7446a-125">Le app che producono un numero elevato di componenti devono considerare l'uso di strategie per ridurre l'overhead di elaborazione, ad esempio limitando il numero di componenti sottoposti a rendering.</span><span class="sxs-lookup"><span data-stu-id="7446a-125">Apps that produce a large number of components should consider using strategies to reduce processing overhead, such as limiting the number of rendered components.</span></span>

<span data-ttu-id="7446a-126">Per altre informazioni, vedere <xref:blazor/components/virtualization>.</span><span class="sxs-lookup"><span data-stu-id="7446a-126">For more information, see <xref:blazor/components/virtualization>.</span></span>

## <a name="avoid-javascript-interop-to-marshal-data"></a><span data-ttu-id="7446a-127">Evitare l'interoperabilità JavaScript per il marshalling dei dati</span><span class="sxs-lookup"><span data-stu-id="7446a-127">Avoid JavaScript interop to marshal data</span></span>

<span data-ttu-id="7446a-128">In Blazor WebAssembly una chiamata di interoperabilità JavaScript (js) deve attraversare il limite webassembly-js.</span><span class="sxs-lookup"><span data-stu-id="7446a-128">In Blazor WebAssembly, a JavaScript (JS) interop call must traverse the WebAssembly-JS boundary.</span></span> <span data-ttu-id="7446a-129">La serializzazione e la deserializzazione del contenuto tra i due contesti crea un sovraccarico di elaborazione per l'app.</span><span class="sxs-lookup"><span data-stu-id="7446a-129">Serializing and deserializing content across the two contexts creates processing overhead for the app.</span></span> <span data-ttu-id="7446a-130">Le frequenti chiamate di interoperabilità JS spesso influiscono negativamente sulle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="7446a-130">Frequent JS interop calls often adversely affects performance.</span></span> <span data-ttu-id="7446a-131">Per ridurre il marshalling dei dati attraverso il limite, determinare se l'app può consolidare molti piccoli payload in un unico payload di grandi dimensioni per evitare il volume elevato di scambi di contesto tra webassembly e JS.</span><span class="sxs-lookup"><span data-stu-id="7446a-131">To reduce the marshalling of data across the boundary, determine if the app can consolidate many small payloads into a single large payload to avoid the high volume of context switching between WebAssembly and JS.</span></span>

## <a name="use-systemtextjson"></a><span data-ttu-id="7446a-132">USA System.Text.Js</span><span class="sxs-lookup"><span data-stu-id="7446a-132">Use System.Text.Json</span></span>

<span data-ttu-id="7446a-133">Blazorl'implementazione dell'interoperabilità JS di è basata su <xref:System.Text.Json> , ovvero una libreria di serializzazione JSON a prestazioni elevate con allocazione di memoria insufficiente.</span><span class="sxs-lookup"><span data-stu-id="7446a-133">Blazor's JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="7446a-134">L'uso <xref:System.Text.Json> di non comporta dimensioni aggiuntive del payload dell'app rispetto all'aggiunta di una o più librerie JSON alternative.</span><span class="sxs-lookup"><span data-stu-id="7446a-134">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="7446a-135">Per informazioni aggiuntive sulla migrazione, vedere [come eseguire la migrazione da `Newtonsoft.Json` a `System.Text.Json` ](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="7446a-135">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a><span data-ttu-id="7446a-136">Usare API di interoperabilità JS sincrone e non Marshall laddove appropriato</span><span class="sxs-lookup"><span data-stu-id="7446a-136">Use synchronous and unmarshalled JS interop APIs where appropriate</span></span>

<span data-ttu-id="7446a-137">Blazor WebAssembly offre due versioni aggiuntive di <xref:Microsoft.JSInterop.IJSRuntime> rispetto alla versione singola disponibili per le Blazor Server app:</span><span class="sxs-lookup"><span data-stu-id="7446a-137">Blazor WebAssembly offers two additional versions of <xref:Microsoft.JSInterop.IJSRuntime> over the single version available to Blazor Server apps:</span></span>

* <span data-ttu-id="7446a-138"><xref:Microsoft.JSInterop.IJSInProcessRuntime> consente di richiamare in modo sincrono le chiamate di interoperabilità JS, che hanno un sovraccarico minore rispetto alle versioni asincrone:</span><span class="sxs-lookup"><span data-stu-id="7446a-138"><xref:Microsoft.JSInterop.IJSInProcessRuntime> allows invoking JS interop calls synchronously, which has less overhead than the asynchronous versions:</span></span>

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

* <span data-ttu-id="7446a-139"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> consente le chiamate di interoperabilità JS non Marshall:</span><span class="sxs-lookup"><span data-stu-id="7446a-139"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permits unmarshalled JS interop calls:</span></span>

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
  > <span data-ttu-id="7446a-140">Quando <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> si usa ha il minor sovraccarico degli approcci di interoperabilità js, le API JavaScript necessarie per interagire con queste API non sono attualmente documentate e sono soggette a modifiche di rilievo nelle versioni future.</span><span class="sxs-lookup"><span data-stu-id="7446a-140">While using <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

## <a name="reduce-app-size"></a><span data-ttu-id="7446a-141">Ridurre le dimensioni dell'app</span><span class="sxs-lookup"><span data-stu-id="7446a-141">Reduce app size</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a><span data-ttu-id="7446a-142">Troncamento del linguaggio intermedio (IL)</span><span class="sxs-lookup"><span data-stu-id="7446a-142">Intermediate Language (IL) trimming</span></span>

<span data-ttu-id="7446a-143">Il [trimming degli assembly inutilizzati da un' Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-trimmer) consente di ridurre le dimensioni dell'app rimuovendo il codice inutilizzato nei file binari dell'app.</span><span class="sxs-lookup"><span data-stu-id="7446a-143">[Trimming unused assemblies from a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-trimmer) reduces the app's size by removing unused code in the app's binaries.</span></span> <span data-ttu-id="7446a-144">Per impostazione predefinita, il trimmer viene eseguito durante la pubblicazione di un'applicazione.</span><span class="sxs-lookup"><span data-stu-id="7446a-144">By default, the Trimmer is executed when publishing an application.</span></span> <span data-ttu-id="7446a-145">Per trarre vantaggio dal taglio, pubblicare l'app per la distribuzione usando il [`dotnet publish`](/dotnet/core/tools/dotnet-publish) comando con l'opzione [-c |--Configuration](/dotnet/core/tools/dotnet-publish#options) impostata su `Release` :</span><span class="sxs-lookup"><span data-stu-id="7446a-145">To benefit from trimming, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="7446a-146">Collegamento del linguaggio intermedio (IL)</span><span class="sxs-lookup"><span data-stu-id="7446a-146">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="7446a-147">Il [collegamento di un' Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) consente di ridurre le dimensioni dell'app, ritagliando il codice inutilizzato nei file binari dell'app.</span><span class="sxs-lookup"><span data-stu-id="7446a-147">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="7446a-148">Per impostazione predefinita, il linker linguaggio intermedio (IL) viene abilitato solo quando si compila nella `Release` configurazione.</span><span class="sxs-lookup"><span data-stu-id="7446a-148">By default, the Intermediate Language (IL) Linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="7446a-149">Per trarre vantaggio da questo problema, pubblicare l'app per la distribuzione usando il [`dotnet publish`](/dotnet/core/tools/dotnet-publish) comando con l'opzione [-c |--Configuration](/dotnet/core/tools/dotnet-publish#options) impostata su `Release` :</span><span class="sxs-lookup"><span data-stu-id="7446a-149">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="lazy-load-assemblies"></a><span data-ttu-id="7446a-150">Assembly di caricamento lazy</span><span class="sxs-lookup"><span data-stu-id="7446a-150">Lazy load assemblies</span></span>

<span data-ttu-id="7446a-151">Caricare gli assembly in fase di esecuzione quando gli assembly sono necessari per una route.</span><span class="sxs-lookup"><span data-stu-id="7446a-151">Load assemblies at runtime when the assemblies are required by a route.</span></span> <span data-ttu-id="7446a-152">Per altre informazioni, vedere <xref:blazor/webassembly-lazy-load-assemblies>.</span><span class="sxs-lookup"><span data-stu-id="7446a-152">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="compression"></a><span data-ttu-id="7446a-153">Compressione</span><span class="sxs-lookup"><span data-stu-id="7446a-153">Compression</span></span>

<span data-ttu-id="7446a-154">Quando Blazor WebAssembly viene pubblicata un'app, l'output viene compresso in modo statico durante la pubblicazione per ridurre le dimensioni dell'app e rimuovere l'overhead per la compressione in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="7446a-154">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="7446a-155">Blazor si basa sul server per eseguire il contenuto negotation e gestire file compressi in modo statico.</span><span class="sxs-lookup"><span data-stu-id="7446a-155">Blazor relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="7446a-156">Dopo che un'app è stata distribuita, verificare che l'app servi i file compressi.</span><span class="sxs-lookup"><span data-stu-id="7446a-156">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="7446a-157">Controllare la scheda rete nel Strumenti di sviluppo del browser e verificare che i file siano serviti con `Content-Encoding: br` o `Content-Encoding: gz` .</span><span class="sxs-lookup"><span data-stu-id="7446a-157">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="7446a-158">Se l'host non serve file compressi, seguire le istruzioni riportate in <xref:blazor/host-and-deploy/webassembly#compression> .</span><span class="sxs-lookup"><span data-stu-id="7446a-158">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="7446a-159">Disabilitare le funzionalità inutilizzate</span><span class="sxs-lookup"><span data-stu-id="7446a-159">Disable unused features</span></span>

<span data-ttu-id="7446a-160">Blazor WebAssemblyil runtime di include le seguenti funzionalità .NET che possono essere disabilitate se l'app non le richiede per una dimensione di payload inferiore:</span><span class="sxs-lookup"><span data-stu-id="7446a-160">Blazor WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="7446a-161">Un file di dati è incluso per rendere corrette le informazioni sul fuso orario.</span><span class="sxs-lookup"><span data-stu-id="7446a-161">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="7446a-162">Se l'app non richiede questa funzionalità, provare a disabilitarla impostando la `BlazorEnableTimeZoneSupport` Proprietà MSBuild nel file di progetto dell'app su `false` :</span><span class="sxs-lookup"><span data-stu-id="7446a-162">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="7446a-163">Per impostazione predefinita, include Blazor WebAssembly le risorse di globalizzazione necessarie per visualizzare i valori, ad esempio le date e la valuta, nelle impostazioni cultura dell'utente.</span><span class="sxs-lookup"><span data-stu-id="7446a-163">By default, Blazor WebAssembly carries globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="7446a-164">Se l'app non richiede la localizzazione, è possibile [configurare l'app in modo che supporti la lingua inglese](xref:blazor/globalization-localization), che è basata sulle `en-US` impostazioni cultura:</span><span class="sxs-lookup"><span data-stu-id="7446a-164">If the app doesn't require localization, you may [configure the app to support the invariant culture](xref:blazor/globalization-localization), which is based on the `en-US` culture:</span></span>

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="7446a-165">Le informazioni sulle regole di confronto sono incluse per fare in modo che le API <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> funzionino correttamente.</span><span class="sxs-lookup"><span data-stu-id="7446a-165">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="7446a-166">Se si è certi che l'app non richiede i dati delle regole di confronto, provare a disabilitarla impostando la `BlazorWebAssemblyPreserveCollationData` Proprietà MSBuild nel file di progetto dell'app su `false` :</span><span class="sxs-lookup"><span data-stu-id="7446a-166">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
