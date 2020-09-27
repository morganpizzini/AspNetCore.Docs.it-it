---
title: BlazorVirtualizzazione componenti ASP.NET Core
author: guardrex
description: Informazioni su come usare la virtualizzazione di componenti nelle Blazor app ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2020
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
ms.openlocfilehash: 9c3e53bee7535b36bba3474ff50a881568bbd690
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393808"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="ab1e5-103">BlazorVirtualizzazione componenti ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ab1e5-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="ab1e5-104">Di [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ab1e5-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="ab1e5-105">Migliorare le prestazioni percepite del rendering dei componenti utilizzando il Blazor supporto della virtualizzazione incorporato del Framework.</span><span class="sxs-lookup"><span data-stu-id="ab1e5-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="ab1e5-106">La virtualizzazione è una tecnica che consente di limitare il rendering dell'interfaccia utente solo alle parti attualmente visibili.</span><span class="sxs-lookup"><span data-stu-id="ab1e5-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="ab1e5-107">La virtualizzazione, ad esempio, è utile quando l'applicazione deve eseguire il rendering di un lungo elenco di elementi ed è necessario che sia visibile solo un subset di elementi in un determinato momento.</span><span class="sxs-lookup"><span data-stu-id="ab1e5-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="ab1e5-108">Blazor fornisce il `Virtualize` componente che può essere usato per aggiungere la virtualizzazione ai componenti di un'app.</span><span class="sxs-lookup"><span data-stu-id="ab1e5-108">Blazor provides the `Virtualize` component that can be used to add virtualization to an app's components.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ab1e5-109">Senza virtualizzazione, un elenco tipico potrebbe usare un [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) ciclo C# per eseguire il rendering di ogni elemento nell'elenco:</span><span class="sxs-lookup"><span data-stu-id="ab1e5-109">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

<span data-ttu-id="ab1e5-110">Se l'elenco contiene migliaia di elementi, il rendering dell'elenco potrebbe richiedere molto tempo.</span><span class="sxs-lookup"><span data-stu-id="ab1e5-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="ab1e5-111">L'utente può riscontrare un ritardo dell'interfaccia utente evidente.</span><span class="sxs-lookup"><span data-stu-id="ab1e5-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="ab1e5-112">Anziché eseguire il rendering di ogni elemento nell'elenco in una sola volta, sostituire il [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) ciclo con il `Virtualize` componente e specificare un'origine di elemento fisso con `Items` .</span><span class="sxs-lookup"><span data-stu-id="ab1e5-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with `Items`.</span></span> <span data-ttu-id="ab1e5-113">Vengono visualizzati solo gli elementi attualmente visibili:</span><span class="sxs-lookup"><span data-stu-id="ab1e5-113">Only the items that are currently visible are rendered:</span></span>

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="ab1e5-114">Se non si specifica un contesto per il componente con `Context` , usare il `context` valore ( `@context.{PROPERTY}` ) nel modello di contenuto dell'elemento:</span><span class="sxs-lookup"><span data-stu-id="ab1e5-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="ab1e5-115">Il `Virtualize` componente calcola il numero di elementi di cui eseguire il rendering in base all'altezza del contenitore e alle dimensioni degli elementi di cui è stato eseguito il rendering.</span><span class="sxs-lookup"><span data-stu-id="ab1e5-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

<span data-ttu-id="ab1e5-116">Il contenuto dell'elemento per il `Virtualize` componente può includere:</span><span class="sxs-lookup"><span data-stu-id="ab1e5-116">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="ab1e5-117">HTML semplice e Razor codice, come illustrato nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="ab1e5-117">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="ab1e5-118">Uno o più Razor componenti.</span><span class="sxs-lookup"><span data-stu-id="ab1e5-118">One or more Razor components.</span></span>
* <span data-ttu-id="ab1e5-119">Combinazione di componenti HTML/ Razor e Razor .</span><span class="sxs-lookup"><span data-stu-id="ab1e5-119">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="ab1e5-120">Delegato del provider di elementi</span><span class="sxs-lookup"><span data-stu-id="ab1e5-120">Item provider delegate</span></span>

<span data-ttu-id="ab1e5-121">Se non si desidera caricare tutti gli elementi in memoria, è possibile specificare un metodo delegato del provider di elementi per il parametro del componente `ItemsProvider` che recupera in modo asincrono gli elementi richiesti su richiesta:</span><span class="sxs-lookup"><span data-stu-id="ab1e5-121">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's `ItemsProvider` parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="ab1e5-122">Il provider Items riceve un oggetto `ItemsProviderRequest` che specifica il numero di elementi richiesto a partire da un indice iniziale specifico.</span><span class="sxs-lookup"><span data-stu-id="ab1e5-122">The items provider receives an `ItemsProviderRequest`, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="ab1e5-123">Il provider Items recupera quindi gli elementi richiesti da un database o da un altro servizio e li restituisce come `ItemsProviderResult<TItem>` insieme al numero totale di elementi.</span><span class="sxs-lookup"><span data-stu-id="ab1e5-123">The items provider then retrieves the requested items from a database or other service and returns them as an `ItemsProviderResult<TItem>` along with a count of the total items.</span></span> <span data-ttu-id="ab1e5-124">Il provider di elementi può scegliere di recuperare gli elementi con ogni richiesta o di memorizzarli nella cache in modo che siano immediatamente disponibili.</span><span class="sxs-lookup"><span data-stu-id="ab1e5-124">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span> <span data-ttu-id="ab1e5-125">Non tentare di usare un provider di elementi e assegnare una raccolta a `Items` per lo stesso `Virtualize` componente.</span><span class="sxs-lookup"><span data-stu-id="ab1e5-125">Don't attempt to use an items provider and assign a collection to `Items` for the same `Virtualize` component.</span></span>

<span data-ttu-id="ab1e5-126">Nell'esempio seguente vengono caricati i dipendenti da un `EmployeeService` :</span><span class="sxs-lookup"><span data-stu-id="ab1e5-126">The following example loads employees from an `EmployeeService`:</span></span>

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

## <a name="placeholder"></a><span data-ttu-id="ab1e5-127">Segnaposto</span><span class="sxs-lookup"><span data-stu-id="ab1e5-127">Placeholder</span></span>

<span data-ttu-id="ab1e5-128">Poiché la richiesta di elementi da un'origine dati remota potrebbe richiedere del tempo, è possibile eseguire il rendering di un segnaposto ( `<Placeholder>...</Placeholder>` ) finché non sono disponibili i dati dell'elemento:</span><span class="sxs-lookup"><span data-stu-id="ab1e5-128">Because requesting items from a remote data source might take some time, you have the option to render a placeholder (`<Placeholder>...</Placeholder>`) until the item data is available:</span></span>

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

## <a name="item-size"></a><span data-ttu-id="ab1e5-129">Dimensioni dell'elemento</span><span class="sxs-lookup"><span data-stu-id="ab1e5-129">Item size</span></span>

<span data-ttu-id="ab1e5-130">È possibile impostare le dimensioni di ogni elemento in pixel con `ItemSize` (impostazione predefinita: 50px):</span><span class="sxs-lookup"><span data-stu-id="ab1e5-130">The size of each item in pixels can be set with `ItemSize` (default: 50px):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="ab1e5-131">Conteggio overscan</span><span class="sxs-lookup"><span data-stu-id="ab1e5-131">Overscan count</span></span>

<span data-ttu-id="ab1e5-132">`OverscanCount` determina il numero di elementi aggiuntivi che vengono sottoposti a rendering prima e dopo l'area visibile.</span><span class="sxs-lookup"><span data-stu-id="ab1e5-132">`OverscanCount` determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="ab1e5-133">Questa impostazione consente di ridurre la frequenza di rendering durante lo scorrimento.</span><span class="sxs-lookup"><span data-stu-id="ab1e5-133">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="ab1e5-134">Tuttavia, i valori più elevati comportano il rendering di più elementi nella pagina (impostazione predefinita: 3):</span><span class="sxs-lookup"><span data-stu-id="ab1e5-134">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="ab1e5-135">Ad esempio, una griglia o un elenco che esegue il rendering di centinaia di righe contenenti componenti comporta un utilizzo intensivo del processore per il rendering.</span><span class="sxs-lookup"><span data-stu-id="ab1e5-135">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="ab1e5-136">Si consiglia di virtualizzare un layout di griglia o elenco in modo da eseguire il rendering solo di un subset dei componenti in un determinato momento.</span><span class="sxs-lookup"><span data-stu-id="ab1e5-136">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="ab1e5-137">Per un esempio di rendering del subset di componenti, vedere i componenti seguenti nell' [ `Virtualization` app di esempio (archivio GitHub ASPNET/Samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span><span class="sxs-lookup"><span data-stu-id="ab1e5-137">For an example of component subset rendering, see the following components in the [`Virtualization` sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="ab1e5-138">`Virtualize` Component ( [`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs) ): componente scritto in C# che implementa <xref:Microsoft.AspNetCore.Components.ComponentBase> per eseguire il rendering di un set di righe di dati meteo in base allo scorrimento dell'utente.</span><span class="sxs-lookup"><span data-stu-id="ab1e5-138">`Virtualize` component ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="ab1e5-139">`FetchData` Component ( [`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor) ): usa il `Virtualize` componente per visualizzare 25 righe di dati meteorologici alla volta.</span><span class="sxs-lookup"><span data-stu-id="ab1e5-139">`FetchData` component ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

::: moniker-end

## <a name="state-changes"></a><span data-ttu-id="ab1e5-140">Modifiche stato</span><span class="sxs-lookup"><span data-stu-id="ab1e5-140">State changes</span></span>

<span data-ttu-id="ab1e5-141">Quando si apportano modifiche agli elementi di cui è stato eseguito il rendering dal `Virtualize` componente, chiamare <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> per forzare nuovamente la valutazione e il rendering del componente.</span><span class="sxs-lookup"><span data-stu-id="ab1e5-141">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
