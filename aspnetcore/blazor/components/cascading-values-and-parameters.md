---
title: BlazorParametri e valori di propagazione ASP.NET Core
author: guardrex
description: Informazioni su come eseguire il flusso dei dati da un componente predecessore ai componenti discendenti.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: 1ee30a84e0869f6b9ee937648eb87bd41e8702e3
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014711"
---
# <a name="aspnet-core-no-locblazor-cascading-values-and-parameters"></a>BlazorParametri e valori di propagazione ASP.NET Core

Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))

In alcuni scenari, non è pratico eseguire il flusso dei dati da un componente predecessore a un componente discendente usando i [parametri del componente](xref:blazor/components/index#component-parameters), soprattutto quando sono presenti diversi livelli di componenti. I valori e i parametri di propagazione consentono di risolvere questo problema fornendo un modo pratico per un componente predecessore per fornire un valore a tutti i relativi componenti discendenti. I parametri e i valori di propagazione forniscono anche un approccio per la coordinazione dei componenti.

### <a name="theme-example"></a>Esempio di tema

Nell'esempio seguente dall'app di esempio, la `ThemeInfo` classe specifica le informazioni sul tema per scorrere la gerarchia dei componenti in modo che tutti i pulsanti all'interno di una determinata parte dell'app condividono lo stesso stile.

`UIThemeClasses/ThemeInfo.cs`:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Un componente predecessore può fornire un valore di propagazione utilizzando il componente valore di propagazione. Il <xref:Microsoft.AspNetCore.Components.CascadingValue%601> componente esegue il wrapping di un sottoalbero della gerarchia dei componenti e fornisce un singolo valore a tutti i componenti all'interno di tale sottoalbero.

Ad esempio, l'app di esempio specifica le informazioni sul tema ( `ThemeInfo` ) in uno dei layout dell'app come parametro di propagazione per tutti i componenti che costituiscono il corpo del layout della `@Body` Proprietà. `ButtonClass`viene assegnato un valore di `btn-success` nel componente layout. Qualsiasi componente discendente può utilizzare questa proprietà tramite l' `ThemeInfo` oggetto a cascata.

`CascadingValuesParametersLayout`componente

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

Per utilizzare i valori di propagazione, i componenti dichiarano i parametri di propagazione utilizzando l' [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attributo. I valori a cascata vengono associati ai parametri di propagazione per tipo.

Nell'app di esempio, il `CascadingValuesParametersTheme` componente associa il `ThemeInfo` valore a cascata a un parametro di propagazione. Il parametro viene usato per impostare la classe CSS per uno dei pulsanti visualizzati dal componente.

`CascadingValuesParametersTheme`componente

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

Per eseguire il propagazione di più valori dello stesso tipo all'interno dello stesso sottoalbero, fornire una stringa univoca <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> a ogni <xref:Microsoft.AspNetCore.Components.CascadingValue%601> componente e l' [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attributo corrispondente. Nell'esempio seguente due componenti propagano <xref:Microsoft.AspNetCore.Components.CascadingValue%601> istanze diverse di `MyCascadingType` per nome:

```razor
<CascadingValue Value="@parentCascadeParameter1" Name="CascadeParam1">
    <CascadingValue Value="@ParentCascadeParameter2" Name="CascadeParam2">
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

In un componente discendente i parametri a cascata ricevono i rispettivi valori dai valori a cascata corrispondenti nel componente predecessore per nome:

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>Esempio di tabulazione

I parametri di propagazione consentono inoltre ai componenti di collaborare attraverso la gerarchia dei componenti. Si consideri, ad esempio, l' `TabSet` esempio seguente nell'app di esempio.

L'app di esempio ha un' `ITab` interfaccia implementata dalle schede:

[!code-csharp[](../common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

Il `CascadingValuesParametersTabSet` componente usa il `TabSet` componente, che contiene diversi `Tab` componenti:

```razor
@page "/CascadingValuesParametersTabSet"

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

@code {
    private bool showThirdTab;
}
```

I `Tab` componenti figlio non vengono passati in modo esplicito come parametri a `TabSet` . Al contrario, i `Tab` componenti figlio fanno parte del contenuto figlio di `TabSet` . Tuttavia, `TabSet` è comunque necessario conoscere ogni componente in `Tab` modo che sia in grado di eseguire il rendering delle intestazioni e della scheda attiva. Per abilitare questo coordinamento senza richiedere codice aggiuntivo, il `TabSet` componente *può fornire se stesso come valore* di propagazione che viene quindi prelevato dai `Tab` componenti discendenti.

`TabSet`componente

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

I componenti discendenti `Tab` acquisiscono l'oggetto che contiene `TabSet` come parametro di propagazione, in modo `Tab` che i componenti si aggiungano alla `TabSet` coordinata e della scheda attiva.

`Tab`componente

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]
