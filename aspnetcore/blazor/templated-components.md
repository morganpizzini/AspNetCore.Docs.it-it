---
title: ASP.NET Core Blazor componenti basati su modelli
author: guardrex
description: Informazioni su come i componenti basati su modelli possono accettare uno o più modelli di interfaccia utente come parametri, che possono quindi essere usati come parte della logica di rendering del componente.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templated-components
ms.openlocfilehash: 8c970387fa79b02127c8a2375195373148749679
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "83851889"
---
# <a name="aspnet-core-blazor-templated-components"></a>ASP.NET Core Blazor componenti basati su modelli

Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

I componenti basati su modelli sono componenti che accettano uno o più modelli di interfaccia utente come parametri, che possono quindi essere usati come parte della logica di rendering del componente. I componenti basati su modelli consentono di creare componenti di livello superiore più riutilizzabili rispetto ai componenti normali. Di seguito sono riportati alcuni esempi:

* Componente della tabella che consente a un utente di specificare i modelli per l'intestazione, le righe e il piè di pagina della tabella.
* Componente di elenco che consente a un utente di specificare un modello per il rendering degli elementi in un elenco.

## <a name="template-parameters"></a>Parametri di modelli

Un componente basato su modelli viene definito specificando uno o più parametri del componente di tipo <xref:Microsoft.AspNetCore.Components.RenderFragment> o <xref:Microsoft.AspNetCore.Components.RenderFragment%601> . Un frammento di rendering rappresenta un segmento di interfaccia utente di cui eseguire il rendering. <xref:Microsoft.AspNetCore.Components.RenderFragment%601>accetta un parametro di tipo che può essere specificato quando viene richiamato il frammento di rendering.

`TableTemplate`componente

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

Quando si usa un componente basato su modelli, i parametri del modello possono essere specificati usando gli elementi figlio che corrispondono ai nomi dei parametri ( `TableHeader` e `RowTemplate` nell'esempio seguente):

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

> [!NOTE]
> I vincoli di tipo generico saranno supportati in una versione futura. Per ulteriori informazioni, vedere [Consenti vincoli di tipo generico (DotNet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).

## <a name="template-context-parameters"></a>Parametri di contesto del modello

Gli argomenti del componente di tipo <xref:Microsoft.AspNetCore.Components.RenderFragment%601> passati come elementi hanno un parametro implicito denominato `context` (ad esempio, nell'esempio di codice precedente, `@context.PetId` ), ma è possibile modificare il nome del parametro usando l' `Context` attributo nell'elemento figlio. Nell'esempio seguente, l' `RowTemplate` attributo dell'elemento `Context` specifica il `pet` parametro:

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

In alternativa, è possibile specificare l' `Context` attributo sull'elemento Component. L' `Context` attributo specificato si applica a tutti i parametri di modello specificati. Questa operazione può essere utile quando si desidera specificare il nome del parametro di contenuto per il contenuto figlio implicito (senza alcun elemento figlio di wrapping). Nell'esempio seguente l' `Context` attributo viene visualizzato nell' `TableTemplate` elemento e si applica a tutti i parametri del modello:

```razor
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

## <a name="generic-typed-components"></a>Componenti tipizzati in modo generico

I componenti basati su modelli spesso sono tipizzati in modo generico. È ad esempio `ListViewTemplate` possibile utilizzare un componente generico per eseguire il rendering `IEnumerable<T>` dei valori. Per definire un componente generico, usare la [`@typeparam`](xref:mvc/views/razor#typeparam) direttiva per specificare i parametri di tipo:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

Quando si usano componenti tipizzati generici, il parametro di tipo viene dedotto, se possibile:

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

In caso contrario, il parametro di tipo deve essere specificato in modo esplicito utilizzando un attributo che corrisponde al nome del parametro di tipo. Nell'esempio seguente viene `TItem="Pet"` specificato il tipo:

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
