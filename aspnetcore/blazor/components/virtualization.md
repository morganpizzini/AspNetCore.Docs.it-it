---
title: BlazorVirtualizzazione componenti ASP.NET Core
author: guardrex
description: Informazioni su come usare la virtualizzazione di componenti nelle Blazor app ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
no-loc:
- appsettings.json
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
ms.openlocfilehash: 706564bb8607d0bb25c092c31a72e5790c825ee4
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024678"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="09eea-103">BlazorVirtualizzazione componenti ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="09eea-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="09eea-104">Di [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="09eea-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="09eea-105">Migliorare le prestazioni percepite del rendering dei componenti utilizzando il Blazor supporto della virtualizzazione incorporato del Framework.</span><span class="sxs-lookup"><span data-stu-id="09eea-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="09eea-106">La virtualizzazione è una tecnica che consente di limitare il rendering dell'interfaccia utente solo alle parti attualmente visibili.</span><span class="sxs-lookup"><span data-stu-id="09eea-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="09eea-107">La virtualizzazione, ad esempio, è utile quando l'applicazione deve eseguire il rendering di un lungo elenco di elementi ed è necessario che sia visibile solo un subset di elementi in un determinato momento.</span><span class="sxs-lookup"><span data-stu-id="09eea-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="09eea-108">Blazorfornisce il [ `Virtualize` componente](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) che può essere usato per aggiungere la virtualizzazione ai componenti di un'app.</span><span class="sxs-lookup"><span data-stu-id="09eea-108">Blazor provides the [`Virtualize` component](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) that can be used to add virtualization to an app's components.</span></span>

<span data-ttu-id="09eea-109">Il `Virtualize` componente può essere usato nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="09eea-109">The `Virtualize` component can be used when:</span></span>

* <span data-ttu-id="09eea-110">Rendering di un set di elementi di dati in un ciclo.</span><span class="sxs-lookup"><span data-stu-id="09eea-110">Rendering a set of data items in a loop.</span></span>
* <span data-ttu-id="09eea-111">La maggior parte degli elementi non è visibile a causa dello scorrimento.</span><span class="sxs-lookup"><span data-stu-id="09eea-111">Most of the items aren't visible due to scrolling.</span></span>
* <span data-ttu-id="09eea-112">Gli elementi di cui è stato eseguito il rendering hanno esattamente le stesse dimensioni.</span><span class="sxs-lookup"><span data-stu-id="09eea-112">The rendered items are exactly the same size.</span></span> <span data-ttu-id="09eea-113">Quando l'utente scorre fino a un punto arbitrario, il componente può calcolare gli elementi visibili da visualizzare.</span><span class="sxs-lookup"><span data-stu-id="09eea-113">When the user scrolls to an arbitrary point, the component can calculate the visible items to show.</span></span>

<span data-ttu-id="09eea-114">Senza virtualizzazione, un elenco tipico potrebbe usare un [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) ciclo C# per eseguire il rendering di ogni elemento nell'elenco:</span><span class="sxs-lookup"><span data-stu-id="09eea-114">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    }
</div>
```

<span data-ttu-id="09eea-115">Se l'elenco contiene migliaia di elementi, il rendering dell'elenco potrebbe richiedere molto tempo.</span><span class="sxs-lookup"><span data-stu-id="09eea-115">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="09eea-116">L'utente può riscontrare un ritardo dell'interfaccia utente evidente.</span><span class="sxs-lookup"><span data-stu-id="09eea-116">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="09eea-117">Anziché eseguire il rendering di ogni elemento nell'elenco in una sola volta, sostituire il [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) ciclo con il `Virtualize` componente e specificare un'origine di elemento fisso con <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="09eea-117">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="09eea-118">Vengono visualizzati solo gli elementi attualmente visibili:</span><span class="sxs-lookup"><span data-stu-id="09eea-118">Only the items that are currently visible are rendered:</span></span>

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

<span data-ttu-id="09eea-119">Se non si specifica un contesto per il componente con `Context` , usare il `context` valore ( `context.{PROPERTY}` / `@context.{PROPERTY}` ) nel modello di contenuto dell'elemento:</span><span class="sxs-lookup"><span data-stu-id="09eea-119">If not specifying a context to the component with `Context`, use the `context` value (`context.{PROPERTY}`/`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

> [!NOTE]
> <span data-ttu-id="09eea-120">Il processo di mapping degli oggetti modello a elementi e componenti può essere controllato con l' `@key` attributo della direttiva [] [xrif: MVC/views/Razor # Key].</span><span class="sxs-lookup"><span data-stu-id="09eea-120">The mapping process of model objects to elements and components can be controlled with the [`@key`][xref:mvc/views/razor#key] directive attribute.</span></span> <span data-ttu-id="09eea-121">`@key` fa in modo che l'algoritmo diffe garantisca la conservazione di elementi o componenti in base al valore della chiave.</span><span class="sxs-lookup"><span data-stu-id="09eea-121">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span>
>
> <span data-ttu-id="09eea-122">Per altre informazioni, vedere gli articoli seguenti:</span><span class="sxs-lookup"><span data-stu-id="09eea-122">For more information, see the following articles:</span></span>
>
> <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>
> <xref:mvc/views/razor#key>

<span data-ttu-id="09eea-123">Il `Virtualize` componente:</span><span class="sxs-lookup"><span data-stu-id="09eea-123">The `Virtualize` component:</span></span>

* <span data-ttu-id="09eea-124">Calcola il numero di elementi di cui eseguire il rendering in base all'altezza del contenitore e alle dimensioni degli elementi di cui è stato eseguito il rendering.</span><span class="sxs-lookup"><span data-stu-id="09eea-124">Calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>
* <span data-ttu-id="09eea-125">Ricalcola ed esegue nuovamente il rendering degli elementi quando l'utente scorre.</span><span class="sxs-lookup"><span data-stu-id="09eea-125">Recalculates and rerenders items as the user scrolls.</span></span>
* <span data-ttu-id="09eea-126">Recupera solo la sezione di record da un'API esterna che corrisponde all'area visibile corrente, anziché scaricare tutti i dati dalla raccolta.</span><span class="sxs-lookup"><span data-stu-id="09eea-126">Only fetches the slice of records from an external API that correspond to the current visible region, instead of downloading all of the data from the collection.</span></span>

<span data-ttu-id="09eea-127">Il contenuto dell'elemento per il `Virtualize` componente può includere:</span><span class="sxs-lookup"><span data-stu-id="09eea-127">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="09eea-128">HTML semplice e Razor codice, come illustrato nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="09eea-128">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="09eea-129">Uno o più Razor componenti.</span><span class="sxs-lookup"><span data-stu-id="09eea-129">One or more Razor components.</span></span>
* <span data-ttu-id="09eea-130">Combinazione di componenti HTML/ Razor e Razor .</span><span class="sxs-lookup"><span data-stu-id="09eea-130">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="09eea-131">Delegato del provider di elementi</span><span class="sxs-lookup"><span data-stu-id="09eea-131">Item provider delegate</span></span>

<span data-ttu-id="09eea-132">Se non si desidera caricare tutti gli elementi in memoria, è possibile specificare un metodo delegato del provider di elementi per il parametro del componente <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> che recupera in modo asincrono gli elementi richiesti su richiesta:</span><span class="sxs-lookup"><span data-stu-id="09eea-132">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="09eea-133">Il provider Items riceve un oggetto <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest> che specifica il numero di elementi richiesto a partire da un indice iniziale specifico.</span><span class="sxs-lookup"><span data-stu-id="09eea-133">The items provider receives an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="09eea-134">Il provider Items recupera quindi gli elementi richiesti da un database o da un altro servizio e li restituisce come <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> insieme al numero totale di elementi.</span><span class="sxs-lookup"><span data-stu-id="09eea-134">The items provider then retrieves the requested items from a database or other service and returns them as an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> along with a count of the total items.</span></span> <span data-ttu-id="09eea-135">Il provider di elementi può scegliere di recuperare gli elementi con ogni richiesta o di memorizzarli nella cache in modo che siano immediatamente disponibili.</span><span class="sxs-lookup"><span data-stu-id="09eea-135">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span>

<span data-ttu-id="09eea-136">Un `Virtualize` componente può accettare solo **un'origine elemento** dai relativi parametri, quindi non tentare di usare contemporaneamente un provider di elementi e assegnare una raccolta a `Items` .</span><span class="sxs-lookup"><span data-stu-id="09eea-136">A `Virtualize` component can only accept **one item source** from its parameters, so don't attempt to simultaneously use an items provider and assign a collection to `Items`.</span></span> <span data-ttu-id="09eea-137">Se entrambi sono assegnati, <xref:System.InvalidOperationException> viene generata un'eccezione quando i parametri del componente vengono impostati in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="09eea-137">If both are assigned, an <xref:System.InvalidOperationException> is thrown when the component's parameters are set at runtime.</span></span>

<span data-ttu-id="09eea-138">Nell'esempio seguente vengono caricati i dipendenti da un `EmployeeService` :</span><span class="sxs-lookup"><span data-stu-id="09eea-138">The following example loads employees from an `EmployeeService`:</span></span>

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

<span data-ttu-id="09eea-139"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> indica al componente di ririchiedere i dati dal relativo oggetto <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A> .</span><span class="sxs-lookup"><span data-stu-id="09eea-139"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> instructs the component to rerequest data from its <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A>.</span></span> <span data-ttu-id="09eea-140">Questa operazione è utile quando i dati esterni cambiano.</span><span class="sxs-lookup"><span data-stu-id="09eea-140">This is useful when external data changes.</span></span> <span data-ttu-id="09eea-141">Non è necessario chiamare questo quando si usa <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A> .</span><span class="sxs-lookup"><span data-stu-id="09eea-141">There's no need to call this when using <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A>.</span></span>

## <a name="placeholder"></a><span data-ttu-id="09eea-142">Segnaposto</span><span class="sxs-lookup"><span data-stu-id="09eea-142">Placeholder</span></span>

<span data-ttu-id="09eea-143">Poiché la richiesta di elementi da un'origine dati remota potrebbe richiedere del tempo, è possibile eseguire il rendering di un segnaposto con contenuto dell'elemento:</span><span class="sxs-lookup"><span data-stu-id="09eea-143">Because requesting items from a remote data source might take some time, you have the option to render a placeholder  with item content:</span></span>

* <span data-ttu-id="09eea-144">Usare <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> ( `<Placeholder>...</Placeholder>` ) per visualizzare il contenuto fino a quando i dati dell'elemento non sono disponibili.</span><span class="sxs-lookup"><span data-stu-id="09eea-144">Use a <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) to display content until the item data is available.</span></span>
* <span data-ttu-id="09eea-145">Utilizzare <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> per impostare il modello di elemento per l'elenco.</span><span class="sxs-lookup"><span data-stu-id="09eea-145">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> to set the item template for the list.</span></span>

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

## <a name="item-size"></a><span data-ttu-id="09eea-146">Dimensioni dell'elemento</span><span class="sxs-lookup"><span data-stu-id="09eea-146">Item size</span></span>

<span data-ttu-id="09eea-147">È possibile impostare le dimensioni di ogni elemento in pixel con <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (impostazione predefinita: 50px):</span><span class="sxs-lookup"><span data-stu-id="09eea-147">The size of each item in pixels can be set with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (default: 50px):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="09eea-148">Conteggio overscan</span><span class="sxs-lookup"><span data-stu-id="09eea-148">Overscan count</span></span>

<span data-ttu-id="09eea-149"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> determina il numero di elementi aggiuntivi che vengono sottoposti a rendering prima e dopo l'area visibile.</span><span class="sxs-lookup"><span data-stu-id="09eea-149"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="09eea-150">Questa impostazione consente di ridurre la frequenza di rendering durante lo scorrimento.</span><span class="sxs-lookup"><span data-stu-id="09eea-150">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="09eea-151">Tuttavia, i valori più elevati comportano il rendering di più elementi nella pagina (impostazione predefinita: 3):</span><span class="sxs-lookup"><span data-stu-id="09eea-151">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="09eea-152">Modifiche stato</span><span class="sxs-lookup"><span data-stu-id="09eea-152">State changes</span></span>

<span data-ttu-id="09eea-153">Quando si apportano modifiche agli elementi di cui è stato eseguito il rendering dal `Virtualize` componente, chiamare <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> per forzare nuovamente la valutazione e il rendering del componente.</span><span class="sxs-lookup"><span data-stu-id="09eea-153">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
