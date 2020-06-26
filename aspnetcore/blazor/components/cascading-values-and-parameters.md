---
title: BlazorParametri e valori di propagazione ASP.NET Core
author: guardrex
description: Informazioni su come eseguire il flusso dei dati da un componente predecessore ai componenti discendenti.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/16/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: c72bf5584c7e0afdeadbb4103b5e58d91ae8c1e9
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399218"
---
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a><span data-ttu-id="8bfd6-103">BlazorParametri e valori di propagazione ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8bfd6-103">ASP.NET Core Blazor cascading values and parameters</span></span>

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="8bfd6-104">Valori e parametri a cascata</span><span class="sxs-lookup"><span data-stu-id="8bfd6-104">Cascading values and parameters</span></span>

<span data-ttu-id="8bfd6-105">In alcuni scenari, non è pratico eseguire il flusso dei dati da un componente predecessore a un componente discendente usando i [parametri del componente](xref:blazor/components/index#component-parameters), soprattutto quando sono presenti diversi livelli di componenti.</span><span class="sxs-lookup"><span data-stu-id="8bfd6-105">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](xref:blazor/components/index#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="8bfd6-106">I valori e i parametri di propagazione consentono di risolvere questo problema fornendo un modo pratico per un componente predecessore per fornire un valore a tutti i relativi componenti discendenti.</span><span class="sxs-lookup"><span data-stu-id="8bfd6-106">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="8bfd6-107">I parametri e i valori di propagazione forniscono anche un approccio per la coordinazione dei componenti.</span><span class="sxs-lookup"><span data-stu-id="8bfd6-107">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="8bfd6-108">Esempio di tema</span><span class="sxs-lookup"><span data-stu-id="8bfd6-108">Theme example</span></span>

<span data-ttu-id="8bfd6-109">Nell'esempio seguente dall'app di esempio, la `ThemeInfo` classe specifica le informazioni sul tema per scorrere la gerarchia dei componenti in modo che tutti i pulsanti all'interno di una determinata parte dell'app condividono lo stesso stile.</span><span class="sxs-lookup"><span data-stu-id="8bfd6-109">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="8bfd6-110">`UIThemeClasses/ThemeInfo.cs`:</span><span class="sxs-lookup"><span data-stu-id="8bfd6-110">`UIThemeClasses/ThemeInfo.cs`:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="8bfd6-111">Un componente predecessore può fornire un valore di propagazione utilizzando il componente valore di propagazione.</span><span class="sxs-lookup"><span data-stu-id="8bfd6-111">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="8bfd6-112">Il <xref:Microsoft.AspNetCore.Components.CascadingValue%601> componente esegue il wrapping di un sottoalbero della gerarchia dei componenti e fornisce un singolo valore a tutti i componenti all'interno di tale sottoalbero.</span><span class="sxs-lookup"><span data-stu-id="8bfd6-112">The <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="8bfd6-113">Ad esempio, l'app di esempio specifica le informazioni sul tema ( `ThemeInfo` ) in uno dei layout dell'app come parametro di propagazione per tutti i componenti che costituiscono il corpo del layout della `@Body` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="8bfd6-113">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="8bfd6-114">`ButtonClass`viene assegnato un valore di `btn-success` nel componente layout.</span><span class="sxs-lookup"><span data-stu-id="8bfd6-114">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="8bfd6-115">Qualsiasi componente discendente può utilizzare questa proprietà tramite l' `ThemeInfo` oggetto a cascata.</span><span class="sxs-lookup"><span data-stu-id="8bfd6-115">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="8bfd6-116">`CascadingValuesParametersLayout`componente</span><span class="sxs-lookup"><span data-stu-id="8bfd6-116">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="8bfd6-117">Per utilizzare i valori di propagazione, i componenti dichiarano i parametri di propagazione utilizzando l' [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="8bfd6-117">To make use of cascading values, components declare cascading parameters using the [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="8bfd6-118">I valori a cascata vengono associati ai parametri di propagazione per tipo.</span><span class="sxs-lookup"><span data-stu-id="8bfd6-118">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="8bfd6-119">Nell'app di esempio, il `CascadingValuesParametersTheme` componente associa il `ThemeInfo` valore a cascata a un parametro di propagazione.</span><span class="sxs-lookup"><span data-stu-id="8bfd6-119">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="8bfd6-120">Il parametro viene usato per impostare la classe CSS per uno dei pulsanti visualizzati dal componente.</span><span class="sxs-lookup"><span data-stu-id="8bfd6-120">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="8bfd6-121">`CascadingValuesParametersTheme`componente</span><span class="sxs-lookup"><span data-stu-id="8bfd6-121">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="8bfd6-122">Per eseguire il propagazione di più valori dello stesso tipo all'interno dello stesso sottoalbero, fornire una stringa univoca <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> a ogni <xref:Microsoft.AspNetCore.Components.CascadingValue%601> componente e l' [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attributo corrispondente.</span><span class="sxs-lookup"><span data-stu-id="8bfd6-122">To cascade multiple values of the same type within the same subtree, provide a unique <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> string to each <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component and its corresponding [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="8bfd6-123">Nell'esempio seguente due componenti propagano <xref:Microsoft.AspNetCore.Components.CascadingValue%601> istanze diverse di `MyCascadingType` per nome:</span><span class="sxs-lookup"><span data-stu-id="8bfd6-123">In the following example, two <xref:Microsoft.AspNetCore.Components.CascadingValue%601> components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value=@parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="8bfd6-124">In un componente discendente i parametri a cascata ricevono i rispettivi valori dai valori a cascata corrispondenti nel componente predecessore per nome:</span><span class="sxs-lookup"><span data-stu-id="8bfd6-124">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="8bfd6-125">Esempio di tabulazione</span><span class="sxs-lookup"><span data-stu-id="8bfd6-125">TabSet example</span></span>

<span data-ttu-id="8bfd6-126">I parametri di propagazione consentono inoltre ai componenti di collaborare attraverso la gerarchia dei componenti.</span><span class="sxs-lookup"><span data-stu-id="8bfd6-126">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="8bfd6-127">Si consideri, ad esempio, l' `TabSet` esempio seguente nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="8bfd6-127">For example, consider the following `TabSet` example in the sample app.</span></span>

<span data-ttu-id="8bfd6-128">L'app di esempio ha un' `ITab` interfaccia implementata dalle schede:</span><span class="sxs-lookup"><span data-stu-id="8bfd6-128">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](../common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="8bfd6-129">Il `CascadingValuesParametersTabSet` componente usa il `TabSet` componente, che contiene diversi `Tab` componenti:</span><span class="sxs-lookup"><span data-stu-id="8bfd6-129">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

<span data-ttu-id="8bfd6-130">I `Tab` componenti figlio non vengono passati in modo esplicito come parametri a `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="8bfd6-130">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="8bfd6-131">Al contrario, i `Tab` componenti figlio fanno parte del contenuto figlio di `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="8bfd6-131">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="8bfd6-132">Tuttavia, `TabSet` è comunque necessario conoscere ogni componente in `Tab` modo che sia in grado di eseguire il rendering delle intestazioni e della scheda attiva. Per abilitare questo coordinamento senza richiedere codice aggiuntivo, il `TabSet` componente *può fornire se stesso come valore* di propagazione che viene quindi prelevato dai `Tab` componenti discendenti.</span><span class="sxs-lookup"><span data-stu-id="8bfd6-132">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="8bfd6-133">`TabSet`componente</span><span class="sxs-lookup"><span data-stu-id="8bfd6-133">`TabSet` component:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="8bfd6-134">I componenti discendenti `Tab` acquisiscono l'oggetto che contiene `TabSet` come parametro di propagazione, in modo `Tab` che i componenti si aggiungano alla `TabSet` coordinata e della scheda attiva.</span><span class="sxs-lookup"><span data-stu-id="8bfd6-134">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="8bfd6-135">`Tab`componente</span><span class="sxs-lookup"><span data-stu-id="8bfd6-135">`Tab` component:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]