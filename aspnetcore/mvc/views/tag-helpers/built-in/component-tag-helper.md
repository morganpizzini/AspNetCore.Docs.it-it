---
title: Helper tag di componente in ASP.NET Core
author: guardrex
ms.author: riande
description: Informazioni su come usare l'helper Tag componente ASP.NET Core per eseguire il Razor rendering dei componenti in pagine e visualizzazioni.
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 4e003e5ed5e7863d8a218c0f02bb37e214e31910
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773929"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="c244e-103">Helper tag di componente in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c244e-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="c244e-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c244e-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c244e-105">Per eseguire il rendering di un componente da una pagina o da una vista, usare l' [Helper Tag Component](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span><span class="sxs-lookup"><span data-stu-id="c244e-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c244e-106">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="c244e-106">Prerequisites</span></span>

<span data-ttu-id="c244e-107">Seguire le istruzioni riportate nella sezione *preparare l'app per l'uso dei componenti in pagine e viste* dell' <xref:blazor/integrate-components#prepare-the-app> articolo.</span><span class="sxs-lookup"><span data-stu-id="c244e-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/integrate-components#prepare-the-app> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="c244e-108">Helper Tag componente</span><span class="sxs-lookup"><span data-stu-id="c244e-108">Component Tag Helper</span></span>

<span data-ttu-id="c244e-109">L'helper tag di componente seguente esegue il rendering `Counter` del componente in una pagina o in una visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="c244e-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="c244e-110">Nell'esempio precedente si presuppone che `Counter` il componente si trovi nella cartella *pages* dell'app.</span><span class="sxs-lookup"><span data-stu-id="c244e-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span>

<span data-ttu-id="c244e-111">L'helper Tag Component può anche passare parametri ai componenti.</span><span class="sxs-lookup"><span data-stu-id="c244e-111">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="c244e-112">Si consideri il componente seguente `ColorfulCheckbox` che imposta il colore e le dimensioni dell'etichetta della casella di controllo:</span><span class="sxs-lookup"><span data-stu-id="c244e-112">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="c244e-113">I `Size` parametri`int`del `Color` [componente](xref:blazor/components#component-parameters) (`string`) e () possono essere impostati dall'helper tag dei componenti:</span><span class="sxs-lookup"><span data-stu-id="c244e-113">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="c244e-114">Nell'esempio precedente si presuppone che `ColorfulCheckbox` il componente si trovi nella cartella *condivisa* dell'app.</span><span class="sxs-lookup"><span data-stu-id="c244e-114">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span>

<span data-ttu-id="c244e-115">Viene eseguito il rendering del codice HTML seguente nella pagina o nella visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="c244e-115">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="c244e-116">Il passaggio di una stringa racchiusa tra virgolette richiede un' [espressione Razor esplicita](xref:mvc/views/razor#explicit-razor-expressions), come illustrato `param-Color` nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="c244e-116">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="c244e-117">Il comportamento di analisi Razor per un `string` valore di tipo non è applicabile `param-*` a un attributo perché l'attributo `object` è di tipo.</span><span class="sxs-lookup"><span data-stu-id="c244e-117">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="c244e-118">Il tipo di parametro deve essere serializzabile JSON, che in genere significa che il tipo deve avere un costruttore predefinito e le proprietà impostabili.</span><span class="sxs-lookup"><span data-stu-id="c244e-118">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="c244e-119">Ad esempio, è possibile specificare un valore per `Size` e `Color` nell'esempio precedente perché i tipi `Size` di e `Color` sono tipi primitivi (`int` e `string`), che sono supportati dal serializzatore JSON.</span><span class="sxs-lookup"><span data-stu-id="c244e-119">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="c244e-120">Nell'esempio seguente viene passato un oggetto classe al componente:</span><span class="sxs-lookup"><span data-stu-id="c244e-120">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="c244e-121">*MyClass.cs*:</span><span class="sxs-lookup"><span data-stu-id="c244e-121">*MyClass.cs*:</span></span>

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

<span data-ttu-id="c244e-122">**La classe deve avere un costruttore pubblico senza parametri.**</span><span class="sxs-lookup"><span data-stu-id="c244e-122">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="c244e-123">*Shared/componente. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c244e-123">*Shared/MyComponent.razor*:</span></span>

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

<span data-ttu-id="c244e-124">*Pagine/pagina. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c244e-124">*Pages/MyPage.cshtml*:</span></span>

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

<span data-ttu-id="c244e-125">Nell'esempio precedente si presuppone che `MyComponent` il componente si trovi nella cartella *condivisa* dell'app.</span><span class="sxs-lookup"><span data-stu-id="c244e-125">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="c244e-126">`MyClass`si trova nello spazio dei nomi dell'`{APP ASSEMBLY}`app ().</span><span class="sxs-lookup"><span data-stu-id="c244e-126">`MyClass` is in the app's namespace (`{APP ASSEMBLY}`).</span></span>

<span data-ttu-id="c244e-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>Configura se il componente:</span><span class="sxs-lookup"><span data-stu-id="c244e-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="c244e-128">Viene preeseguito nella pagina.</span><span class="sxs-lookup"><span data-stu-id="c244e-128">Is prerendered into the page.</span></span>
* <span data-ttu-id="c244e-129">Viene visualizzato come HTML statico nella pagina o se include le informazioni necessarie per eseguire il bootstrap di un'app Blazer dall'agente utente.</span><span class="sxs-lookup"><span data-stu-id="c244e-129">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="c244e-130">Modalità di rendering</span><span class="sxs-lookup"><span data-stu-id="c244e-130">Render Mode</span></span> | <span data-ttu-id="c244e-131">Description</span><span class="sxs-lookup"><span data-stu-id="c244e-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="c244e-132">Esegue il rendering del componente in HTML statico e include un marcatore Blazor per un'app Server.</span><span class="sxs-lookup"><span data-stu-id="c244e-132">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="c244e-133">Quando l'agente utente viene avviato, questo marcatore viene usato per il Blazor bootstrap di un'app.</span><span class="sxs-lookup"><span data-stu-id="c244e-133">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="c244e-134">Esegue il rendering di un marcatore per un' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="c244e-134">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="c244e-135">L'output del componente non è incluso.</span><span class="sxs-lookup"><span data-stu-id="c244e-135">Output from the component isn't included.</span></span> <span data-ttu-id="c244e-136">Quando l'agente utente viene avviato, questo marcatore viene usato per il Blazor bootstrap di un'app.</span><span class="sxs-lookup"><span data-stu-id="c244e-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="c244e-137">Esegue il rendering del componente in HTML statico.</span><span class="sxs-lookup"><span data-stu-id="c244e-137">Renders the component into static HTML.</span></span> |

<span data-ttu-id="c244e-138">Mentre le pagine e le visualizzazioni possono usare i componenti, il contrario non è vero.</span><span class="sxs-lookup"><span data-stu-id="c244e-138">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="c244e-139">I componenti non possono utilizzare funzionalità specifiche di visualizzazione e pagina, ad esempio visualizzazioni parziali e sezioni.</span><span class="sxs-lookup"><span data-stu-id="c244e-139">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="c244e-140">Per usare la logica da una visualizzazione parziale in un componente, scomporre la logica di visualizzazione parziale in un componente.</span><span class="sxs-lookup"><span data-stu-id="c244e-140">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="c244e-141">Il rendering dei componenti server da una pagina HTML statica non è supportato.</span><span class="sxs-lookup"><span data-stu-id="c244e-141">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c244e-142">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="c244e-142">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
