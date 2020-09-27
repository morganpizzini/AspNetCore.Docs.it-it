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

Ad esempio, una griglia o un elenco che esegue il rendering di centinaia di righe contenenti componenti comporta un utilizzo intensivo del processore per il rendering. Si consiglia di virtualizzare un layout di griglia o elenco in modo da eseguire il rendering solo di un subset dei componenti in un determinato momento. Per un esempio di rendering del subset di componenti, vedere i componenti seguenti nell' [ `Virtualization` app di esempio (archivio GitHub ASPNET/Samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):

* `Virtualize` Component ( [`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs) ): componente scritto in C# che implementa <xref:Microsoft.AspNetCore.Components.ComponentBase> per eseguire il rendering di un set di righe di dati meteo in base allo scorrimento dell'utente.
* `FetchData` Component ( [`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor) ): usa il `Virtualize` componente per visualizzare 25 righe di dati meteorologici alla volta.

::: moniker-end

## <a name="state-changes"></a>Modifiche stato

Quando si apportano modifiche agli elementi di cui è stato eseguito il rendering dal `Virtualize` componente, chiamare <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> per forzare nuovamente la valutazione e il rendering del componente.
