---
title: BlazorVirtualizzazione componenti ASP.NET Core
author: guardrex
description: Informazioni su come usare la virtualizzazione di componenti nelle Blazor app ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
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
uid: blazor/components/virtualization
ms.openlocfilehash: 2ba698eaba23fd67617375e5186d40d45d9772fd
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653880"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a>BlazorVirtualizzazione componenti ASP.NET Core

Di [Daniel Roth](https://github.com/danroth27)

Migliorare le prestazioni percepite del rendering dei componenti utilizzando il Blazor supporto della virtualizzazione incorporato del Framework. La virtualizzazione è una tecnica che consente di limitare il rendering dell'interfaccia utente solo alle parti attualmente visibili. La virtualizzazione, ad esempio, è utile quando l'applicazione deve eseguire il rendering di un lungo elenco di elementi ed è necessario che sia visibile solo un subset di elementi in un determinato momento. Blazor fornisce il `Virtualize` componente che può essere usato per aggiungere la virtualizzazione ai componenti di un'app.

::: moniker range=">= aspnetcore-5.0"

Senza virtualizzazione, un elenco tipico potrebbe usare un [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) ciclo C# per eseguire il rendering di ogni elemento nell'elenco:

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

Se l'elenco contiene migliaia di elementi, il rendering dell'elenco potrebbe richiedere molto tempo. L'utente può riscontrare un ritardo dell'interfaccia utente evidente.

Anziché eseguire il rendering di ogni elemento nell'elenco in una sola volta, sostituire il [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) ciclo con il `Virtualize` componente e specificare un'origine di elemento fisso con `Items` . Vengono visualizzati solo gli elementi attualmente visibili:

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

Se non si specifica un contesto per il componente con `Context` , usare il `context` valore ( `@context.{PROPERTY}` ) nel modello di contenuto dell'elemento:

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

Il `Virtualize` componente calcola il numero di elementi di cui eseguire il rendering in base all'altezza del contenitore e alle dimensioni degli elementi di cui è stato eseguito il rendering.

Il contenuto dell'elemento per il `Virtualize` componente può includere:

* HTML semplice e Razor codice, come illustrato nell'esempio precedente.
* Uno o più Razor componenti.
* Combinazione di componenti HTML/ Razor e Razor .

## <a name="item-provider-delegate"></a>Delegato del provider di elementi

Se non si desidera caricare tutti gli elementi in memoria, è possibile specificare un metodo delegato del provider di elementi per il parametro del componente `ItemsProvider` che recupera in modo asincrono gli elementi richiesti su richiesta:

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

Il provider Items riceve un oggetto `ItemsProviderRequest` che specifica il numero di elementi richiesto a partire da un indice iniziale specifico. Il provider Items recupera quindi gli elementi richiesti da un database o da un altro servizio e li restituisce come `ItemsProviderResult<TItem>` insieme al numero totale di elementi. Il provider di elementi può scegliere di recuperare gli elementi con ogni richiesta o di memorizzarli nella cache in modo che siano immediatamente disponibili. Non tentare di usare un provider di elementi e assegnare una raccolta a `Items` per lo stesso `Virtualize` componente.

Nell'esempio seguente vengono caricati i dipendenti da un `EmployeeService` :

```csharp
private async ValueTask<ItemsProviderResult<Employee>> LoadEmployees(
    ItemsProviderRequest request)
{
    var numEmployees = Math.Min(request.Count, totalEmployees - request.StartIndex);
    var employees = await EmployeesService.GetEmployeesAsync(request.StartIndex, 
        numEmployees, request.CancellationToken);

    return new ItemsProviderResult<Employee>(employees, totalEmployees);
}
```

## <a name="placeholder"></a>Segnaposto

Poiché la richiesta di elementi da un'origine dati remota potrebbe richiedere del tempo, è possibile eseguire il rendering di un segnaposto ( `<Placeholder>...</Placeholder>` ) finché non sono disponibili i dati dell'elemento:

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <ItemContent>
        <p>
            @employee.FirstName @employee.LastName has the 
            job title of @employee.JobTitle.
        </p>
    </ItemContent>
    <Placeholder>
        <p>
            Loading&hellip;
        </p>
    </Placeholder>
</Virtualize>
```

## <a name="item-size"></a>Dimensioni dell'elemento

È possibile impostare le dimensioni di ogni elemento in pixel con `ItemSize` (impostazione predefinita: 50px):

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a>Conteggio overscan

`OverscanCount` determina il numero di elementi aggiuntivi che vengono sottoposti a rendering prima e dopo l'area visibile. Questa impostazione consente di ridurre la frequenza di rendering durante lo scorrimento. Tuttavia, i valori più elevati comportano il rendering di più elementi nella pagina (impostazione predefinita: 3):

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Ad esempio, una griglia o un elenco che esegue il rendering di centinaia di righe contenenti componenti comporta un utilizzo intensivo del processore per il rendering. Si consiglia di virtualizzare un layout di griglia o elenco in modo da eseguire il rendering solo di un subset dei componenti in un determinato momento.

Il `Virtualize` componente seguente ( `Virtualize.cs` ) implementa <xref:Microsoft.AspNetCore.Components.ComponentBase> per eseguire il rendering del contenuto figlio in base allo scorrimento utente:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Rendering;
using Microsoft.JSInterop;

public class Virtualize<TItem> : ComponentBase
{
    [Parameter]
    public string TagName { get; set; } = "div";

    [Parameter]
    public RenderFragment<TItem> ChildContent { get; set; }

    [Parameter]
    public ICollection<TItem> Items { get; set; }

    [Parameter]
    public double ItemHeight { get; set; }

    [Parameter(CaptureUnmatchedValues = true)] 
    public Dictionary<string, object> Attributes { get; set; }

    [Inject]
    IJSRuntime JS { get; set; }

    ElementReference contentElement;
    int numItemsToSkipBefore;
    int numItemsToShow;

    protected override void BuildRenderTree(RenderTreeBuilder builder)
    {
        // Render actual content
        builder.OpenElement(0, TagName);
        builder.AddMultipleAttributes(1, Attributes);

        var translateY = numItemsToSkipBefore * ItemHeight;
        builder.AddAttribute(2, "style", $"transform: translateY({ translateY }px);");
        builder.AddAttribute(2, "data-translateY", translateY);
        builder.AddElementReferenceCapture(3, @ref => { contentElement = @ref; });

        // As an important optimization, *don't* use builder.AddContent(seq, ChildContent, item)
        // because that implicitly wraps a new region around each item, which in turn means that 
        // @key does nothing (because keys are scoped to regions). Instead, create a single 
        // container region and then invoke the fragments directly.

        builder.OpenRegion(4);

        foreach (var item in Items.Skip(numItemsToSkipBefore).Take(numItemsToShow))
        {
            ChildContent(item)(builder);
        }

        builder.CloseRegion();

        builder.CloseElement();

        // Also emit a spacer that causes the total vertical height to add up to 
        // Items.Count()*numItems

        builder.OpenElement(5, "div");
        var numHiddenItems = Items.Count - numItemsToShow;
        builder.AddAttribute(6, "style", 
            $"width: 1px; height: { numHiddenItems * ItemHeight }px;");
        builder.CloseElement();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            var objectRef = DotNetObjectReference.Create(this);
            var initResult = await JS.InvokeAsync<ScrollEventArgs>(
                "VirtualizedComponent._initialize", objectRef, contentElement);
            OnScroll(initResult);
        }
    }

    [JSInvokable]
    public void OnScroll(ScrollEventArgs args)
    {
        var relativeTop = args.ContainerRect.Top - args.ContentRect.Top;
        numItemsToSkipBefore = Math.Max(0, (int)(relativeTop / ItemHeight));

        var visibleHeight = args.ContainerRect.Bottom - 
            (args.ContentRect.Top + numItemsToSkipBefore * ItemHeight);
        numItemsToShow = (int)Math.Ceiling(visibleHeight / ItemHeight) + 3;

        StateHasChanged();
    }

    public class ScrollEventArgs
    {
        public DOMRect ContainerRect { get; set; }
        public DOMRect ContentRect { get; set; }
    }

    public class DOMRect
    {
        public double Top { get; set; }
        public double Bottom { get; set; }
        public double Left { get; set; } 
        public double Right { get; set; }
        public double Width { get; set; }
        public double Height { get; set; }
    }
}
```

Il componente seguente `FetchData` ( `FetchData.razor` ) usa il `Virtualize` componente precedente per visualizzare 25 righe di dati meteorologici alla volta:

```razor
@page "/"
@page "/fetchdata"
@inject HttpClient Http

<h1>Weather forecast</h1>

<p>This component demonstrates fetching data from a service.</p>

@if (forecasts == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <h2>Using <code>table-layout: fixed</code></h2>
    <div style="height:300px; overflow-y: auto;">
        <Virtualize ItemHeight="25" Items="@forecasts">
            <tr @key="@context.GetHashCode()" 
                    style="display: table; table-layout: fixed; width: 100%;">
                <td>@context.Date.ToShortDateString()</td>
                <td>@context.TemperatureC</td>
                <td>@context.TemperatureF</td>
                <td>@context.Summary</td>
            </tr>
        </Virtualize>
    </div>

    <h2>Using <code>position: sticky</code></h2>
    <div style="height: 300px; overflow-y: auto; position: relative;">
        <table>
            <thead class="sticky">
                <tr>
                    <th>Date</th>
                    <th>Temperature (C)</th>
                    <th>Temperature (F)</th>
                    <th>Summary</th>
                </tr>
            </thead>

            <Virtualize TagName="tbody" ItemHeight="25" Items="@forecasts">
                <tr @key="@context.GetHashCode()">
                    <td>@context.Date.ToShortDateString()</td>
                    <td>@context.TemperatureC</td>
                    <td>@context.TemperatureF</td>
                    <td>@context.Summary</td>
                </tr>
            </Virtualize>
        </table>
    </div>

    <style type="text/css">
        thead.sticky th {
            position: sticky;
            top: 0;
        }
        tr td {
            height: 25px;
        }
    </style>
}

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "sample-data/weather.json");
    }

    public class WeatherForecast
    {
        public DateTime Date { get; set; }

        public int TemperatureC { get; set; }

        public string Summary { get; set; }

        public int TemperatureF => 32 + (int)(TemperatureC / 0.5556);
    }
}
```

Nell'esempio precedente, l'approccio consiste nel evitare il posizionamento assoluto per ogni singolo elemento. Il posizionamento assoluto presenta alcuni vantaggi (si può essere certi che gli elementi occupino la quantità specificata di spazio Y), ma presenta uno svantaggio negativo che consente di perdere le larghezze normali e non è possibile recuperare le colonne della tabella per allinearle tra righe o intestazioni quando si basano sul dimensionamento del contenuto.

Il concetto alla base della progettazione del `Virtualize` componente è che il componente non modifica la struttura degli elementi all'interno del Dom. Non sono presenti elementi wrapper aggiuntivi, oltre a quello di cui `TagName` è stato specificato.

L'approccio migliore consiste nell'evitare anche l' `TagName` elemento wrapper. Il `Virtualize` componente non emette alcun elemento. Viene eseguito il rendering di tutti i componenti, ma molte delle istanze del modello sono necessarie per riempire lo spazio visibile rimanente nel predecessore scorrevole più vicino, oltre ad aggiungere un elemento spaziatore seguente che rende il predecessore scorrevole con l'intervallo di scorrimento destro. Per quanto concerne il layout, è come se l'intera gamma di elementi figlio fosse fisicamente nel DOM. Tuttavia, è necessario specificare un oggetto preciso `ItemHeight` . Se si verificano problemi, ad esempio perché si è confusi e si ritiene che sia valido specificare `style.height` su un oggetto `<tr>` , il componente termina il rendering del numero errato di istanze del modello e non riempie l'interfaccia utente o ne esegue il rendering in modo non efficiente. Inoltre, l'intervallo di scorrimento nell'elemento padre non è corretto.

::: moniker-end

## <a name="state-changes"></a>Modifiche stato

Quando si apportano modifiche agli elementi di cui è stato eseguito il rendering dal `Virtualize` componente, chiamare <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> per forzare nuovamente la valutazione e il rendering del componente.
