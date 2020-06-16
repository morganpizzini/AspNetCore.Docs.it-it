---
title: Helper tag di componente in ASP.NET Core
author: guardrex
ms.author: riande
description: Informazioni su come usare l'helper Tag componente ASP.NET Core per eseguire il rendering dei Razor componenti in pagine e visualizzazioni.
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: b6e3890e13ef5ad20098d3907b6895046087aeca
ms.sourcegitcommit: b0062f29cba2e5c21b95cf89eaf435ba830d11a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/15/2020
ms.locfileid: "84776501"
---
# <a name="component-tag-helper-in-aspnet-core"></a>Helper tag di componente in ASP.NET Core

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Per eseguire il rendering di un componente da una pagina o da una vista, usare l' [Helper Tag Component](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).

## <a name="prerequisites"></a>Prerequisiti

Seguire le istruzioni riportate nella sezione *preparare l'app per l'uso dei componenti in pagine e viste* dell' <xref:blazor/integrate-components#prepare-the-app> articolo.

## <a name="component-tag-helper"></a>Helper Tag componente

L'helper tag di componente seguente esegue il rendering del `Counter` componente in una pagina o in una visualizzazione:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

Nell'esempio precedente si presuppone che il `Counter` componente si trovi nella cartella *pages* dell'app. Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `@using BlazorSample.Pages` ).

L'helper Tag Component può anche passare parametri ai componenti. Si consideri il `ColorfulCheckbox` componente seguente che imposta il colore e le dimensioni dell'etichetta della casella di controllo:

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

I `Size` `int` parametri del componente () e `Color` ( `string` ) possono essere impostati dall'helper tag dei componenti: [component parameters](xref:blazor/components#component-parameters)

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

Nell'esempio precedente si presuppone che il `ColorfulCheckbox` componente si trovi nella cartella *condivisa* dell'app. Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `@using BlazorSample.Shared` ).

Viene eseguito il rendering del codice HTML seguente nella pagina o nella visualizzazione:

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

Il passaggio di una stringa racchiusa tra virgolette richiede un' [ Razor espressione esplicita](xref:mvc/views/razor#explicit-razor-expressions), come illustrato `param-Color` nell'esempio precedente. Il Razor comportamento di analisi per un `string` valore di tipo non si applica a un `param-*` attributo perché l'attributo è di `object` tipo.

Il tipo di parametro deve essere serializzabile JSON, che in genere significa che il tipo deve avere un costruttore predefinito e le proprietà impostabili. Ad esempio, è possibile specificare un valore per `Size` e `Color` nell'esempio precedente perché i tipi di `Size` e `Color` sono tipi primitivi ( `int` e `string` ), che sono supportati dal serializzatore JSON.

Nell'esempio seguente viene passato un oggetto classe al componente:

*MyClass.cs*:

```csharp
public class MyClass
{
    public MyClass()
    {
    }

    public int MyInt { get; set; } = 999;
    public string MyString { get; set; } = "Initial value";
}
```

**La classe deve avere un costruttore pubblico senza parametri.**

*Shared/componente. Razor*:

```razor
<h2>MyComponent</h2>

<p>Int: @MyObject.MyInt</p>
<p>String: @MyObject.MyString</p>

@code
{
    [Parameter]
    public MyClass MyObject { get; set; }
}
```

*Pagine/pagina. cshtml*:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}
@using {APP ASSEMBLY}.Shared

...

@{
    var myObject = new MyClass();
    myObject.MyInt = 7;
    myObject.MyString = "Set by MyPage";
}

<component type="typeof(MyComponent)" render-mode="ServerPrerendered" 
    param-MyObject="@myObject" />
```

Nell'esempio precedente si presuppone che il `MyComponent` componente si trovi nella cartella *condivisa* dell'app. Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app, ad esempio `@using BlazorSample` e `@using BlazorSample.Shared` . `MyClass`si trova nello spazio dei nomi dell'app.

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>Configura se il componente:

* Viene preeseguito nella pagina.
* Viene sottoposto a rendering come HTML statico nella pagina o se include le informazioni necessarie per il bootstrap di un' Blazor app dall'agente utente.

| Modalità di rendering | Descrizione |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Esegue il rendering del componente in HTML statico e include un marcatore per un' Blazor app Server. Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Esegue il rendering di un marcatore per un' Blazor app Server. L'output del componente non è incluso. Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Esegue il rendering del componente in HTML statico. |

Mentre le pagine e le visualizzazioni possono usare i componenti, il contrario non è vero. I componenti non possono utilizzare funzionalità specifiche di visualizzazione e pagina, ad esempio visualizzazioni parziali e sezioni. Per usare la logica da una visualizzazione parziale in un componente, scomporre la logica di visualizzazione parziale in un componente.

Il rendering dei componenti server da una pagina HTML statica non è supportato.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
