---
title: Componenti Blazor basati su modelli di ASP.NET Core
author: guardrex
description: Informazioni su come i componenti basati su modelli possono accettare uno o più modelli di interfaccia utente come parametri, che possono quindi essere usati come parte della logica di rendering del componente.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templated-components
ms.openlocfilehash: b57e3fe186402723607e90b1628062f602c77632
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "79989492"
---
# <a name="aspnet-core-opno-locblazor-templated-components"></a>Componenti Blazor basati su modelli di ASP.NET Core

Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

I componenti basati su modelli sono componenti che accettano uno o più modelli di interfaccia utente come parametri, che possono quindi essere utilizzati come parte della logica di rendering del componente. I componenti basati su modelli consentono di creare componenti di livello superiore più riutilizzabili rispetto ai componenti normali. Un paio di esempi includono:

* Componente di tabella che consente all'utente di specificare i modelli per l'intestazione, le righe e il piè di pagina della tabella.
* Componente elenco che consente a un utente di specificare un modello per il rendering degli elementi in un elenco.

## <a name="template-parameters"></a>Parametri di modelli

Un componente basato su modelli viene definito specificando `RenderFragment` `RenderFragment<T>`uno o più parametri del componente di tipo o . Un frammento di rendering rappresenta un segmento dell'interfaccia utente di cui eseguire il rendering. `RenderFragment<T>`accetta un parametro di tipo che può essere specificato quando viene richiamato il frammento di rendering.

`TableTemplate`Componente:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

Quando si utilizza un componente basato su modelli, i parametri del`TableHeader` modello `RowTemplate` possono essere specificati utilizzando elementi figlio che corrispondono ai nomi dei parametri ( e nell'esempio seguente):

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
> I vincoli di tipo generico saranno supportati in una versione futura. Per ulteriori informazioni, consultate Consentire vincoli di [tipo generico (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).

## <a name="template-context-parameters"></a>Parametri di contesto del modello

Gli argomenti `RenderFragment<T>` del componente di tipo `context` passato come elementi dispongono di `@context.PetId`un parametro implicito denominato `Context` , ad esempio dall'esempio di codice precedente, ), ma è possibile modificare il nome del parametro utilizzando l'attributo sull'elemento figlio. Nell'esempio seguente, `RowTemplate` l'attributo dell'elemento `Context` specifica il `pet` parametro:

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

In alternativa, è `Context` possibile specificare l'attributo sull'elemento componente. L'attributo specificato `Context` si applica a tutti i parametri di modello specificati. Ciò può essere utile quando si desidera specificare il nome del parametro di contenuto per il contenuto figlio implicito (senza alcun elemento figlio di ritorno a capo). Nell'esempio seguente, `Context` l'attributo viene visualizzato sull'elemento `TableTemplate` e si applica a tutti i parametri di modello:

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

## <a name="generic-typed-components"></a>Componenti di tipo generico

I componenti basati su modelli sono spesso tipizzato in modo generico. Ad esempio, `ListViewTemplate` un componente generico `IEnumerable<T>` può essere utilizzato per eseguire il rendering dei valori. Per definire un componente [`@typeparam`](xref:mvc/views/razor#typeparam) generico, utilizzare la direttiva per specificare i parametri di tipo:To define a generic component, use the directive to specify type parameters:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

Quando si utilizzano componenti di tipo generico, il parametro di tipo viene dedotto se possibile:

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

In caso contrario, il parametro di tipo deve essere specificato in modo esplicito utilizzando un attributo che corrisponde al nome del parametro di tipo. Nell'esempio seguente, `TItem="Pet"` specifica il tipo:

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
