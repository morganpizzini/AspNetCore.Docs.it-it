---
title: Helper tag componente in ASP.NET CoreComponent Tag Helper in ASP.NET Core
author: guardrex
ms.author: riande
description: Informazioni su come utilizzare ASP.NET Core Component Tag Helper per eseguire il rendering dei componenti Razor nelle pagine e nelle viste.
ms.custom: mvc
ms.date: 04/01/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 4a6b21229ce086099fcddfeb51c3a959ef639f24
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123424"
---
# <a name="component-tag-helper-in-aspnet-core"></a>Helper tag componente in ASP.NET CoreComponent Tag Helper in ASP.NET Core

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Per eseguire il rendering di un componente da una pagina o da una vista, utilizzate [l'helper tag componente](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).

## <a name="prerequisites"></a>Prerequisiti

Seguire le istruzioni nella sezione *Preparare l'app per l'uso* dei componenti nelle pagine e nelle visualizzazioni dell'articolo. <xref:blazor/integrate-components#prepare-the-app-to-use-components-in-pages-and-views>

## <a name="component-tag-helper"></a>Helper tag componente

Il seguente Component Tag `Counter` Helper esegue il rendering del componente in una pagina o in una vista:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

L'esempio precedente presuppone `Counter` che il componente si trova nella cartella *Pages* dell'app.

L'helper tag componente può anche passare parametri ai componenti. Si consideri il componente seguente `ColorfulCheckbox` che imposta il colore e le dimensioni dell'etichetta della casella di controllo:

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

I `Size` `int`parametri `Color` del`string` [componente](xref:blazor/components#component-parameters) ( ) e ( ) possono essere impostati dall'helper tag componente:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

Nell'esempio precedente si `ColorfulCheckbox` presuppone che il componente si trova nella cartella *Shared* dell'app.

Nella pagina o nella visualizzazione viene eseguito il rendering del codice HTML seguente:

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

Il passaggio di una stringa tra virgolette `param-Color` richiede [un'espressione Razor esplicita,](xref:mvc/views/razor#explicit-razor-expressions)come illustrato nell'esempio precedente. Il comportamento di analisi Razor per un `string` valore `param-*` di tipo non `object` si applica a un attributo perché l'attributo è un tipo.

Il tipo di parametro deve essere serializzabile IN JSON, il che in genere significa che il tipo deve avere un costruttore predefinito e proprietà impostabili. Ad esempio, è possibile `Size` specificare `Color` un valore per e `Size` `Color` nell'esempio precedente`int` perché i tipi di e sono tipi primitivi ( e `string`), supportati dal serializzatore JSON.

Nell'esempio seguente, un oggetto di classe viene passato al componente:In the following example, a class object is passed to the component:

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

*Condiviso/MyComponent.razor*:

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

*Pages/MyPage.cshtml*:

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

Nell'esempio precedente si `MyComponent` presuppone che il componente si trova nella cartella *Shared* dell'app. `MyClass`è nello spazio dei`{APP ASSEMBLY}`nomi dell'app ( ).

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>configura se il componente:

* Viene eseguito il prerendering nella pagina.
* Viene eseguito il rendering come HTML statico nella pagina o se include le informazioni necessarie per eseguire il bootstrap di un'app Blazor dall'agente utente.

| Modalità di rendering | Descrizione |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Esegue il rendering del componente in Blazor codice HTML statico e include un marcatore per un'app Server. Quando l'agente utente viene avviato, Blazor questo marcatore viene usato per eseguire il bootstrap di un'app. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Esegue il rendering Blazor di un marcatore per un'app Server. L'output del componente non è incluso. Quando l'agente utente viene avviato, Blazor questo marcatore viene usato per eseguire il bootstrap di un'app. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Esegue il rendering del componente in codice HTML statico. |

Mentre le pagine e le viste possono utilizzare i componenti, il contrario non è vero. I componenti non possono utilizzare funzionalità specifiche della visualizzazione e della pagina, ad esempio viste parziali e sezioni. Per usare la logica da una visualizzazione parziale in un componente, eseguire il factorout della logica di visualizzazione parziale in un componente.

Il rendering dei componenti server da una pagina HTML statica non è supportato.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
