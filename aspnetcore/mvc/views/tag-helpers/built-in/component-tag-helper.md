---
title: Helper tag componente in ASP.NET CoreComponent Tag Helper in ASP.NET Core
author: guardrex
ms.author: riande
description: Informazioni su come utilizzare ASP.NET Core Component Tag Helper per eseguire il rendering dei componenti Razor nelle pagine e nelle viste.
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: aaa4b92a8912b4f52d861ed07432aa7cf3ca5240
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440961"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="0213a-103">Helper tag componente in ASP.NET CoreComponent Tag Helper in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0213a-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="0213a-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0213a-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0213a-105">Per eseguire il rendering di un componente da una pagina o da una vista, utilizzate [l'helper tag componente](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span><span class="sxs-lookup"><span data-stu-id="0213a-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0213a-106">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="0213a-106">Prerequisites</span></span>

<span data-ttu-id="0213a-107">Seguire le istruzioni nella sezione *Preparare l'app per l'uso* dei componenti nelle pagine e nelle visualizzazioni dell'articolo. <xref:blazor/integrate-components#prepare-the-app></span><span class="sxs-lookup"><span data-stu-id="0213a-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/integrate-components#prepare-the-app> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="0213a-108">Helper tag componente</span><span class="sxs-lookup"><span data-stu-id="0213a-108">Component Tag Helper</span></span>

<span data-ttu-id="0213a-109">Il seguente Component Tag `Counter` Helper esegue il rendering del componente in una pagina o in una vista:</span><span class="sxs-lookup"><span data-stu-id="0213a-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="0213a-110">L'esempio precedente presuppone `Counter` che il componente si trova nella cartella *Pages* dell'app.</span><span class="sxs-lookup"><span data-stu-id="0213a-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span>

<span data-ttu-id="0213a-111">L'helper tag componente può anche passare parametri ai componenti.</span><span class="sxs-lookup"><span data-stu-id="0213a-111">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="0213a-112">Si consideri il componente seguente `ColorfulCheckbox` che imposta il colore e le dimensioni dell'etichetta della casella di controllo:</span><span class="sxs-lookup"><span data-stu-id="0213a-112">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="0213a-113">I `Size` `int`parametri `Color` del`string` [componente](xref:blazor/components#component-parameters) ( ) e ( ) possono essere impostati dall'helper tag componente:</span><span class="sxs-lookup"><span data-stu-id="0213a-113">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="0213a-114">Nell'esempio precedente si `ColorfulCheckbox` presuppone che il componente si trova nella cartella *Shared* dell'app.</span><span class="sxs-lookup"><span data-stu-id="0213a-114">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span>

<span data-ttu-id="0213a-115">Nella pagina o nella visualizzazione viene eseguito il rendering del codice HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="0213a-115">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="0213a-116">Il passaggio di una stringa tra virgolette `param-Color` richiede [un'espressione Razor esplicita,](xref:mvc/views/razor#explicit-razor-expressions)come illustrato nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="0213a-116">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="0213a-117">Il comportamento di analisi Razor per un `string` valore `param-*` di tipo non `object` si applica a un attributo perché l'attributo è un tipo.</span><span class="sxs-lookup"><span data-stu-id="0213a-117">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="0213a-118">Il tipo di parametro deve essere serializzabile IN JSON, il che in genere significa che il tipo deve avere un costruttore predefinito e proprietà impostabili.</span><span class="sxs-lookup"><span data-stu-id="0213a-118">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="0213a-119">Ad esempio, è possibile `Size` specificare `Color` un valore per e `Size` `Color` nell'esempio precedente`int` perché i tipi di e sono tipi primitivi ( e `string`), supportati dal serializzatore JSON.</span><span class="sxs-lookup"><span data-stu-id="0213a-119">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="0213a-120">Nell'esempio seguente, un oggetto di classe viene passato al componente:In the following example, a class object is passed to the component:</span><span class="sxs-lookup"><span data-stu-id="0213a-120">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="0213a-121">*MyClass.cs*:</span><span class="sxs-lookup"><span data-stu-id="0213a-121">*MyClass.cs*:</span></span>

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

<span data-ttu-id="0213a-122">**La classe deve avere un costruttore pubblico senza parametri.**</span><span class="sxs-lookup"><span data-stu-id="0213a-122">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="0213a-123">*Condiviso/MyComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="0213a-123">*Shared/MyComponent.razor*:</span></span>

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

<span data-ttu-id="0213a-124">*Pages/MyPage.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0213a-124">*Pages/MyPage.cshtml*:</span></span>

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

<span data-ttu-id="0213a-125">Nell'esempio precedente si `MyComponent` presuppone che il componente si trova nella cartella *Shared* dell'app.</span><span class="sxs-lookup"><span data-stu-id="0213a-125">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="0213a-126">`MyClass`è nello spazio dei`{APP ASSEMBLY}`nomi dell'app ( ).</span><span class="sxs-lookup"><span data-stu-id="0213a-126">`MyClass` is in the app's namespace (`{APP ASSEMBLY}`).</span></span>

<span data-ttu-id="0213a-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>configura se il componente:</span><span class="sxs-lookup"><span data-stu-id="0213a-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="0213a-128">Viene eseguito il prerendering nella pagina.</span><span class="sxs-lookup"><span data-stu-id="0213a-128">Is prerendered into the page.</span></span>
* <span data-ttu-id="0213a-129">Viene eseguito il rendering come HTML statico nella pagina o se include le informazioni necessarie per eseguire il bootstrap di un'app Blazor dall'agente utente.</span><span class="sxs-lookup"><span data-stu-id="0213a-129">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="0213a-130">Modalità di rendering</span><span class="sxs-lookup"><span data-stu-id="0213a-130">Render Mode</span></span> | <span data-ttu-id="0213a-131">Descrizione</span><span class="sxs-lookup"><span data-stu-id="0213a-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="0213a-132">Esegue il rendering del componente in Blazor codice HTML statico e include un marcatore per un'app Server.</span><span class="sxs-lookup"><span data-stu-id="0213a-132">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="0213a-133">Quando l'agente utente viene avviato, Blazor questo marcatore viene usato per eseguire il bootstrap di un'app.</span><span class="sxs-lookup"><span data-stu-id="0213a-133">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="0213a-134">Esegue il rendering Blazor di un marcatore per un'app Server.</span><span class="sxs-lookup"><span data-stu-id="0213a-134">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="0213a-135">L'output del componente non è incluso.</span><span class="sxs-lookup"><span data-stu-id="0213a-135">Output from the component isn't included.</span></span> <span data-ttu-id="0213a-136">Quando l'agente utente viene avviato, Blazor questo marcatore viene usato per eseguire il bootstrap di un'app.</span><span class="sxs-lookup"><span data-stu-id="0213a-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="0213a-137">Esegue il rendering del componente in codice HTML statico.</span><span class="sxs-lookup"><span data-stu-id="0213a-137">Renders the component into static HTML.</span></span> |

<span data-ttu-id="0213a-138">Mentre le pagine e le viste possono utilizzare i componenti, il contrario non è vero.</span><span class="sxs-lookup"><span data-stu-id="0213a-138">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="0213a-139">I componenti non possono utilizzare funzionalità specifiche della visualizzazione e della pagina, ad esempio viste parziali e sezioni.</span><span class="sxs-lookup"><span data-stu-id="0213a-139">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="0213a-140">Per usare la logica da una visualizzazione parziale in un componente, eseguire il factorout della logica di visualizzazione parziale in un componente.</span><span class="sxs-lookup"><span data-stu-id="0213a-140">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="0213a-141">Il rendering dei componenti server da una pagina HTML statica non è supportato.</span><span class="sxs-lookup"><span data-stu-id="0213a-141">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0213a-142">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="0213a-142">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
