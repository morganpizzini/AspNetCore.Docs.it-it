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
# <a name="aspnet-core-opno-locblazor-lifecycle"></a>ASP.NET Blazor ciclo di vita di ASP.NET Core

Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

Il Blazor framework include metodi sincroni e asincroni del ciclo di vita. Eseguire l'override dei metodi del ciclo di vita per eseguire operazioni aggiuntive sui componenti durante l'inizializzazione e il rendering dei componenti.

## <a name="lifecycle-methods"></a>Metodi del ciclo di vita

### <a name="component-initialization-methods"></a>Metodi di inizializzazione dei componenti

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> vengono richiamati quando il componente viene inizializzato dopo aver ricevuto i parametri iniziali dal componente padre. Utilizzare `OnInitializedAsync` quando il componente esegue un'operazione asincrona e deve aggiornare al termine dell'operazione.

Per un'operazione `OnInitialized`sincrona, eseguire l'override di :

```csharp
protected override void OnInitialized()
{
    ...
}
```

Per eseguire un'operazione `OnInitializedAsync` asincrona, eseguire l'override e utilizzare la `await` parola chiave sull'operazione:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

BlazorLe app server che `OnInitializedAsync` [eseguono il prerendering](xref:blazor/hosting-model-configuration#render-mode) della chiamata al contenuto due **_volte:_**

* Una volta quando il componente viene inizialmente sottoposto a rendering in modo statico come parte della pagina.
* Una seconda volta quando il browser stabilisce una connessione al server.

Per impedire l'esecuzione `OnInitializedAsync` del codice dello sviluppatore due volte, vedere la sezione [Riconnessione con stato dopo il prerendering.](#stateful-reconnection-after-prerendering)

Mentre Blazor un'app Server esegue il prerendering, alcune azioni, ad esempio la chiamata in JavaScript, non sono possibili perché non è stata stabilita una connessione con il browser. Potrebbe essere necessario eseguire il rendering dei componenti in modo diverso quando viene eseguito il prerendering. Per altre informazioni, vedere la sezione [Rilevare il prerendering dell'app.](#detect-when-the-app-is-prerendering)

Se sono impostati gestori eventi, sganciarli durante l'eliminazione. Per altre informazioni, vedere la sezione Eliminazione dei componenti con IDisposable.For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.

### <a name="before-parameters-are-set"></a>Prima dell'impostazione dei parametri

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*>imposta i parametri forniti dall'elemento padre del componente nell'albero di rendering:

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView>contiene l'intero insieme di `SetParametersAsync` valori dei parametri ogni volta che viene chiamato.

L'implementazione `SetParametersAsync` predefinita di imposta il `[Parameter]` `[CascadingParameter]` valore di ogni proprietà `ParameterView`con l'attributo o che ha un valore corrispondente nell'oggetto . I parametri che non hanno `ParameterView` un valore corrispondente in vengono lasciati invariati.

Se `base.SetParametersAync` non viene richiamato, il codice personalizzato può interpretare il valore dei parametri in ingresso in qualsiasi modo richiesto. Ad esempio, non è necessario assegnare i parametri in ingresso alle proprietà della classe.

Se sono impostati gestori eventi, sganciarli durante l'eliminazione. Per altre informazioni, vedere la sezione Eliminazione dei componenti con IDisposable.For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.

### <a name="after-parameters-are-set"></a>Dopo aver impostato i parametri

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> sono chiamati:

* Quando il componente viene inizializzato e ha ricevuto il primo set di parametri dal componente padre.
* Quando il componente padre esegue nuovamente il rendering e fornisce:
  * Solo i tipi primitivi noti non modificabili di cui almeno un parametro è stato modificato.
  * Qualsiasi parametro di tipo complesso. Il framework non può sapere se i valori di un parametro di tipo complesso sono mutati internamente, pertanto considera il set di parametri come modificato.

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> Il lavoro asincrono quando si applicano `OnParametersSetAsync` parametri e valori di proprietà deve verificarsi durante l'evento del ciclo di vita.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

Se sono impostati gestori eventi, sganciarli durante l'eliminazione. Per altre informazioni, vedere la sezione Eliminazione dei componenti con IDisposable.For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.

### <a name="after-component-render"></a>Dopo il rendering del componente

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> vengono chiamati al termine del rendering di un componente. A questo punto i riferimenti a elementi e componenti vengono popolati. Utilizzare questa fase per eseguire passaggi di inizializzazione aggiuntivi utilizzando il contenuto di cui è stato eseguito il rendering, ad esempio l'attivazione di librerie JavaScript di terze parti che operano sugli elementi DOM di cui è stato eseguito il rendering.

Il `firstRender` parametro per `OnAfterRenderAsync` e `OnAfterRender`:

* Viene impostato `true` sulla prima volta che viene eseguito il rendering dell'istanza del componente.
* Può essere utilizzato per garantire che il lavoro di inizializzazione venga eseguito una sola volta.

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
> Il lavoro asincrono immediatamente `OnAfterRenderAsync` dopo il rendering deve verificarsi durante l'evento del ciclo di vita.
>
> Anche se si <xref:System.Threading.Tasks.Task> `OnAfterRenderAsync`restituisce un from , il framework non pianifica un ulteriore ciclo di rendering per il componente una volta completata l'attività. Questo per evitare un ciclo di rendering infinito. È diverso dagli altri metodi del ciclo di vita, che pianificano un ulteriore ciclo di rendering una volta completata l'attività restituita.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

`OnAfterRender`e `OnAfterRenderAsync` *non vengono chiamati durante il prerendering sul server.*

Se sono impostati gestori eventi, sganciarli durante l'eliminazione. Per altre informazioni, vedere la sezione Eliminazione dei componenti con IDisposable.For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.

### <a name="suppress-ui-refreshing"></a>Eliminazione dell'aggiornamento dell'interfaccia utente

Eseguire <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> l'override per eliminare l'aggiornamento dell'interfaccia utente. Se l'implementazione restituisce `true`, l'interfaccia utente viene aggiornata:

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

`ShouldRender`viene chiamato ogni volta che viene eseguito il rendering del componente.

Anche `ShouldRender` se viene sottoposto a override, il componente viene sempre eseguito il rendering inizialmente.

## <a name="state-changes"></a>Modifiche stato

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>notifica al componente che il relativo stato è stato modificato. Se applicabile, `StateHasChanged` la chiamata determina il rendering del componente.

## <a name="handle-incomplete-async-actions-at-render"></a>Gestire le azioni asincrone incomplete al renderingHandle incomplete async actions at render

Le azioni asincrone eseguite negli eventi del ciclo di vita potrebbero non essere state completate prima del rendering del componente. Gli oggetti `null` potrebbero essere o essere incompleti con i dati durante l'esecuzione del metodo del ciclo di vita. Fornire la logica di rendering per verificare che gli oggetti vengano inizializzati. Esegue il rendering di elementi dell'interfaccia utente `null`segnaposto (ad esempio, un messaggio di caricamento) mentre gli oggetti sono .

Nel `FetchData` componente dei Blazor modelli, `OnInitializedAsync` viene eseguito l'override per ricevere`forecasts`in modo asincrono i dati di previsione ( ). Quando `forecasts` `null`è , viene visualizzato un messaggio di caricamento all'utente. Dopo `Task` il completamento restituito, `OnInitializedAsync` il componente viene nuovamente eseguito il rendering con lo stato aggiornato.

*Pages/FetchData.razor* nel Blazor modello Server:

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>Smaltimento dei componenti con IDisposable

Se un <xref:System.IDisposable>componente implementa , il [metodo Dispose](/dotnet/standard/garbage-collection/implementing-dispose) viene chiamato quando il componente viene rimosso dall'interfaccia utente. Il seguente `@implements IDisposable` componente `Dispose` utilizza e il metodo:

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
> La <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> `Dispose` chiamata non è supportata. `StateHasChanged`potrebbe essere richiamato come parte dell'abbattimento del renderer, pertanto la richiesta di aggiornamenti dell'interfaccia utente a quel punto non è supportata.

Annullare la sottoscrizione di gestori eventi da eventi .NET. Negli esempi di [ Blazor form](xref:blazor/forms-validation) seguenti viene illustrato come eseguire l'unhook di un gestore eventi nel metodo:The following form examples show how to unhook an event handler in the `Dispose` method:

* Approccio di base e lambda privato

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* Approccio al metodo privato

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a>Gestire gli errori

Per informazioni sulla gestione degli errori <xref:blazor/handle-errors#lifecycle-methods>durante l'esecuzione del metodo del ciclo di vita, vedere .

## <a name="stateful-reconnection-after-prerendering"></a>Riconnessione con stato dopo il prerendering

In Blazor un'app `RenderMode` `ServerPrerendered`Server quando è , il componente viene inizialmente sottoposto a rendering in modo statico come parte della pagina. Una volta stabilita una connessione al server, il rendering del componente viene *eseguito nuovamente*e il componente è interattivo. Se per l'inizializzazione del componente è presente il metodo del ciclo di vita [OnInitialized,Async](xref:blazor/lifecycle#component-initialization-methods) per l'inizializzazione del componente, il metodo viene eseguito *due volte:*

* Quando viene eseguito il prerendering statico del componente.
* Una volta stabilita la connessione al server.

Ciò può comportare una modifica notevole nei dati visualizzati nell'interfaccia utente quando il componente viene infine eseguito il rendering.

Per evitare lo scenario Blazor a doppio rendering in un'app Server:

* Passare un identificatore che può essere usato per memorizzare nella cache lo stato durante il prerendering e recuperare lo stato dopo il riavvio dell'app.
* Utilizzare l'identificatore durante il prerendering per salvare lo stato del componente.
* Utilizzare l'identificatore dopo il prerendering per recuperare lo stato memorizzato nella cache.

Il codice seguente `WeatherForecastService` illustra un aggiornamento Blazor in un'app Server basata su modelli che evita il doppio rendering:The following code demonstrates an updated in a template-based Server app that avoids the double rendering:

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

Per ulteriori informazioni `RenderMode`su <xref:blazor/hosting-model-configuration#render-mode>, vedere .

## <a name="detect-when-the-app-is-prerendering"></a>Rilevare il prerendering dell'app

[!INCLUDE[](~/includes/blazor-prerendering.md)]
