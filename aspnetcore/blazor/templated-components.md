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
# <a name="aspnet-core-opno-locblazor-templated-components"></a><span data-ttu-id="bfedc-103">Componenti Blazor basati su modelli di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bfedc-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="bfedc-104">Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="bfedc-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="bfedc-105">I componenti basati su modelli sono componenti che accettano uno o più modelli di interfaccia utente come parametri, che possono quindi essere utilizzati come parte della logica di rendering del componente.</span><span class="sxs-lookup"><span data-stu-id="bfedc-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="bfedc-106">I componenti basati su modelli consentono di creare componenti di livello superiore più riutilizzabili rispetto ai componenti normali.</span><span class="sxs-lookup"><span data-stu-id="bfedc-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="bfedc-107">Un paio di esempi includono:</span><span class="sxs-lookup"><span data-stu-id="bfedc-107">A couple of examples include:</span></span>

* <span data-ttu-id="bfedc-108">Componente di tabella che consente all'utente di specificare i modelli per l'intestazione, le righe e il piè di pagina della tabella.</span><span class="sxs-lookup"><span data-stu-id="bfedc-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="bfedc-109">Componente elenco che consente a un utente di specificare un modello per il rendering degli elementi in un elenco.</span><span class="sxs-lookup"><span data-stu-id="bfedc-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

## <a name="template-parameters"></a><span data-ttu-id="bfedc-110">Parametri di modelli</span><span class="sxs-lookup"><span data-stu-id="bfedc-110">Template parameters</span></span>

<span data-ttu-id="bfedc-111">Un componente basato su modelli viene definito specificando `RenderFragment` `RenderFragment<T>`uno o più parametri del componente di tipo o .</span><span class="sxs-lookup"><span data-stu-id="bfedc-111">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="bfedc-112">Un frammento di rendering rappresenta un segmento dell'interfaccia utente di cui eseguire il rendering.</span><span class="sxs-lookup"><span data-stu-id="bfedc-112">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="bfedc-113">`RenderFragment<T>`accetta un parametro di tipo che può essere specificato quando viene richiamato il frammento di rendering.</span><span class="sxs-lookup"><span data-stu-id="bfedc-113">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="bfedc-114">`TableTemplate`Componente:</span><span class="sxs-lookup"><span data-stu-id="bfedc-114">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="bfedc-115">Quando si utilizza un componente basato su modelli, i parametri del`TableHeader` modello `RowTemplate` possono essere specificati utilizzando elementi figlio che corrispondono ai nomi dei parametri ( e nell'esempio seguente):</span><span class="sxs-lookup"><span data-stu-id="bfedc-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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
> <span data-ttu-id="bfedc-116">I vincoli di tipo generico saranno supportati in una versione futura.</span><span class="sxs-lookup"><span data-stu-id="bfedc-116">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="bfedc-117">Per ulteriori informazioni, consultate Consentire vincoli di [tipo generico (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span><span class="sxs-lookup"><span data-stu-id="bfedc-117">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="bfedc-118">Parametri di contesto del modello</span><span class="sxs-lookup"><span data-stu-id="bfedc-118">Template context parameters</span></span>

<span data-ttu-id="bfedc-119">Gli argomenti `RenderFragment<T>` del componente di tipo `context` passato come elementi dispongono di `@context.PetId`un parametro implicito denominato `Context` , ad esempio dall'esempio di codice precedente, ), ma è possibile modificare il nome del parametro utilizzando l'attributo sull'elemento figlio.</span><span class="sxs-lookup"><span data-stu-id="bfedc-119">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="bfedc-120">Nell'esempio seguente, `RowTemplate` l'attributo dell'elemento `Context` specifica il `pet` parametro:</span><span class="sxs-lookup"><span data-stu-id="bfedc-120">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

<span data-ttu-id="bfedc-121">In alternativa, è `Context` possibile specificare l'attributo sull'elemento componente.</span><span class="sxs-lookup"><span data-stu-id="bfedc-121">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="bfedc-122">L'attributo specificato `Context` si applica a tutti i parametri di modello specificati.</span><span class="sxs-lookup"><span data-stu-id="bfedc-122">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="bfedc-123">Ciò può essere utile quando si desidera specificare il nome del parametro di contenuto per il contenuto figlio implicito (senza alcun elemento figlio di ritorno a capo).</span><span class="sxs-lookup"><span data-stu-id="bfedc-123">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="bfedc-124">Nell'esempio seguente, `Context` l'attributo viene visualizzato sull'elemento `TableTemplate` e si applica a tutti i parametri di modello:</span><span class="sxs-lookup"><span data-stu-id="bfedc-124">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

## <a name="generic-typed-components"></a><span data-ttu-id="bfedc-125">Componenti di tipo generico</span><span class="sxs-lookup"><span data-stu-id="bfedc-125">Generic-typed components</span></span>

<span data-ttu-id="bfedc-126">I componenti basati su modelli sono spesso tipizzato in modo generico.</span><span class="sxs-lookup"><span data-stu-id="bfedc-126">Templated components are often generically typed.</span></span> <span data-ttu-id="bfedc-127">Ad esempio, `ListViewTemplate` un componente generico `IEnumerable<T>` può essere utilizzato per eseguire il rendering dei valori.</span><span class="sxs-lookup"><span data-stu-id="bfedc-127">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="bfedc-128">Per definire un componente [`@typeparam`](xref:mvc/views/razor#typeparam) generico, utilizzare la direttiva per specificare i parametri di tipo:To define a generic component, use the directive to specify type parameters:</span><span class="sxs-lookup"><span data-stu-id="bfedc-128">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="bfedc-129">Quando si utilizzano componenti di tipo generico, il parametro di tipo viene dedotto se possibile:</span><span class="sxs-lookup"><span data-stu-id="bfedc-129">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="bfedc-130">In caso contrario, il parametro di tipo deve essere specificato in modo esplicito utilizzando un attributo che corrisponde al nome del parametro di tipo.</span><span class="sxs-lookup"><span data-stu-id="bfedc-130">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="bfedc-131">Nell'esempio seguente, `TItem="Pet"` specifica il tipo:</span><span class="sxs-lookup"><span data-stu-id="bfedc-131">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
