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
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a>BlazorProcedure consigliate per le prestazioni di ASP.NET Core Webassembly

Di [il](https://github.com/pranavkm) suo

Questo articolo fornisce linee guida per ASP.NET Core Blazor procedure consigliate per le prestazioni di webassembly.

## <a name="avoid-unnecessary-component-renders"></a>Evitare il rendering di componenti superflui

Blazorl'algoritmo diffing evita di eseguire nuovamente il rendering di un componente quando l'algoritmo rileva che il componente non è stato modificato. Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> per un controllo con granularità fine sul rendering dei componenti.

Se la creazione di un componente di solo interfaccia utente che non cambia mai dopo il rendering iniziale, configurare <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> per restituire `false` :

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

La maggior parte delle app non richiede un controllo con granularità fine, ma <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> può anche essere usata per eseguire il rendering selettivo di un componente che risponde a un evento dell'interfaccia utente.

Nell'esempio seguente:

* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>viene sottoposto a override e impostato sul valore del <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> campo, che inizialmente si verifica `false` quando il componente viene caricato.
* Quando il pulsante è selezionato, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> viene impostato su `true` , che forza il rendering del componente con l'oggetto aggiornato `currentCount` .
* Subito dopo il rendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> imposta il valore di <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> nuovo su `false` per impedire un ulteriore rendering fino alla successiva selezione del pulsante.

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

Per altre informazioni, vedere <xref:blazor/components/lifecycle#after-component-render>.

## <a name="virtualize-re-usable-fragments"></a>Virtualizza i frammenti riutilizzabili

I componenti offrono un approccio pratico per produrre frammenti riutilizzabili di codice e markup. In generale, si consiglia di creare singoli componenti che meglio si allineano ai requisiti dell'app. Un avvertimento è che ogni componente figlio aggiuntivo contribuisce al tempo totale necessario per eseguire il rendering di un componente padre. Per la maggior parte delle app, l'overhead aggiuntivo è trascurabile. Le app che producono un numero elevato di componenti devono considerare l'uso di strategie per ridurre l'overhead di elaborazione, ad esempio limitando il numero di componenti sottoposti a rendering.

Ad esempio, una griglia o un elenco che esegue il rendering di centinaia di righe contenenti componenti comporta un utilizzo intensivo del processore per il rendering. Si consiglia di virtualizzare un layout di griglia o elenco in modo da eseguire il rendering solo di un subset dei componenti in un determinato momento. Per un esempio di rendering del subset di componenti, vedere i componenti seguenti nell' [app di esempio di virtualizzazione (archivio di GitHub ASPNET/Samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):

* `Virtualize`Component ([Shared/virtualizzate. Razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): componente scritto in C# che implementa <xref:Microsoft.AspNetCore.Components.ComponentBase> per eseguire il rendering di un set di righe di dati meteo in base allo scorrimento dell'utente.
* `FetchData`Component ([pages/fetchData. Razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): usa il `Virtualize` componente per visualizzare 25 righe di dati meteorologici alla volta.

## <a name="avoid-javascript-interop-to-marshal-data"></a>Evitare l'interoperabilità JavaScript per il marshalling dei dati

In Blazor webassembly una chiamata di interoperabilità JavaScript (js) deve attraversare il limite webassembly-js. La serializzazione e la deserializzazione del contenuto tra i due contesti crea un sovraccarico di elaborazione per l'app. Le frequenti chiamate di interoperabilità JS spesso influiscono negativamente sulle prestazioni. Per ridurre il marshalling dei dati attraverso il limite, determinare se l'app può consolidare molti piccoli payload in un unico payload di grandi dimensioni per evitare il volume elevato di scambi di contesto tra webassembly e JS.

## <a name="use-systemtextjson"></a>USA System.Text.Js

Blazorl'implementazione dell'interoperabilità JS di è basata su <xref:System.Text.Json> , ovvero una libreria di serializzazione JSON a prestazioni elevate con allocazione di memoria insufficiente. L'uso <xref:System.Text.Json> di non comporta dimensioni aggiuntive del payload dell'app rispetto all'aggiunta di una o più librerie JSON alternative.

Per informazioni aggiuntive sulla migrazione, vedere [How to migrate from Newtonsoft.Json to System.Text.Json](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a>Usare API di interoperabilità JS sincrone e non Marshall laddove appropriato

BlazorWebassembly offre due versioni aggiuntive di <xref:Microsoft.JSInterop.IJSRuntime> rispetto alla singola versione disponibile per le Blazor app Server:

* <xref:Microsoft.JSInterop.IJSInProcessRuntime>consente di richiamare in modo sincrono le chiamate di interoperabilità JS, che hanno un sovraccarico minore rispetto alle versioni asincrone:

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

* <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime>consente le chiamate di interoperabilità JS non Marshall:

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
  > Quando <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> si usa ha il minor sovraccarico degli approcci di interoperabilità js, le API JavaScript necessarie per interagire con queste API non sono attualmente documentate e sono soggette a modifiche di rilievo nelle versioni future.

## <a name="reduce-app-size"></a>Ridurre le dimensioni dell'app

### <a name="intermediate-language-il-linking"></a>Collegamento del linguaggio intermedio (IL)

[Collegamento di un Blazor L'app webassembly](xref:blazor/host-and-deploy/configure-linker) riduce le dimensioni dell'app eliminando il codice inutilizzato nei file binari dell'app. Per impostazione predefinita, il linker viene abilitato solo quando si compila nella `Release` configurazione. Per trarre vantaggio da questo problema, pubblicare l'app per la distribuzione usando il comando [DotNet Publish](/dotnet/core/tools/dotnet-publish) con l'opzione [-c |--Configuration](/dotnet/core/tools/dotnet-publish#options) impostata su `Release` :

```dotnetcli
dotnet publish -c Release
```

### <a name="compression"></a>Compressione

Quando Blazor viene pubblicata un'app webassembly, l'output viene compresso in modo statico durante la pubblicazione per ridurre le dimensioni dell'app e rimuovere l'overhead per la compressione in fase di esecuzione. Blazorsi basa sul server per eseguire il contenuto negotation e gestire file compressi in modo statico.

Dopo che un'app è stata distribuita, verificare che l'app servi i file compressi. Controllare la scheda rete nel Strumenti di sviluppo del browser e verificare che i file siano serviti con `Content-Encoding: br` o `Content-Encoding: gz` . Se l'host non serve file compressi, seguire le istruzioni riportate in <xref:blazor/host-and-deploy/webassembly#compression> .

### <a name="disable-unused-features"></a>Disabilitare le funzionalità inutilizzate

BlazorIl runtime di webassembly include le funzionalità .NET seguenti che possono essere disabilitate se l'app non le richiede per una dimensione di payload inferiore:

* Un file di dati è incluso per rendere corrette le informazioni sul fuso orario. Se l'app non richiede questa funzionalità, provare a disabilitarla impostando la `BlazorEnableTimeZoneSupport` Proprietà MSBuild nel file di progetto dell'app su `false` :

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* Le informazioni sulle regole di confronto sono incluse per fare in modo che le API <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> funzionino correttamente. Se si è certi che l'app non richiede i dati delle regole di confronto, provare a disabilitarla impostando la `BlazorWebAssemblyPreserveCollationData` Proprietà MSBuild nel file di progetto dell'app su `false` :

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
