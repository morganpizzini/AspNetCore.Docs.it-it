---
title: BlazorVirtualizzazione componenti ASP.NET Core
author: guardrex
description: Informazioni su come usare la virtualizzazione di componenti nelle Blazor app ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/21/2020
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
ms.openlocfilehash: 911eeeb445741aa1519e1464dd4a75e26f6f12ab
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847572"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="e9295-103">BlazorVirtualizzazione componenti ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e9295-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="e9295-104">Di [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="e9295-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="e9295-105">Migliorare le prestazioni percepite del rendering dei componenti utilizzando il Blazor supporto della virtualizzazione incorporato del Framework.</span><span class="sxs-lookup"><span data-stu-id="e9295-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="e9295-106">La virtualizzazione è una tecnica che consente di limitare il rendering dell'interfaccia utente solo alle parti attualmente visibili.</span><span class="sxs-lookup"><span data-stu-id="e9295-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="e9295-107">La virtualizzazione, ad esempio, è utile quando l'applicazione deve eseguire il rendering di un lungo elenco o una tabella con molte righe ed è necessario che sia visibile solo un subset di elementi in un determinato momento.</span><span class="sxs-lookup"><span data-stu-id="e9295-107">For example, virtualization is helpful when the app must render a long list or a table with many rows and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="e9295-108">Blazor fornisce il `Virtualize` componente che può essere usato per aggiungere la virtualizzazione ai componenti di un'app.</span><span class="sxs-lookup"><span data-stu-id="e9295-108">Blazor provides the `Virtualize` component that can be used to add virtualization to an app's components.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e9295-109">Senza virtualizzazione, è possibile che in un elenco tipico o in un componente basato su tabelle venga utilizzato un [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) ciclo C# per eseguire il rendering di ogni elemento nell'elenco o di ogni riga della tabella:</span><span class="sxs-lookup"><span data-stu-id="e9295-109">Without virtualization, a typical list or table-based component might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list or each row in the table:</span></span>

```razor
<table>
    @foreach (var employee in employees)
    {
        <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    }
</table>
```

<span data-ttu-id="e9295-110">Se l'elenco contiene migliaia di elementi, il rendering dell'elenco potrebbe richiedere molto tempo.</span><span class="sxs-lookup"><span data-stu-id="e9295-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="e9295-111">L'utente può riscontrare un ritardo dell'interfaccia utente evidente.</span><span class="sxs-lookup"><span data-stu-id="e9295-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="e9295-112">Anziché eseguire il rendering di ogni elemento nell'elenco in una sola volta, sostituire il [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) ciclo con il `Virtualize` componente e specificare un'origine di elemento fisso con `Items` .</span><span class="sxs-lookup"><span data-stu-id="e9295-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with `Items`.</span></span> <span data-ttu-id="e9295-113">Vengono visualizzati solo gli elementi attualmente visibili:</span><span class="sxs-lookup"><span data-stu-id="e9295-113">Only the items that are currently visible are rendered:</span></span>

```razor
<table>
    <Virtualize Context="employee" Items="@employees">
        <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

<span data-ttu-id="e9295-114">Se non si specifica un contesto per il componente con `Context` , usare il `context` valore ( `@context.{PROPERTY}` ) nel modello di contenuto dell'elemento:</span><span class="sxs-lookup"><span data-stu-id="e9295-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<table>
    <Virtualize Items="@employees">
        <tr>
            <td>@context.FirstName</td>
            <td>@context.LastName</td>
            <td>@context.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

<span data-ttu-id="e9295-115">Il `Virtualize` componente calcola il numero di elementi di cui eseguire il rendering in base all'altezza del contenitore e alle dimensioni degli elementi di cui è stato eseguito il rendering.</span><span class="sxs-lookup"><span data-stu-id="e9295-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="e9295-116">Delegato del provider di elementi</span><span class="sxs-lookup"><span data-stu-id="e9295-116">Item provider delegate</span></span>

<span data-ttu-id="e9295-117">Se non si desidera caricare tutti gli elementi in memoria, è possibile specificare un metodo delegato del provider di elementi per il parametro del componente `ItemsProvider` che recupera in modo asincrono gli elementi richiesti su richiesta:</span><span class="sxs-lookup"><span data-stu-id="e9295-117">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's `ItemsProvider` parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<table>
    <Virtualize Context="employee" ItemsProvider="@LoadEmployees">
         <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

<span data-ttu-id="e9295-118">Il provider Items riceve un oggetto `ItemsProviderRequest` che specifica il numero di elementi richiesto a partire da un indice iniziale specifico.</span><span class="sxs-lookup"><span data-stu-id="e9295-118">The items provider receives an `ItemsProviderRequest`, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="e9295-119">Il provider Items recupera quindi gli elementi richiesti da un database o da un altro servizio e li restituisce come `ItemsProviderResult<TItem>` insieme al numero totale di elementi.</span><span class="sxs-lookup"><span data-stu-id="e9295-119">The items provider then retrieves the requested items from a database or other service and returns them as an `ItemsProviderResult<TItem>` along with a count of the total items.</span></span> <span data-ttu-id="e9295-120">Il provider di elementi può scegliere di recuperare gli elementi con ogni richiesta o di memorizzarli nella cache in modo che siano immediatamente disponibili.</span><span class="sxs-lookup"><span data-stu-id="e9295-120">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span> <span data-ttu-id="e9295-121">Non tentare di usare un provider di elementi e assegnare una raccolta a `Items` per lo stesso `Virtualize` componente.</span><span class="sxs-lookup"><span data-stu-id="e9295-121">Don't attempt to use an items provider and assign a collection to `Items` for the same `Virtualize` component.</span></span>

<span data-ttu-id="e9295-122">Nell'esempio seguente vengono caricati i dipendenti da un `EmployeeService` :</span><span class="sxs-lookup"><span data-stu-id="e9295-122">The following example loads employees from an `EmployeeService`:</span></span>

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

## <a name="placeholder"></a><span data-ttu-id="e9295-123">Segnaposto</span><span class="sxs-lookup"><span data-stu-id="e9295-123">Placeholder</span></span>

<span data-ttu-id="e9295-124">Poiché la richiesta di elementi da un'origine dati remota potrebbe richiedere del tempo, è possibile eseguire il rendering di un segnaposto ( `<Placeholder>...</Placeholder>` ) finché non sono disponibili i dati dell'elemento:</span><span class="sxs-lookup"><span data-stu-id="e9295-124">Because requesting items from a remote data source might take some time, you have the option to render a placeholder (`<Placeholder>...</Placeholder>`) until the item data is available:</span></span>

```razor
<table>
    <Virtualize Context="employee" ItemsProvider="@LoadEmployees">
        <ItemContent>
            <tr>
                <td>@employee.FirstName</td>
                <td>@employee.LastName</td>
                <td>@employee.JobTitle</td>
            </tr>
        </ItemContent>
        <Placeholder>
            <tr>
                <td>Loading...</td>
            </tr>
        </Placeholder>
    </Virtualize>
</table>
```

## <a name="item-size"></a><span data-ttu-id="e9295-125">Dimensioni dell'elemento</span><span class="sxs-lookup"><span data-stu-id="e9295-125">Item size</span></span>

<span data-ttu-id="e9295-126">È possibile impostare le dimensioni di ogni elemento in pixel con `ItemSize` (impostazione predefinita: 50px):</span><span class="sxs-lookup"><span data-stu-id="e9295-126">The size of each item in pixels can be set with `ItemSize` (default: 50px):</span></span>

```razor
<table>
    <Virtualize Context="employee" Items="@employees" ItemSize="25">
        ...
    </Virtualize>
</table>
```

## <a name="overscan-count"></a><span data-ttu-id="e9295-127">Conteggio overscan</span><span class="sxs-lookup"><span data-stu-id="e9295-127">Overscan count</span></span>

<span data-ttu-id="e9295-128">`OverscanCount` determina il numero di elementi aggiuntivi che vengono sottoposti a rendering prima e dopo l'area visibile.</span><span class="sxs-lookup"><span data-stu-id="e9295-128">`OverscanCount` determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="e9295-129">Questa impostazione consente di ridurre la frequenza di rendering durante lo scorrimento.</span><span class="sxs-lookup"><span data-stu-id="e9295-129">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="e9295-130">Tuttavia, i valori più elevati comportano il rendering di più elementi nella pagina (impostazione predefinita: 3):</span><span class="sxs-lookup"><span data-stu-id="e9295-130">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<table>
    <Virtualize Context="employee" Items="@employees" OverscanCount="4">
        ...
    </Virtualize>
</table>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="e9295-131">Ad esempio, una griglia o un elenco che esegue il rendering di centinaia di righe contenenti componenti comporta un utilizzo intensivo del processore per il rendering.</span><span class="sxs-lookup"><span data-stu-id="e9295-131">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="e9295-132">Si consiglia di virtualizzare un layout di griglia o elenco in modo da eseguire il rendering solo di un subset dei componenti in un determinato momento.</span><span class="sxs-lookup"><span data-stu-id="e9295-132">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="e9295-133">Per un esempio di rendering del subset di componenti, vedere i componenti seguenti nell' [ `Virtualization` app di esempio (archivio GitHub ASPNET/Samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span><span class="sxs-lookup"><span data-stu-id="e9295-133">For an example of component subset rendering, see the following components in the [`Virtualization` sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="e9295-134">`Virtualize` Component ( [`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs) ): componente scritto in C# che implementa <xref:Microsoft.AspNetCore.Components.ComponentBase> per eseguire il rendering di un set di righe di dati meteo in base allo scorrimento dell'utente.</span><span class="sxs-lookup"><span data-stu-id="e9295-134">`Virtualize` component ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="e9295-135">`FetchData` Component ( [`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor) ): usa il `Virtualize` componente per visualizzare 25 righe di dati meteorologici alla volta.</span><span class="sxs-lookup"><span data-stu-id="e9295-135">`FetchData` component ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

::: moniker-end

## <a name="state-changes"></a><span data-ttu-id="e9295-136">Modifiche stato</span><span class="sxs-lookup"><span data-stu-id="e9295-136">State changes</span></span>

<span data-ttu-id="e9295-137">Quando si apportano modifiche agli elementi di cui è stato eseguito il rendering dal `Virtualize` componente, chiamare <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> per forzare nuovamente la valutazione e il rendering del componente.</span><span class="sxs-lookup"><span data-stu-id="e9295-137">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
