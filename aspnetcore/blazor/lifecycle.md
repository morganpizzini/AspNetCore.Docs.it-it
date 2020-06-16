---
title: Ciclo di vita ASP.NET Core Blazor
author: guardrex
description: Informazioni su come usare i Razor metodi del ciclo di vita del componente nelle Blazor app ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/01/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: 3f9feef205e0d28d3160d5e5f6f49390ce5cd0b1
ms.sourcegitcommit: b0062f29cba2e5c21b95cf89eaf435ba830d11a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/15/2020
ms.locfileid: "84776371"
---
# <a name="aspnet-core-blazor-lifecycle"></a>Ciclo di vita ASP.NET Core Blazor

Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

Il Blazor Framework include metodi del ciclo di vita sincroni e asincroni. Eseguire l'override dei metodi Lifecycle per eseguire operazioni aggiuntive sui componenti durante l'inizializzazione e il rendering dei componenti.

## <a name="lifecycle-methods"></a>Metodi del ciclo di vita

### <a name="before-parameters-are-set"></a>Prima dell'impostazione dei parametri

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>Imposta i parametri forniti dall'elemento padre del componente nell'albero di rendering:

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView>contiene l'intero set di valori dei parametri ogni volta che <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> viene chiamato.

L'implementazione predefinita di <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> imposta il valore di ogni proprietà con l' [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attributo o con [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) un valore corrispondente in <xref:Microsoft.AspNetCore.Components.ParameterView> . I parametri che non hanno un valore corrispondente in <xref:Microsoft.AspNetCore.Components.ParameterView> rimangono invariati.

Se [base. SetParametersAync](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) non viene richiamato, il codice personalizzato può interpretare il valore dei parametri in ingresso in qualsiasi modo necessario. Ad esempio, non è necessario assegnare i parametri in ingresso alle proprietà della classe.

Se sono configurati gestori di eventi, rimuoverli a disposizione. Per ulteriori informazioni, vedere la sezione relativa all' [eliminazione dei componenti con IDisposable](#component-disposal-with-idisposable) .

### <a name="component-initialization-methods"></a>Metodi di inizializzazione componenti

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> vengono richiamati quando il componente viene inizializzato dopo aver ricevuto i parametri iniziali dal componente padre in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> . 

Usare <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> quando il componente esegue un'operazione asincrona e deve essere aggiornato al termine dell'operazione.

Per un'operazione sincrona, eseguire l'override di <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> :

```csharp
protected override void OnInitialized()
{
    ...
}
```

Per eseguire un'operazione asincrona, eseguire l'override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> e usare l'operatore [await](/dotnet/csharp/language-reference/operators/await) nell'operazione:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

BlazorApp server che [preeseguono la](xref:blazor/hosting-model-configuration#render-mode) chiamata al contenuto per <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_due volte_**:

* Una volta quando il componente viene inizialmente sottoposto a rendering statico come parte della pagina.
* Una seconda volta quando il browser stabilisce una connessione al server.

Per impedire l'esecuzione di codice dello sviluppatore per <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> due volte, vedere la sezione [riconnessione con stato dopo il rendering](#stateful-reconnection-after-prerendering) .

Mentre un' Blazor app Server è prerendering, alcune azioni, ad esempio la chiamata a JavaScript, non sono possibili perché non è stata stabilita una connessione con il browser. I componenti potrebbero dover eseguire il rendering in modo diverso quando ne viene eseguito il rendering. Per altre informazioni, vedere la sezione [rilevare quando l'app è prerendering](#detect-when-the-app-is-prerendering) .

Se sono configurati gestori di eventi, rimuoverli a disposizione. Per ulteriori informazioni, vedere la sezione relativa all' [eliminazione dei componenti con IDisposable](#component-disposal-with-idisposable) .

### <a name="after-parameters-are-set"></a>Parametri after impostati

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>o <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> sono chiamati:

* Dopo che il componente è stato inizializzato in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> o <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> .
* Quando il componente padre esegue nuovamente il rendering e fornisce:
  * Solo i tipi non modificabili primitivi noti di cui è stato modificato almeno un parametro.
  * Tutti i parametri tipizzati complessi. Il Framework non è in grado di stabilire se i valori di un parametro tipizzato complesso sono stati modificati internamente, quindi considera il set di parametri come modificato.

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> Il lavoro asincrono quando si applicano parametri e valori di proprietà deve verificarsi durante l'evento del ciclo di vita <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> .

```csharp
protected override void OnParametersSet()
{
    ...
}
```

Se sono configurati gestori di eventi, rimuoverli a disposizione. Per ulteriori informazioni, vedere la sezione relativa all' [eliminazione dei componenti con IDisposable](#component-disposal-with-idisposable) .

### <a name="after-component-render"></a>Rendering del componente dopo

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> vengono chiamati dopo che un componente ha terminato il rendering. I riferimenti a elementi e componenti vengono popolati a questo punto. Usare questa fase per eseguire passaggi di inizializzazione aggiuntivi usando il contenuto sottoposto a rendering, ad esempio l'attivazione di librerie JavaScript di terze parti che operano sugli elementi DOM sottoposti a rendering.

Il `firstRender` parametro per <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> :

* Viene impostato `true` sulla prima volta che viene eseguito il rendering dell'istanza del componente.
* Può essere usato per garantire che il lavoro di inizializzazione venga eseguito una sola volta.

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
> Il lavoro asincrono immediatamente dopo il rendering deve verificarsi durante l'evento del ciclo di vita <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> .
>
> Anche se si restituisce un oggetto <xref:System.Threading.Tasks.Task> da <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> , il Framework non pianifica un ulteriore ciclo di rendering per il componente al termine dell'attività. In questo modo è possibile evitare un ciclo di rendering infinito. È diverso dagli altri metodi del ciclo di vita, che pianificano un ulteriore ciclo di rendering dopo il completamento dell'attività restituita.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *non vengono chiamati durante il prerendering sul server.*

Se sono configurati gestori di eventi, rimuoverli a disposizione. Per ulteriori informazioni, vedere la sezione relativa all' [eliminazione dei componenti con IDisposable](#component-disposal-with-idisposable) .

### <a name="suppress-ui-refreshing"></a>Evita aggiornamento dell'interfaccia utente

Eseguire l'override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> per impedire l'aggiornamento dell'interfaccia utente. Se l'implementazione restituisce `true` , l'interfaccia utente viene aggiornata:

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>viene chiamato ogni volta che il componente viene sottoposto a rendering.

Anche se <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> viene sottoposto a override, il componente viene sempre sottoposto a rendering iniziale.

Per altre informazioni, vedere <xref:performance/blazor/webassembly-best-practices#avoid-unnecessary-component-renders>.

## <a name="state-changes"></a>Modifiche stato

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>notifica al componente che lo stato è stato modificato. Se applicabile, <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> la chiamata a comporta il rendering del componente.

## <a name="handle-incomplete-async-actions-at-render"></a>Gestisci azioni asincrone incomplete durante il rendering

Le azioni asincrone eseguite negli eventi del ciclo di vita potrebbero non essere state completate prima del rendering del componente. Gli oggetti possono essere `null` o compilati in modo non completo con i dati mentre è in esecuzione il metodo del ciclo di vita. Fornire la logica di rendering per confermare che gli oggetti vengono inizializzati. Esegue il rendering degli elementi dell'interfaccia utente segnaposto (ad esempio, un messaggio di caricamento) mentre gli oggetti sono `null` .

Nel `FetchData` componente dei Blazor modelli <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> viene eseguito l'override di asincrono Receive forecast data ( `forecasts` ). Quando `forecasts` è `null` , all'utente viene visualizzato un messaggio di caricamento. Quando l'oggetto `Task` restituito da <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> viene completato, viene eseguito il rendering del componente con lo stato aggiornato.

*Pages/fetchData. Razor* nel Blazor modello server:

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>Eliminazione di componenti con IDisposable

Se un componente implementa <xref:System.IDisposable> , il [metodo Dispose](/dotnet/standard/garbage-collection/implementing-dispose) viene chiamato quando il componente viene rimosso dall'interfaccia utente. Il componente seguente utilizza `@implements IDisposable` e il `Dispose` Metodo:

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
> La chiamata <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> di in `Dispose` non è supportata. <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>potrebbe essere richiamato come parte del propagazione del renderer, quindi la richiesta di aggiornamenti dell'interfaccia utente in quel momento non è supportata.

Annulla la sottoscrizione di gestori eventi da eventi .NET. Negli esempi di [ Blazor form](xref:blazor/forms-validation) seguenti viene illustrato come scollegare un gestore eventi nel `Dispose` Metodo:

* Approccio basato su campo privato e lambda

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* Approccio metodo privato

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a>Gestire gli errori

Per informazioni sulla gestione degli errori durante l'esecuzione del metodo del ciclo di vita, vedere <xref:blazor/handle-errors#lifecycle-methods> .

## <a name="stateful-reconnection-after-prerendering"></a>Riconnessione con stato dopo il rendering preliminare

In un' Blazor app Server quando <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> è <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> , il componente viene inizialmente sottoposto a rendering statico come parte della pagina. Quando il browser stabilisce una connessione al server, viene *nuovamente*eseguito il rendering del componente e il componente è ora interattivo. Se è presente il metodo [{Async} Lifecycle OnInitialized](#component-initialization-methods) per l'inizializzazione del componente, il metodo viene eseguito *due volte*:

* Quando il componente viene preeseguito in modo statico.
* Una volta stabilita la connessione al server.

Ciò può comportare una modifica evidente nei dati visualizzati nell'interfaccia utente quando il componente viene sottoposto a rendering.

Per evitare lo scenario di doppio rendering in un' Blazor app Server:

* Passare un identificatore che può essere usato per memorizzare nella cache lo stato durante il prerendering e recuperare lo stato dopo il riavvio dell'app.
* Utilizzare l'identificatore durante il prerendering per salvare lo stato del componente.
* Utilizzare l'identificatore dopo il prerendering per recuperare lo stato memorizzato nella cache.

Il codice seguente illustra un aggiornamento `WeatherForecastService` in un'app server basata su modello Blazor che evita il doppio rendering:

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

Per ulteriori informazioni su <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> , vedere <xref:blazor/hosting-model-configuration#render-mode> .

## <a name="detect-when-the-app-is-prerendering"></a>Rilevare il momento in cui viene eseguito il prerendering dell'app

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="cancelable-background-work"></a>Lavoro in background annullabile

I componenti eseguono spesso attività in background con esecuzione prolungata, ad esempio la creazione di chiamate di rete ( <xref:System.Net.Http.HttpClient> ) e l'interazione con i database. È consigliabile arrestare il lavoro in background per conservare le risorse di sistema in diverse situazioni. Ad esempio, le operazioni asincrone in background non vengono arrestate automaticamente quando un utente si sposta da un componente.

Altri motivi per cui gli elementi di lavoro in background potrebbero richiedere l'annullamento includono:

* Un'attività in background in esecuzione è stata avviata con i parametri di elaborazione o i dati di input difettosi.
* Il set corrente di elementi di lavoro in background in esecuzione deve essere sostituito con un nuovo set di elementi di lavoro.
* È necessario modificare la priorità delle attività attualmente in esecuzione.
* L'app deve essere arrestata per poterla ridistribuire nel server.
* Le risorse del server diventano limitate, rendendo necessaria la ripianificazione degli elementi di lavoro in background.

Per implementare un modello di lavoro in background annullabile in un componente:

* Utilizzare <xref:System.Threading.CancellationTokenSource> e <xref:System.Threading.CancellationToken> .
* In caso di [eliminazione del componente](#component-disposal-with-idisposable) e in qualsiasi momento si desideri annullare l'annullamento del token, chiamare [CancellationTokenSource. Cancel](xref:System.Threading.CancellationTokenSource.Cancel%2A) per segnalare che il lavoro in background deve essere annullato.
* Dopo che la chiamata asincrona restituisce, chiamare <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> sul token.

Nell'esempio seguente:

* `await Task.Delay(5000, cts.Token);`rappresenta il lavoro in background asincrono con esecuzione prolungata.
* `BackgroundResourceMethod`rappresenta un metodo in background con esecuzione prolungata che non deve essere avviato se l'oggetto `Resource` viene eliminato prima della chiamata al metodo.

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
