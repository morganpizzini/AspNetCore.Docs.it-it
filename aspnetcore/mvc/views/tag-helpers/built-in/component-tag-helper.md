---
title: Helper tag di componente in ASP.NET Core
author: guardrex
ms.author: riande
description: "Informazioni su come usare l'helper Tag componente ASP.NET Core per eseguire il rendering dei Razor componenti in pagine e visualizzazioni."
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 761c125e3c5f94157cf7bf4524374db2545610b1
ms.sourcegitcommit: 98f92d766d4f343d7e717b542c1b08da29e789c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595454"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="872e5-103">Helper tag di componente in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="872e5-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="872e5-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="872e5-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="872e5-105">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="872e5-105">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="872e5-106">Seguire le istruzioni nella sezione di *configurazione* per:</span><span class="sxs-lookup"><span data-stu-id="872e5-106">Follow the guidance in the *Configuration* section for either:</span></span>

* [Blazor WebAssembly](xref:blazor/components/prerendering-and-integration?pivots=webassembly)
* [Blazor Server](xref:blazor/components/prerendering-and-integration?pivots=server)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="872e5-107">Seguire le istruzioni riportate nella sezione *configurazione* dell' <xref:blazor/components/prerendering-and-integration?pivots=server> articolo.</span><span class="sxs-lookup"><span data-stu-id="872e5-107">Follow the guidance in the *Configuration* section of the <xref:blazor/components/prerendering-and-integration?pivots=server> article.</span></span>

::: moniker-end

## <a name="component-tag-helper"></a><span data-ttu-id="872e5-108">Helper Tag componente</span><span class="sxs-lookup"><span data-stu-id="872e5-108">Component Tag Helper</span></span>

<span data-ttu-id="872e5-109">Per eseguire il rendering di un componente da una pagina o da una vista, usare l' [Helper Tag Component](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) ( `<component>` tag).</span><span class="sxs-lookup"><span data-stu-id="872e5-109">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) (`<component>` tag).</span></span>

> [!NOTE]
> <span data-ttu-id="872e5-110">L'integrazione di Razor componenti in Razor pagine e app MVC in *un' Blazor WebAssembly app ospitata* è supportata in ASP.NET Core in .NET 5,0 o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="872e5-110">Integrating Razor components into Razor Pages and MVC apps in a *hosted Blazor WebAssembly app* is supported in ASP.NET Core in .NET 5.0 or later.</span></span>

<span data-ttu-id="872e5-111"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> Configura se il componente:</span><span class="sxs-lookup"><span data-stu-id="872e5-111"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="872e5-112">Viene preeseguito nella pagina.</span><span class="sxs-lookup"><span data-stu-id="872e5-112">Is prerendered into the page.</span></span>
* <span data-ttu-id="872e5-113">Viene sottoposto a rendering come HTML statico nella pagina o se include le informazioni necessarie per il bootstrap di un' Blazor app dall'agente utente.</span><span class="sxs-lookup"><span data-stu-id="872e5-113">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="872e5-114">Blazor WebAssembly nella tabella seguente sono illustrate le modalità di rendering dell'app.</span><span class="sxs-lookup"><span data-stu-id="872e5-114">Blazor WebAssembly app render modes are shown in the following table.</span></span>

| <span data-ttu-id="872e5-115">Modalità di rendering</span><span class="sxs-lookup"><span data-stu-id="872e5-115">Render Mode</span></span> | <span data-ttu-id="872e5-116">Descrizione</span><span class="sxs-lookup"><span data-stu-id="872e5-116">Description</span></span> |
| ----------- | ----------- |
| `WebAssembly` | <span data-ttu-id="872e5-117">Esegue il rendering di un marcatore per un' Blazor WebAssembly app da usare per includere un componente interattivo quando viene caricato nel browser.</span><span class="sxs-lookup"><span data-stu-id="872e5-117">Renders a marker for a Blazor WebAssembly app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="872e5-118">Il componente non è preeseguito.</span><span class="sxs-lookup"><span data-stu-id="872e5-118">The component isn't prerendered.</span></span> <span data-ttu-id="872e5-119">Questa opzione rende più semplice il rendering di Blazor WebAssembly componenti diversi in pagine diverse.</span><span class="sxs-lookup"><span data-stu-id="872e5-119">This option makes it easier to render different Blazor WebAssembly components on different pages.</span></span> |
| `WebAssemblyPrerendered` | <span data-ttu-id="872e5-120">Esegue il rendering del componente in HTML statico e include un marcatore per un'app da usare in un Blazor WebAssembly secondo momento per rendere il componente interattivo quando viene caricato nel browser.</span><span class="sxs-lookup"><span data-stu-id="872e5-120">Prerenders the component into static HTML and includes a marker for a Blazor WebAssembly app for later use to make the component interactive when loaded in the browser.</span></span> |

<span data-ttu-id="872e5-121">Blazor Server nella tabella seguente sono illustrate le modalità di rendering dell'app.</span><span class="sxs-lookup"><span data-stu-id="872e5-121">Blazor Server app render modes are shown in the following table.</span></span>

| <span data-ttu-id="872e5-122">Modalità di rendering</span><span class="sxs-lookup"><span data-stu-id="872e5-122">Render Mode</span></span> | <span data-ttu-id="872e5-123">Descrizione</span><span class="sxs-lookup"><span data-stu-id="872e5-123">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="872e5-124">Esegue il rendering del componente in HTML statico e include un marcatore per un' Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="872e5-124">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="872e5-125">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="872e5-125">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="872e5-126">Esegue il rendering di un marcatore per un' Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="872e5-126">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="872e5-127">L'output del componente non è incluso.</span><span class="sxs-lookup"><span data-stu-id="872e5-127">Output from the component isn't included.</span></span> <span data-ttu-id="872e5-128">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="872e5-128">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="872e5-129">Esegue il rendering del componente in HTML statico.</span><span class="sxs-lookup"><span data-stu-id="872e5-129">Renders the component into static HTML.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="872e5-130">Blazor Server nella tabella seguente sono illustrate le modalità di rendering dell'app.</span><span class="sxs-lookup"><span data-stu-id="872e5-130">Blazor Server app render modes are shown in the following table.</span></span>

| <span data-ttu-id="872e5-131">Modalità di rendering</span><span class="sxs-lookup"><span data-stu-id="872e5-131">Render Mode</span></span> | <span data-ttu-id="872e5-132">Descrizione</span><span class="sxs-lookup"><span data-stu-id="872e5-132">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="872e5-133">Esegue il rendering del componente in HTML statico e include un marcatore per un' Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="872e5-133">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="872e5-134">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="872e5-134">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="872e5-135">Esegue il rendering di un marcatore per un' Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="872e5-135">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="872e5-136">L'output del componente non è incluso.</span><span class="sxs-lookup"><span data-stu-id="872e5-136">Output from the component isn't included.</span></span> <span data-ttu-id="872e5-137">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="872e5-137">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="872e5-138">Esegue il rendering del componente in HTML statico.</span><span class="sxs-lookup"><span data-stu-id="872e5-138">Renders the component into static HTML.</span></span> |

::: moniker-end

<span data-ttu-id="872e5-139">Le caratteristiche aggiuntive includono:</span><span class="sxs-lookup"><span data-stu-id="872e5-139">Additional characteristics include:</span></span>

* <span data-ttu-id="872e5-140">Sono consentiti più helper tag di componenti che rendono più Razor componenti.</span><span class="sxs-lookup"><span data-stu-id="872e5-140">Multiple Component Tag Helpers rendering multiple Razor components is allowed.</span></span>
* <span data-ttu-id="872e5-141">Non è possibile eseguire il rendering dinamico dei componenti dopo l'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="872e5-141">Components can't be dynamically rendered after the app has started.</span></span>
* <span data-ttu-id="872e5-142">Mentre le pagine e le visualizzazioni possono usare i componenti, il contrario non è vero.</span><span class="sxs-lookup"><span data-stu-id="872e5-142">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="872e5-143">I componenti non possono utilizzare funzionalità specifiche di visualizzazione e pagina, ad esempio visualizzazioni parziali e sezioni.</span><span class="sxs-lookup"><span data-stu-id="872e5-143">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="872e5-144">Per usare la logica da una visualizzazione parziale in un componente, scomporre la logica di visualizzazione parziale in un componente.</span><span class="sxs-lookup"><span data-stu-id="872e5-144">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>
* <span data-ttu-id="872e5-145">Il rendering dei componenti server da una pagina HTML statica non è supportato.</span><span class="sxs-lookup"><span data-stu-id="872e5-145">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="872e5-146">L'helper tag di componente seguente esegue il rendering del `Counter` componente in una pagina o in una vista di un' Blazor Server app con `ServerPrerendered` :</span><span class="sxs-lookup"><span data-stu-id="872e5-146">The following Component Tag Helper renders the `Counter` component in a page or view in a Blazor Server app with `ServerPrerendered`:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="872e5-147">Nell'esempio precedente si presuppone che il `Counter` componente si trovi nella cartella *pages* dell'app.</span><span class="sxs-lookup"><span data-stu-id="872e5-147">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="872e5-148">Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app, ad esempio `@using BlazorSample.Pages` o `@using BlazorSample.Client.Pages` in una Blazor soluzione ospitata.</span><span class="sxs-lookup"><span data-stu-id="872e5-148">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Pages` or `@using BlazorSample.Client.Pages` in a hosted Blazor solution).</span></span>

<span data-ttu-id="872e5-149">L'helper Tag Component può anche passare parametri ai componenti.</span><span class="sxs-lookup"><span data-stu-id="872e5-149">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="872e5-150">Si consideri il `ColorfulCheckbox` componente seguente che imposta il colore e le dimensioni dell'etichetta della casella di controllo:</span><span class="sxs-lookup"><span data-stu-id="872e5-150">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="872e5-151">I `Size` `int` parametri del componente () e `Color` ( `string` ) possono essere impostati dall'helper tag dei componenti: [component parameters](xref:blazor/components/index#component-parameters)</span><span class="sxs-lookup"><span data-stu-id="872e5-151">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="872e5-152">Nell'esempio precedente si presuppone che il `ColorfulCheckbox` componente si trovi nella cartella *condivisa* dell'app.</span><span class="sxs-lookup"><span data-stu-id="872e5-152">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="872e5-153">Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `@using BlazorSample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="872e5-153">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Shared`).</span></span>

<span data-ttu-id="872e5-154">Viene eseguito il rendering del codice HTML seguente nella pagina o nella visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="872e5-154">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="872e5-155">Il passaggio di una stringa racchiusa tra virgolette richiede un' [ Razor espressione esplicita](xref:mvc/views/razor#explicit-razor-expressions), come illustrato `param-Color` nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="872e5-155">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="872e5-156">Il Razor comportamento di analisi per un `string` valore di tipo non si applica a un `param-*` attributo perché l'attributo è di `object` tipo.</span><span class="sxs-lookup"><span data-stu-id="872e5-156">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="872e5-157">Sono supportati tutti i tipi di parametri, tranne:</span><span class="sxs-lookup"><span data-stu-id="872e5-157">All types of parameters are supported, except:</span></span>

* <span data-ttu-id="872e5-158">Parametri generici.</span><span class="sxs-lookup"><span data-stu-id="872e5-158">Generic parameters.</span></span>
* <span data-ttu-id="872e5-159">Parametri non serializzabili.</span><span class="sxs-lookup"><span data-stu-id="872e5-159">Non-serializable parameters.</span></span>
* <span data-ttu-id="872e5-160">Ereditarietà nei parametri della raccolta.</span><span class="sxs-lookup"><span data-stu-id="872e5-160">Inheritance in collection parameters.</span></span>
* <span data-ttu-id="872e5-161">Parametri il cui tipo è definito all'esterno dell' Blazor WebAssembly app o all'interno di un assembly caricato in modo differito.</span><span class="sxs-lookup"><span data-stu-id="872e5-161">Parameters whose type is defined outside of the Blazor WebAssembly app or within a lazily-loaded assembly.</span></span>

<span data-ttu-id="872e5-162">Il tipo di parametro deve essere serializzabile JSON, che in genere significa che il tipo deve avere un costruttore predefinito e le proprietà impostabili.</span><span class="sxs-lookup"><span data-stu-id="872e5-162">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="872e5-163">Ad esempio, è possibile specificare un valore per `Size` e `Color` nell'esempio precedente perché i tipi di `Size` e `Color` sono tipi primitivi ( `int` e `string` ), che sono supportati dal serializzatore JSON.</span><span class="sxs-lookup"><span data-stu-id="872e5-163">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="872e5-164">Nell'esempio seguente viene passato un oggetto classe al componente:</span><span class="sxs-lookup"><span data-stu-id="872e5-164">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="872e5-165">*MyClass.cs* :</span><span class="sxs-lookup"><span data-stu-id="872e5-165">*MyClass.cs* :</span></span>

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

<span data-ttu-id="872e5-166">**La classe deve avere un costruttore pubblico senza parametri.**</span><span class="sxs-lookup"><span data-stu-id="872e5-166">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="872e5-167">*Shared/componente. Razor* :</span><span class="sxs-lookup"><span data-stu-id="872e5-167">*Shared/MyComponent.razor* :</span></span>

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

<span data-ttu-id="872e5-168">*Pagine/pagina. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="872e5-168">*Pages/MyPage.cshtml* :</span></span>

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

<span data-ttu-id="872e5-169">Nell'esempio precedente si presuppone che il `MyComponent` componente si trovi nella cartella *condivisa* dell'app.</span><span class="sxs-lookup"><span data-stu-id="872e5-169">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="872e5-170">Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app, ad esempio `@using BlazorSample` e `@using BlazorSample.Shared` .</span><span class="sxs-lookup"><span data-stu-id="872e5-170">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample` and `@using BlazorSample.Shared`).</span></span> <span data-ttu-id="872e5-171">`MyClass` si trova nello spazio dei nomi dell'app.</span><span class="sxs-lookup"><span data-stu-id="872e5-171">`MyClass` is in the app's namespace.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="872e5-172">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="872e5-172">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
