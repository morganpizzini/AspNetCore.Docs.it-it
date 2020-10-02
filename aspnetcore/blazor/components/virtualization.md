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
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="32d4e-103">BlazorVirtualizzazione componenti ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="32d4e-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="32d4e-104">Di [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="32d4e-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="32d4e-105">Migliorare le prestazioni percepite del rendering dei componenti utilizzando il Blazor supporto della virtualizzazione incorporato del Framework.</span><span class="sxs-lookup"><span data-stu-id="32d4e-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="32d4e-106">La virtualizzazione è una tecnica che consente di limitare il rendering dell'interfaccia utente solo alle parti attualmente visibili.</span><span class="sxs-lookup"><span data-stu-id="32d4e-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="32d4e-107">La virtualizzazione, ad esempio, è utile quando l'applicazione deve eseguire il rendering di un lungo elenco di elementi ed è necessario che sia visibile solo un subset di elementi in un determinato momento.</span><span class="sxs-lookup"><span data-stu-id="32d4e-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="32d4e-108">Blazor fornisce il `Virtualize` componente che può essere usato per aggiungere la virtualizzazione ai componenti di un'app.</span><span class="sxs-lookup"><span data-stu-id="32d4e-108">Blazor provides the `Virtualize` component that can be used to add virtualization to an app's components.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="32d4e-109">Senza virtualizzazione, un elenco tipico potrebbe usare un [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) ciclo C# per eseguire il rendering di ogni elemento nell'elenco:</span><span class="sxs-lookup"><span data-stu-id="32d4e-109">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

<span data-ttu-id="32d4e-110">Se l'elenco contiene migliaia di elementi, il rendering dell'elenco potrebbe richiedere molto tempo.</span><span class="sxs-lookup"><span data-stu-id="32d4e-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="32d4e-111">L'utente può riscontrare un ritardo dell'interfaccia utente evidente.</span><span class="sxs-lookup"><span data-stu-id="32d4e-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="32d4e-112">Anziché eseguire il rendering di ogni elemento nell'elenco in una sola volta, sostituire il [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) ciclo con il `Virtualize` componente e specificare un'origine di elemento fisso con `Items` .</span><span class="sxs-lookup"><span data-stu-id="32d4e-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with `Items`.</span></span> <span data-ttu-id="32d4e-113">Vengono visualizzati solo gli elementi attualmente visibili:</span><span class="sxs-lookup"><span data-stu-id="32d4e-113">Only the items that are currently visible are rendered:</span></span>

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="32d4e-114">Se non si specifica un contesto per il componente con `Context` , usare il `context` valore ( `@context.{PROPERTY}` ) nel modello di contenuto dell'elemento:</span><span class="sxs-lookup"><span data-stu-id="32d4e-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="32d4e-115">Il `Virtualize` componente calcola il numero di elementi di cui eseguire il rendering in base all'altezza del contenitore e alle dimensioni degli elementi di cui è stato eseguito il rendering.</span><span class="sxs-lookup"><span data-stu-id="32d4e-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

<span data-ttu-id="32d4e-116">Il contenuto dell'elemento per il `Virtualize` componente può includere:</span><span class="sxs-lookup"><span data-stu-id="32d4e-116">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="32d4e-117">HTML semplice e Razor codice, come illustrato nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="32d4e-117">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="32d4e-118">Uno o più Razor componenti.</span><span class="sxs-lookup"><span data-stu-id="32d4e-118">One or more Razor components.</span></span>
* <span data-ttu-id="32d4e-119">Combinazione di componenti HTML/ Razor e Razor .</span><span class="sxs-lookup"><span data-stu-id="32d4e-119">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="32d4e-120">Delegato del provider di elementi</span><span class="sxs-lookup"><span data-stu-id="32d4e-120">Item provider delegate</span></span>

<span data-ttu-id="32d4e-121">Se non si desidera caricare tutti gli elementi in memoria, è possibile specificare un metodo delegato del provider di elementi per il parametro del componente `ItemsProvider` che recupera in modo asincrono gli elementi richiesti su richiesta:</span><span class="sxs-lookup"><span data-stu-id="32d4e-121">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's `ItemsProvider` parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="32d4e-122">Il provider Items riceve un oggetto `ItemsProviderRequest` che specifica il numero di elementi richiesto a partire da un indice iniziale specifico.</span><span class="sxs-lookup"><span data-stu-id="32d4e-122">The items provider receives an `ItemsProviderRequest`, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="32d4e-123">Il provider Items recupera quindi gli elementi richiesti da un database o da un altro servizio e li restituisce come `ItemsProviderResult<TItem>` insieme al numero totale di elementi.</span><span class="sxs-lookup"><span data-stu-id="32d4e-123">The items provider then retrieves the requested items from a database or other service and returns them as an `ItemsProviderResult<TItem>` along with a count of the total items.</span></span> <span data-ttu-id="32d4e-124">Il provider di elementi può scegliere di recuperare gli elementi con ogni richiesta o di memorizzarli nella cache in modo che siano immediatamente disponibili.</span><span class="sxs-lookup"><span data-stu-id="32d4e-124">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span> <span data-ttu-id="32d4e-125">Non tentare di usare un provider di elementi e assegnare una raccolta a `Items` per lo stesso `Virtualize` componente.</span><span class="sxs-lookup"><span data-stu-id="32d4e-125">Don't attempt to use an items provider and assign a collection to `Items` for the same `Virtualize` component.</span></span>

<span data-ttu-id="32d4e-126">Nell'esempio seguente vengono caricati i dipendenti da un `EmployeeService` :</span><span class="sxs-lookup"><span data-stu-id="32d4e-126">The following example loads employees from an `EmployeeService`:</span></span>

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

## <a name="placeholder"></a><span data-ttu-id="32d4e-127">Segnaposto</span><span class="sxs-lookup"><span data-stu-id="32d4e-127">Placeholder</span></span>

<span data-ttu-id="32d4e-128">Poiché la richiesta di elementi da un'origine dati remota potrebbe richiedere del tempo, è possibile eseguire il rendering di un segnaposto ( `<Placeholder>...</Placeholder>` ) finché non sono disponibili i dati dell'elemento:</span><span class="sxs-lookup"><span data-stu-id="32d4e-128">Because requesting items from a remote data source might take some time, you have the option to render a placeholder (`<Placeholder>...</Placeholder>`) until the item data is available:</span></span>

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

## <a name="item-size"></a><span data-ttu-id="32d4e-129">Dimensioni dell'elemento</span><span class="sxs-lookup"><span data-stu-id="32d4e-129">Item size</span></span>

<span data-ttu-id="32d4e-130">È possibile impostare le dimensioni di ogni elemento in pixel con `ItemSize` (impostazione predefinita: 50px):</span><span class="sxs-lookup"><span data-stu-id="32d4e-130">The size of each item in pixels can be set with `ItemSize` (default: 50px):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="32d4e-131">Conteggio overscan</span><span class="sxs-lookup"><span data-stu-id="32d4e-131">Overscan count</span></span>

<span data-ttu-id="32d4e-132">`OverscanCount` determina il numero di elementi aggiuntivi che vengono sottoposti a rendering prima e dopo l'area visibile.</span><span class="sxs-lookup"><span data-stu-id="32d4e-132">`OverscanCount` determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="32d4e-133">Questa impostazione consente di ridurre la frequenza di rendering durante lo scorrimento.</span><span class="sxs-lookup"><span data-stu-id="32d4e-133">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="32d4e-134">Tuttavia, i valori più elevati comportano il rendering di più elementi nella pagina (impostazione predefinita: 3):</span><span class="sxs-lookup"><span data-stu-id="32d4e-134">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="32d4e-135">Ad esempio, una griglia o un elenco che esegue il rendering di centinaia di righe contenenti componenti comporta un utilizzo intensivo del processore per il rendering.</span><span class="sxs-lookup"><span data-stu-id="32d4e-135">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="32d4e-136">Si consiglia di virtualizzare un layout di griglia o elenco in modo da eseguire il rendering solo di un subset dei componenti in un determinato momento.</span><span class="sxs-lookup"><span data-stu-id="32d4e-136">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span>

<span data-ttu-id="32d4e-137">Il `Virtualize` componente seguente ( `Virtualize.cs` ) implementa <xref:Microsoft.AspNetCore.Components.ComponentBase> per eseguire il rendering del contenuto figlio in base allo scorrimento utente:</span><span class="sxs-lookup"><span data-stu-id="32d4e-137">The following `Virtualize` component (`Virtualize.cs`) implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render child content based on user scrolling:</span></span>

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

<span data-ttu-id="32d4e-138">Il componente seguente `FetchData` ( `FetchData.razor` ) usa il `Virtualize` componente precedente per visualizzare 25 righe di dati meteorologici alla volta:</span><span class="sxs-lookup"><span data-stu-id="32d4e-138">The following `FetchData` component (`FetchData.razor`) uses the preceding `Virtualize` component to display 25 rows of weather data at a time:</span></span>

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

<span data-ttu-id="32d4e-139">Nell'esempio precedente, l'approccio consiste nel evitare il posizionamento assoluto per ogni singolo elemento.</span><span class="sxs-lookup"><span data-stu-id="32d4e-139">In the preceding example, the approach is about avoiding absolute positioning for each individual item.</span></span> <span data-ttu-id="32d4e-140">Il posizionamento assoluto presenta alcuni vantaggi (si può essere certi che gli elementi occupino la quantità specificata di spazio Y), ma presenta uno svantaggio negativo che consente di perdere le larghezze normali e non è possibile recuperare le colonne della tabella per allinearle tra righe o intestazioni quando si basano sul dimensionamento del contenuto.</span><span class="sxs-lookup"><span data-stu-id="32d4e-140">Absolute positioning would have some advantages (we can be sure the items do take up the specified amount of Y space) but has the bad disadvantage that you lose the normal widths and can't get table columns to line up across rows/header when based on content sizing.</span></span>

<span data-ttu-id="32d4e-141">Il concetto alla base della progettazione del `Virtualize` componente è che il componente non modifica la struttura degli elementi all'interno del Dom.</span><span class="sxs-lookup"><span data-stu-id="32d4e-141">The concept behind the design of the `Virtualize` component is that the component doesn't change how the items are laid out within the DOM.</span></span> <span data-ttu-id="32d4e-142">Non sono presenti elementi wrapper aggiuntivi, oltre a quello di cui `TagName` è stato specificato.</span><span class="sxs-lookup"><span data-stu-id="32d4e-142">There are no added wrapper elements, besides the single one whose `TagName` you specify.</span></span>

<span data-ttu-id="32d4e-143">L'approccio migliore consiste nell'evitare anche l' `TagName` elemento wrapper.</span><span class="sxs-lookup"><span data-stu-id="32d4e-143">The best approach is to avoid even the `TagName` wrapper element.</span></span> <span data-ttu-id="32d4e-144">Il `Virtualize` componente non emette alcun elemento.</span><span class="sxs-lookup"><span data-stu-id="32d4e-144">Have the `Virtualize` component emit no elements of its own.</span></span> <span data-ttu-id="32d4e-145">Viene eseguito il rendering di tutti i componenti, ma molte delle istanze del modello sono necessarie per riempire lo spazio visibile rimanente nel predecessore scorrevole più vicino, oltre ad aggiungere un elemento spaziatore seguente che rende il predecessore scorrevole con l'intervallo di scorrimento destro.</span><span class="sxs-lookup"><span data-stu-id="32d4e-145">All the component does is render however many of the template instances are required to fill up any remaining visible space in the closest scrollable ancestor, plus add a following spacer element that makes the scrollable ancestor have the right scrolling range.</span></span> <span data-ttu-id="32d4e-146">Per quanto concerne il layout, è come se l'intera gamma di elementi figlio fosse fisicamente nel DOM.</span><span class="sxs-lookup"><span data-stu-id="32d4e-146">As far as the layout is concerned, it's the same as if the full range of children were physically in the DOM.</span></span> <span data-ttu-id="32d4e-147">Tuttavia, è necessario specificare un oggetto preciso `ItemHeight` .</span><span class="sxs-lookup"><span data-stu-id="32d4e-147">It does require you to specify an accurate `ItemHeight` though.</span></span> <span data-ttu-id="32d4e-148">Se si verificano problemi, ad esempio perché si è confusi e si ritiene che sia valido specificare `style.height` su un oggetto `<tr>` , il componente termina il rendering del numero errato di istanze del modello e non riempie l'interfaccia utente o ne esegue il rendering in modo non efficiente.</span><span class="sxs-lookup"><span data-stu-id="32d4e-148">If you get it wrong (for example, because you're confused and think it's valid to specify `style.height` on a `<tr>`), then the component ends up rendering the wrong number of template instances and either not fill up the UI or inefficiently render too many.</span></span> <span data-ttu-id="32d4e-149">Inoltre, l'intervallo di scorrimento nell'elemento padre non è corretto.</span><span class="sxs-lookup"><span data-stu-id="32d4e-149">Additionally, the scroll range on the parent won't be correct.</span></span>

::: moniker-end

## <a name="state-changes"></a><span data-ttu-id="32d4e-150">Modifiche stato</span><span class="sxs-lookup"><span data-stu-id="32d4e-150">State changes</span></span>

<span data-ttu-id="32d4e-151">Quando si apportano modifiche agli elementi di cui è stato eseguito il rendering dal `Virtualize` componente, chiamare <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> per forzare nuovamente la valutazione e il rendering del componente.</span><span class="sxs-lookup"><span data-stu-id="32d4e-151">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
