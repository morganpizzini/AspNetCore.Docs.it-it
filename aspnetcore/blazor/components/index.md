---
title: Creazione e utilizzo di Razor componenti ASP.NET Core
author: guardrex
description: Informazioni su come creare e usare Razor i componenti, tra cui la modalità di associazione ai dati, la gestione degli eventi e la gestione dei cicli di vita dei componenti.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/14/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/index
ms.openlocfilehash: a5401e1b423740aadf6f8de84763b5c05d451cd0
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445333"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="25054-103">Creazione e utilizzo di Razor componenti ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="25054-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="25054-104">Di [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)e [Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="25054-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="25054-105">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="25054-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="25054-106">le app vengono compilate usando i *componenti*.</span><span class="sxs-lookup"><span data-stu-id="25054-106"> apps are built using *components*.</span></span> <span data-ttu-id="25054-107">Un componente è un blocco di interfaccia utente (UI) autonomo, ad esempio una pagina, una finestra di dialogo o un form.</span><span class="sxs-lookup"><span data-stu-id="25054-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="25054-108">Un componente include il markup HTML e la logica di elaborazione necessaria per inserire i dati o rispondere agli eventi dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="25054-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="25054-109">I componenti sono flessibili e leggeri.</span><span class="sxs-lookup"><span data-stu-id="25054-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="25054-110">Possono essere annidati, riutilizzati e condivisi tra i progetti.</span><span class="sxs-lookup"><span data-stu-id="25054-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="25054-111">Classi di componenti</span><span class="sxs-lookup"><span data-stu-id="25054-111">Component classes</span></span>

<span data-ttu-id="25054-112">I componenti sono implementati in [Razor](xref:mvc/views/razor) file componente ( `.razor` ) utilizzando una combinazione di markup C# e HTML.</span><span class="sxs-lookup"><span data-stu-id="25054-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (`.razor`) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="25054-113">Un componente in Blazor viene definito formalmente come \* Razor componente\*.</span><span class="sxs-lookup"><span data-stu-id="25054-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

### <a name="razor-syntax"></a><span data-ttu-id="25054-114">Sintassi Razor</span><span class="sxs-lookup"><span data-stu-id="25054-114">Razor syntax</span></span>

Razor<span data-ttu-id="25054-115">i componenti nelle Blazor app utilizzano ampiamente la Razor sintassi.</span><span class="sxs-lookup"><span data-stu-id="25054-115"> components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="25054-116">Se non si ha familiarità con il Razor linguaggio di markup, è consigliabile leggere <xref:mvc/views/razor> prima di procedere.</span><span class="sxs-lookup"><span data-stu-id="25054-116">If you aren't familiar with the Razor markup language, we recommend reading <xref:mvc/views/razor> before proceeding.</span></span>

<span data-ttu-id="25054-117">Quando si accede al contenuto sulla Razor sintassi, prestare particolare attenzione alle sezioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="25054-117">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="25054-118">[Direttive](xref:mvc/views/razor#directives): `@` -parole chiave riservate con prefisso che in genere modificano il modo in cui il markup del componente viene analizzato o funzione.</span><span class="sxs-lookup"><span data-stu-id="25054-118">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="25054-119">[Attributi di direttiva](xref:mvc/views/razor#directive-attributes): `@` parole chiave riservate con prefisso che in genere modificano il modo in cui gli elementi componente vengono analizzati o funzionano.</span><span class="sxs-lookup"><span data-stu-id="25054-119">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

### <a name="names"></a><span data-ttu-id="25054-120">Nomi</span><span class="sxs-lookup"><span data-stu-id="25054-120">Names</span></span>

<span data-ttu-id="25054-121">Il nome di un componente deve iniziare con un carattere maiuscolo.</span><span class="sxs-lookup"><span data-stu-id="25054-121">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="25054-122">Ad esempio, `MyCoolComponent.razor` è valido e `myCoolComponent.razor` non è valido.</span><span class="sxs-lookup"><span data-stu-id="25054-122">For example, `MyCoolComponent.razor` is valid, and `myCoolComponent.razor` is invalid.</span></span>

### <a name="routing"></a><span data-ttu-id="25054-123">Routing</span><span class="sxs-lookup"><span data-stu-id="25054-123">Routing</span></span>

<span data-ttu-id="25054-124">Il routing in Blazor viene effettuato fornendo un modello di route a ogni componente accessibile nell'app.</span><span class="sxs-lookup"><span data-stu-id="25054-124">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span> <span data-ttu-id="25054-125">Quando Razor viene compilato un file con una [`@page`][9] direttiva, alla classe generata viene assegnato un oggetto che <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifica il modello di route.</span><span class="sxs-lookup"><span data-stu-id="25054-125">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="25054-126">In fase di esecuzione, il router cerca le classi di componenti con un oggetto <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> ed esegue il rendering di qualsiasi componente con un modello di route corrispondente all'URL richiesto.</span><span class="sxs-lookup"><span data-stu-id="25054-126">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span> <span data-ttu-id="25054-127">Per altre informazioni, vedere <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="25054-127">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a><span data-ttu-id="25054-128">markup</span><span class="sxs-lookup"><span data-stu-id="25054-128">Markup</span></span>

<span data-ttu-id="25054-129">L'interfaccia utente per un componente viene definita utilizzando HTML.</span><span class="sxs-lookup"><span data-stu-id="25054-129">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="25054-130">La logica di rendering dinamica (ad esempio, cicli, condizionali, espressioni) viene aggiunta usando una sintassi C# incorporata denominata *Razor* .</span><span class="sxs-lookup"><span data-stu-id="25054-130">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="25054-131">Quando viene compilata un'app, il markup HTML e la logica di rendering C# vengono convertiti in una classe Component.</span><span class="sxs-lookup"><span data-stu-id="25054-131">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="25054-132">Il nome della classe generata corrisponde al nome del file.</span><span class="sxs-lookup"><span data-stu-id="25054-132">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="25054-133">I membri della classe Component sono definiti in un [`@code`][1] blocco.</span><span class="sxs-lookup"><span data-stu-id="25054-133">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="25054-134">Nel [`@code`][1] blocco, lo stato del componente (proprietà, campi) viene specificato con i metodi per la gestione degli eventi o per la definizione di altre logiche dei componenti.</span><span class="sxs-lookup"><span data-stu-id="25054-134">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="25054-135">È consentito più di un [`@code`][1] blocco.</span><span class="sxs-lookup"><span data-stu-id="25054-135">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="25054-136">I membri dei componenti possono essere usati come parte della logica di rendering del componente usando espressioni C# che iniziano con `@` .</span><span class="sxs-lookup"><span data-stu-id="25054-136">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="25054-137">Ad esempio, viene eseguito il rendering di un campo C# anteponendo `@` il nome del campo.</span><span class="sxs-lookup"><span data-stu-id="25054-137">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="25054-138">Nell'esempio seguente viene valutato ed eseguito il rendering:</span><span class="sxs-lookup"><span data-stu-id="25054-138">The following example evaluates and renders:</span></span>

* <span data-ttu-id="25054-139">`headingFontStyle`al valore della proprietà CSS per `font-style` .</span><span class="sxs-lookup"><span data-stu-id="25054-139">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="25054-140">`headingText`al contenuto dell' `<h1>` elemento.</span><span class="sxs-lookup"><span data-stu-id="25054-140">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="25054-141">Una volta eseguito il rendering iniziale del componente, il componente rigenera l'albero di rendering in risposta agli eventi.</span><span class="sxs-lookup"><span data-stu-id="25054-141">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="25054-142">Confronta quindi il nuovo albero di rendering con quello precedente e applica le modifiche apportate al Document Object Model (DOM) del browser.</span><span class="sxs-lookup"><span data-stu-id="25054-142"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="25054-143">I componenti sono classi C# ordinarie e possono essere inseriti in qualsiasi punto all'interno di un progetto.</span><span class="sxs-lookup"><span data-stu-id="25054-143">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="25054-144">I componenti che producono pagine Web in genere risiedono nella `Pages` cartella.</span><span class="sxs-lookup"><span data-stu-id="25054-144">Components that produce webpages usually reside in the `Pages` folder.</span></span> <span data-ttu-id="25054-145">I componenti non di pagina vengono spesso inseriti nella `Shared` cartella o in una cartella personalizzata aggiunta al progetto.</span><span class="sxs-lookup"><span data-stu-id="25054-145">Non-page components are frequently placed in the `Shared` folder or a custom folder added to the project.</span></span>

### <a name="namespaces"></a><span data-ttu-id="25054-146">Spazi dei nomi</span><span class="sxs-lookup"><span data-stu-id="25054-146">Namespaces</span></span>

<span data-ttu-id="25054-147">Lo spazio dei nomi di un componente viene in genere derivato dallo spazio dei nomi radice dell'app e dal percorso (cartella) del componente all'interno dell'app.</span><span class="sxs-lookup"><span data-stu-id="25054-147">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="25054-148">Se lo spazio dei nomi radice dell'app è `BlazorSample` e il componente si trova `Counter` nella `Pages` cartella:</span><span class="sxs-lookup"><span data-stu-id="25054-148">If the app's root namespace is `BlazorSample` and the `Counter` component resides in the `Pages` folder:</span></span>

* <span data-ttu-id="25054-149">Lo `Counter` spazio dei nomi del componente è `BlazorSample.Pages` .</span><span class="sxs-lookup"><span data-stu-id="25054-149">The `Counter` component's namespace is `BlazorSample.Pages`.</span></span>
* <span data-ttu-id="25054-150">Il nome completo del tipo del componente è `BlazorSample.Pages.Counter` .</span><span class="sxs-lookup"><span data-stu-id="25054-150">The fully qualified type name of the component is `BlazorSample.Pages.Counter`.</span></span>

<span data-ttu-id="25054-151">Per le cartelle personalizzate che contengono componenti, aggiungere una [`@using`][2] direttiva al componente padre o al file dell'app `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="25054-151">For custom folders that hold components, add a [`@using`][2] directive to the parent component or to the app's `_Imports.razor` file.</span></span> <span data-ttu-id="25054-152">L'esempio seguente rende disponibili i componenti nella `Components` cartella:</span><span class="sxs-lookup"><span data-stu-id="25054-152">The following example makes components in the `Components` folder available:</span></span>

```razor
@using BlazorSample.Components
```

<span data-ttu-id="25054-153">È anche possibile fare riferimento ai componenti usando i relativi nomi completi, che non richiedono la [`@using`][2] direttiva:</span><span class="sxs-lookup"><span data-stu-id="25054-153">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
<BlazorSample.Components.MyComponent />
```

<span data-ttu-id="25054-154">Lo spazio dei nomi di un componente creato con Razor è basato su (in ordine di priorità):</span><span class="sxs-lookup"><span data-stu-id="25054-154">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="25054-155">[`@namespace`][8]designazione nel Razor markup file ( `.razor` ) `@namespace BlazorSample.MyNamespace` .</span><span class="sxs-lookup"><span data-stu-id="25054-155">[`@namespace`][8] designation in Razor file (`.razor`) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="25054-156">Il progetto `RootNamespace` nel file di progetto ( `<RootNamespace>BlazorSample</RootNamespace>` ).</span><span class="sxs-lookup"><span data-stu-id="25054-156">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="25054-157">Il nome del progetto, tratto dal nome file del file di progetto ( `.csproj` ) e il percorso dalla radice del progetto al componente.</span><span class="sxs-lookup"><span data-stu-id="25054-157">The project name, taken from the project file's file name (`.csproj`), and the path from the project root to the component.</span></span> <span data-ttu-id="25054-158">Il Framework, ad esempio, risolve `{PROJECT ROOT}/Pages/Index.razor` ( `BlazorSample.csproj` ) nello spazio dei nomi `BlazorSample.Pages` .</span><span class="sxs-lookup"><span data-stu-id="25054-158">For example, the framework resolves `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="25054-159">I componenti seguono le regole di associazione dei nomi in C#.</span><span class="sxs-lookup"><span data-stu-id="25054-159">Components follow C# name binding rules.</span></span> <span data-ttu-id="25054-160">Per il `Index` componente in questo esempio, i componenti nell'ambito sono tutti componenti:</span><span class="sxs-lookup"><span data-stu-id="25054-160">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="25054-161">Nella stessa cartella `Pages` .</span><span class="sxs-lookup"><span data-stu-id="25054-161">In the same folder, `Pages`.</span></span>
  * <span data-ttu-id="25054-162">Componenti nella radice del progetto che non specificano in modo esplicito uno spazio dei nomi diverso.</span><span class="sxs-lookup"><span data-stu-id="25054-162">The components in the project's root that don't explicitly specify a different namespace.</span></span>

> [!NOTE]
> <span data-ttu-id="25054-163">La `global::` qualificazione non è supportata.</span><span class="sxs-lookup"><span data-stu-id="25054-163">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="25054-164">L'importazione di componenti con [`using`](/dotnet/csharp/language-reference/keywords/using-statement) istruzioni con alias (ad esempio, `@using Foo = Bar` ) non è supportata.</span><span class="sxs-lookup"><span data-stu-id="25054-164">Importing components with aliased [`using`](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="25054-165">I nomi parzialmente qualificati non sono supportati.</span><span class="sxs-lookup"><span data-stu-id="25054-165">Partially qualified names aren't supported.</span></span> <span data-ttu-id="25054-166">Ad esempio, `@using BlazorSample` l'aggiunta e il riferimento al `NavMenu` componente ( `NavMenu.razor` ) con `<Shared.NavMenu></Shared.NavMenu>` non è supportato.</span><span class="sxs-lookup"><span data-stu-id="25054-166">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

### <a name="partial-class-support"></a><span data-ttu-id="25054-167">Supporto di classi parziali</span><span class="sxs-lookup"><span data-stu-id="25054-167">Partial class support</span></span>

Razor<span data-ttu-id="25054-168">i componenti vengono generati come classi parziali.</span><span class="sxs-lookup"><span data-stu-id="25054-168"> components are generated as partial classes.</span></span> Razor<span data-ttu-id="25054-169">i componenti vengono creati utilizzando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="25054-169"> components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="25054-170">Il codice C# è definito in un [`@code`][1] blocco con markup HTML e Razor codice in un unico file.</span><span class="sxs-lookup"><span data-stu-id="25054-170">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="25054-171">i modelli definiscono i Razor componenti usando questo approccio.</span><span class="sxs-lookup"><span data-stu-id="25054-171"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="25054-172">Il codice C# viene inserito in un file code-behind definito come classe parziale.</span><span class="sxs-lookup"><span data-stu-id="25054-172">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="25054-173">Nell'esempio seguente viene illustrato il `Counter` componente predefinito con un [`@code`][1] blocco in un'app generata da un Blazor modello.</span><span class="sxs-lookup"><span data-stu-id="25054-173">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="25054-174">Il markup HTML, Razor il codice e il codice C# si trovano nello stesso file:</span><span class="sxs-lookup"><span data-stu-id="25054-174">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="25054-175">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="25054-175">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="25054-176">Il `Counter` componente può essere creato anche usando un file code-behind con una classe parziale:</span><span class="sxs-lookup"><span data-stu-id="25054-176">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="25054-177">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="25054-177">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="25054-178">`Counter.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="25054-178">`Counter.razor.cs`:</span></span>

```csharp
namespace BlazorSample.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

<span data-ttu-id="25054-179">Aggiungere gli spazi dei nomi necessari al file di classe parziale in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="25054-179">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="25054-180">Gli spazi dei nomi tipici usati dai Razor componenti includono:</span><span class="sxs-lookup"><span data-stu-id="25054-180">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> <span data-ttu-id="25054-181">[`@using`][2]le direttive nel `_Imports.razor` file vengono applicate solo ai Razor file ( `.razor` ) e non ai file C# ( `.cs` ).</span><span class="sxs-lookup"><span data-stu-id="25054-181">[`@using`][2] directives in the `_Imports.razor` file are only applied to Razor files (`.razor`), not C# files (`.cs`).</span></span>

### <a name="specify-a-base-class"></a><span data-ttu-id="25054-182">Specificare una classe di base</span><span class="sxs-lookup"><span data-stu-id="25054-182">Specify a base class</span></span>

<span data-ttu-id="25054-183">La [`@inherits`][6] direttiva può essere usata per specificare una classe di base per un componente.</span><span class="sxs-lookup"><span data-stu-id="25054-183">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="25054-184">Nell'esempio seguente viene illustrato come un componente può ereditare una classe base, `BlazorRocksBase` , per fornire le proprietà e i metodi del componente.</span><span class="sxs-lookup"><span data-stu-id="25054-184">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="25054-185">La classe base deve derivare da <xref:Microsoft.AspNetCore.Components.ComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="25054-185">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="25054-186">`Pages/BlazorRocks.razor`:</span><span class="sxs-lookup"><span data-stu-id="25054-186">`Pages/BlazorRocks.razor`:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="25054-187">`BlazorRocksBase.cs`:</span><span class="sxs-lookup"><span data-stu-id="25054-187">`BlazorRocksBase.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

### <a name="use-components"></a><span data-ttu-id="25054-188">Usare i componenti</span><span class="sxs-lookup"><span data-stu-id="25054-188">Use components</span></span>

<span data-ttu-id="25054-189">I componenti possono includere altri componenti dichiarando questi ultimi usando la sintassi degli elementi HTML.</span><span class="sxs-lookup"><span data-stu-id="25054-189">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="25054-190">Il markup per l'uso di un componente è simile a un tag HTML, in cui il nome del tag è il tipo di componente.</span><span class="sxs-lookup"><span data-stu-id="25054-190">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="25054-191">Il markup seguente in `Pages/Index.razor` esegue il rendering di un' `HeadingComponent` istanza di:</span><span class="sxs-lookup"><span data-stu-id="25054-191">The following markup in `Pages/Index.razor` renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="25054-192">`Components/HeadingComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="25054-192">`Components/HeadingComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

<span data-ttu-id="25054-193">Se un componente contiene un elemento HTML con una prima lettera maiuscola che non corrisponde a un nome di componente, viene emesso un avviso che indica che l'elemento ha un nome imprevisto.</span><span class="sxs-lookup"><span data-stu-id="25054-193">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="25054-194">L'aggiunta di una [`@using`][2] direttiva per lo spazio dei nomi del componente rende disponibile il componente, che risolve l'avviso.</span><span class="sxs-lookup"><span data-stu-id="25054-194">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="parameters"></a><span data-ttu-id="25054-195">Parametri</span><span class="sxs-lookup"><span data-stu-id="25054-195">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="25054-196">Parametri di route</span><span class="sxs-lookup"><span data-stu-id="25054-196">Route parameters</span></span>

<span data-ttu-id="25054-197">I componenti possono ricevere parametri di route dal modello di route fornito nella [`@page`][9] direttiva.</span><span class="sxs-lookup"><span data-stu-id="25054-197">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="25054-198">Il router usa parametri di route per popolare i parametri del componente corrispondente.</span><span class="sxs-lookup"><span data-stu-id="25054-198">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="25054-199">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="25054-199">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="25054-200">I parametri facoltativi non sono supportati, quindi [`@page`][9] vengono applicate due direttive nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="25054-200">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="25054-201">Il primo consente la navigazione al componente senza un parametro.</span><span class="sxs-lookup"><span data-stu-id="25054-201">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="25054-202">La seconda [`@page`][9] direttiva riceve il `{text}` parametro di route e assegna il valore alla `Text` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="25054-202">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="25054-203">La sintassi dei parametri *catch-all* ( `*` / `**` ), che acquisisce il percorso tra più limiti di cartella, **non** è supportata in Razor Components ( `.razor` ).</span><span class="sxs-lookup"><span data-stu-id="25054-203">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (`.razor`).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="25054-204">Parametri del componente</span><span class="sxs-lookup"><span data-stu-id="25054-204">Component parameters</span></span>

<span data-ttu-id="25054-205">I componenti possono avere *parametri del componente*, che vengono definiti usando proprietà pubbliche nella classe Component con l' [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="25054-205">Components can have *component parameters*, which are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="25054-206">Usare gli attributi per specificare gli argomenti per un componente nel markup.</span><span class="sxs-lookup"><span data-stu-id="25054-206">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="25054-207">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="25054-207">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="25054-208">Nell'esempio seguente dall'app di esempio, `ParentComponent` imposta il valore della `Title` proprietà dell'oggetto `ChildComponent` .</span><span class="sxs-lookup"><span data-stu-id="25054-208">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="25054-209">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="25054-209">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="25054-210">Non creare componenti che scrivono nei propri *parametri del componente*, usare invece un campo privato.</span><span class="sxs-lookup"><span data-stu-id="25054-210">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="25054-211">Per ulteriori informazioni, vedere la sezione [non creare componenti che scrivono nella relativa proprietà dei parametri](#dont-create-components-that-write-to-their-own-parameter-properties) .</span><span class="sxs-lookup"><span data-stu-id="25054-211">For more information, see the [Don't create components that write to their own parameter properties](#dont-create-components-that-write-to-their-own-parameter-properties) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="25054-212">Contenuto figlio</span><span class="sxs-lookup"><span data-stu-id="25054-212">Child content</span></span>

<span data-ttu-id="25054-213">I componenti possono impostare il contenuto di un altro componente.</span><span class="sxs-lookup"><span data-stu-id="25054-213">Components can set the content of another component.</span></span> <span data-ttu-id="25054-214">Il componente di assegnazione fornisce il contenuto tra i tag che specificano il componente di destinazione.</span><span class="sxs-lookup"><span data-stu-id="25054-214">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="25054-215">Nell'esempio seguente, `ChildComponent` ha una `ChildContent` proprietà che rappresenta un oggetto <xref:Microsoft.AspNetCore.Components.RenderFragment> , che rappresenta un segmento di interfaccia utente di cui eseguire il rendering.</span><span class="sxs-lookup"><span data-stu-id="25054-215">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="25054-216">Il valore di `ChildContent` viene posizionato nel markup del componente in cui deve essere eseguito il rendering del contenuto.</span><span class="sxs-lookup"><span data-stu-id="25054-216">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="25054-217">Il valore di `ChildContent` viene ricevuto dal componente padre e ne viene eseguito il rendering all'interno del pannello bootstrap `panel-body` .</span><span class="sxs-lookup"><span data-stu-id="25054-217">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="25054-218">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="25054-218">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="25054-219">La proprietà che riceve il <xref:Microsoft.AspNetCore.Components.RenderFragment> contenuto deve essere denominata `ChildContent` per convenzione.</span><span class="sxs-lookup"><span data-stu-id="25054-219">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="25054-220">`ParentComponent`Nell'app di esempio può fornire contenuto per il rendering `ChildComponent` di inserendo il contenuto all'interno dei `<ChildComponent>` tag.</span><span class="sxs-lookup"><span data-stu-id="25054-220">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="25054-221">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="25054-221">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

<span data-ttu-id="25054-222">A causa del modo in cui Blazor viene eseguito il rendering del contenuto figlio, i componenti di rendering all'interno di un `for` ciclo richiedono una variabile di indice locale se la variabile del ciclo di incremento viene utilizzata nel contenuto del componente figlio:</span><span class="sxs-lookup"><span data-stu-id="25054-222">Due to the way that Blazor renders child content, rendering components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the child component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <ChildComponent Param1="@c">
>         Child Content: Count: @current
>     </ChildComponent>
> }
> ```
>
> <span data-ttu-id="25054-223">In alternativa, usare un `foreach` ciclo con <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="25054-223">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Param1="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="25054-224">Attributo splatting e parametri arbitrari</span><span class="sxs-lookup"><span data-stu-id="25054-224">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="25054-225">I componenti possono acquisire ed eseguire il rendering di attributi aggiuntivi oltre ai parametri dichiarati del componente.</span><span class="sxs-lookup"><span data-stu-id="25054-225">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="25054-226">È possibile acquisire attributi aggiuntivi in un dizionario e quindi *Splatted* su un elemento quando il componente viene sottoposto a rendering usando la [`@attributes`][3] Razor direttiva.</span><span class="sxs-lookup"><span data-stu-id="25054-226">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="25054-227">Questo scenario è utile quando si definisce un componente che produce un elemento di markup che supporta un'ampia gamma di personalizzazioni.</span><span class="sxs-lookup"><span data-stu-id="25054-227">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="25054-228">Ad esempio, può essere noioso definire gli attributi separatamente per un `<input>` che supporta molti parametri.</span><span class="sxs-lookup"><span data-stu-id="25054-228">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="25054-229">Nell'esempio seguente, il primo `<input>` elemento ( `id="useIndividualParams"` ) USA parametri dei singoli componenti, mentre il secondo `<input>` elemento ( `id="useAttributesDict"` ) usa l'attributo splatting:</span><span class="sxs-lookup"><span data-stu-id="25054-229">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="25054-230">Il tipo del parametro deve implementare `IEnumerable<KeyValuePair<string, object>>` con chiavi di stringa.</span><span class="sxs-lookup"><span data-stu-id="25054-230">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="25054-231">L'uso `IReadOnlyDictionary<string, object>` di è anche un'opzione in questo scenario.</span><span class="sxs-lookup"><span data-stu-id="25054-231">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="25054-232">Gli elementi sottoposti a rendering `<input>` usando entrambi gli approcci sono identici:</span><span class="sxs-lookup"><span data-stu-id="25054-232">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

<span data-ttu-id="25054-233">Per accettare attributi arbitrari, definire un parametro component usando l' [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attributo con la <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> proprietà impostata su `true` :</span><span class="sxs-lookup"><span data-stu-id="25054-233">To accept arbitrary attributes, define a component parameter using the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="25054-234">La <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> proprietà su [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) consente al parametro di trovare la corrispondenza con tutti gli attributi che non corrispondono ad altri parametri.</span><span class="sxs-lookup"><span data-stu-id="25054-234">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="25054-235">Un componente può definire solo un singolo parametro con <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> .</span><span class="sxs-lookup"><span data-stu-id="25054-235">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="25054-236">Il tipo di proprietà utilizzato con <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> deve essere assegnabile da `Dictionary<string, object>` con chiavi di stringa.</span><span class="sxs-lookup"><span data-stu-id="25054-236">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="25054-237">`IEnumerable<KeyValuePair<string, object>>`o `IReadOnlyDictionary<string, object>` sono anche opzioni in questo scenario.</span><span class="sxs-lookup"><span data-stu-id="25054-237">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="25054-238">La posizione di [`@attributes`][3] relativa alla posizione degli attributi degli elementi è importante.</span><span class="sxs-lookup"><span data-stu-id="25054-238">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="25054-239">Quando [`@attributes`][3] Splatted sull'elemento, gli attributi vengono elaborati da destra a sinistra (da ultimo a primo).</span><span class="sxs-lookup"><span data-stu-id="25054-239">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="25054-240">Si consideri l'esempio seguente di un componente che utilizza un `Child` componente:</span><span class="sxs-lookup"><span data-stu-id="25054-240">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="25054-241">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="25054-241">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="25054-242">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="25054-242">`ChildComponent.razor`:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="25054-243">L' `Child` attributo del componente `extra` viene impostato a destra di [`@attributes`][3] .</span><span class="sxs-lookup"><span data-stu-id="25054-243">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="25054-244">Il `Parent` rendering del componente `<div>` contiene `extra="5"` quando viene passato attraverso l'attributo aggiuntivo perché gli attributi vengono elaborati da destra a sinistra (da ultimo a primo):</span><span class="sxs-lookup"><span data-stu-id="25054-244">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="25054-245">Nell'esempio seguente, l'ordine di `extra` e [`@attributes`][3] viene invertito nell'oggetto del `Child` componente `<div>` :</span><span class="sxs-lookup"><span data-stu-id="25054-245">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="25054-246">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="25054-246">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="25054-247">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="25054-247">`ChildComponent.razor`:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="25054-248">Il rendering `<div>` nel `Parent` componente contiene `extra="10"` quando viene passato tramite l'attributo aggiuntivo:</span><span class="sxs-lookup"><span data-stu-id="25054-248">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="25054-249">Acquisisci riferimenti ai componenti</span><span class="sxs-lookup"><span data-stu-id="25054-249">Capture references to components</span></span>

<span data-ttu-id="25054-250">I riferimenti ai componenti forniscono un modo per fare riferimento a un'istanza del componente in modo da poter emettere comandi per tale istanza, ad esempio `Show` o `Reset` .</span><span class="sxs-lookup"><span data-stu-id="25054-250">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="25054-251">Per acquisire un riferimento a un componente:</span><span class="sxs-lookup"><span data-stu-id="25054-251">To capture a component reference:</span></span>

* <span data-ttu-id="25054-252">Aggiungere un [`@ref`][4] attributo al componente figlio.</span><span class="sxs-lookup"><span data-stu-id="25054-252">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="25054-253">Definire un campo con lo stesso tipo del componente figlio.</span><span class="sxs-lookup"><span data-stu-id="25054-253">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="25054-254">Quando viene eseguito il rendering del componente, il `loginDialog` campo viene popolato con l' `MyLoginDialog` istanza del componente figlio.</span><span class="sxs-lookup"><span data-stu-id="25054-254">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="25054-255">È quindi possibile richiamare i metodi .NET nell'istanza del componente.</span><span class="sxs-lookup"><span data-stu-id="25054-255">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="25054-256">La `loginDialog` variabile viene popolata solo dopo il rendering del componente e l'output include `MyLoginDialog` l'elemento.</span><span class="sxs-lookup"><span data-stu-id="25054-256">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="25054-257">Fino a quando non viene eseguito il rendering del componente, non vi sono riferimenti.</span><span class="sxs-lookup"><span data-stu-id="25054-257">Until the component is rendered, there's nothing to reference.</span></span>
>
> <span data-ttu-id="25054-258">Per modificare i riferimenti ai componenti dopo che il componente ha terminato il rendering, usare i [ `OnAfterRenderAsync` `OnAfterRender` metodi o](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="25054-258">To manipulate components references after the component has finished rendering, use the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span>
>
> <span data-ttu-id="25054-259">Per usare una variabile di riferimento con un gestore eventi, usare un'espressione lambda o assegnare il delegato del gestore eventi nei [ `OnAfterRenderAsync` `OnAfterRender` metodi o](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="25054-259">To use a reference variable with an event handler, use a lambda expression or assign the event handler delegate in the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="25054-260">In questo modo si garantisce che la variabile di riferimento venga assegnata prima dell'assegnazione del gestore eventi.</span><span class="sxs-lookup"><span data-stu-id="25054-260">This ensures that the reference variable is assigned before the event handler is assigned.</span></span>
>
> ```razor
> <button type="button" 
>     @onclick="@(() => loginDialog.DoSomething())">Do Something</button>
>
> <MyLoginDialog @ref="loginDialog" ... />
>
> @code {
>     private MyLoginDialog loginDialog;
> }
> ```

<span data-ttu-id="25054-261">Per fare riferimento ai componenti in un ciclo, vedere [Capture References to multiple analogous Child-Components (DotNet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="25054-261">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="25054-262">Mentre l'acquisizione di riferimenti ai componenti usa una sintassi simile per l' [acquisizione di riferimenti a elementi](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), non è una funzionalità di interoperabilità di JavaScript.</span><span class="sxs-lookup"><span data-stu-id="25054-262">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="25054-263">I riferimenti ai componenti non vengono passati al codice JavaScript.</span><span class="sxs-lookup"><span data-stu-id="25054-263">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="25054-264">I riferimenti ai componenti vengono usati solo nel codice .NET.</span><span class="sxs-lookup"><span data-stu-id="25054-264">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="25054-265">**Non** usare i riferimenti ai componenti per mutare lo stato dei componenti figlio.</span><span class="sxs-lookup"><span data-stu-id="25054-265">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="25054-266">Usare invece i normali parametri dichiarativi per passare i dati ai componenti figlio.</span><span class="sxs-lookup"><span data-stu-id="25054-266">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="25054-267">L'utilizzo di normali parametri dichiarativi restituisce automaticamente i componenti figlio che eseguono il rendering alle ore corrette.</span><span class="sxs-lookup"><span data-stu-id="25054-267">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="synchronization-context"></a><span data-ttu-id="25054-268">Contesto di sincronizzazione</span><span class="sxs-lookup"><span data-stu-id="25054-268">Synchronization context</span></span>

Blazor<span data-ttu-id="25054-269">Usa un contesto di sincronizzazione ( <xref:System.Threading.SynchronizationContext> ) per applicare un singolo thread logico di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="25054-269"> uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="25054-270">I metodi del [ciclo](xref:blazor/components/lifecycle) di vita di un componente e tutti i callback di evento generati da Blazor vengono eseguiti nel contesto di sincronizzazione.</span><span class="sxs-lookup"><span data-stu-id="25054-270">A component's [lifecycle methods](xref:blazor/components/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

Blazor Server<span data-ttu-id="25054-271">il contesto di sincronizzazione tenta di emulare un ambiente a thread singolo in modo che corrisponda strettamente al modello webassembly nel browser, ovvero a thread singolo.</span><span class="sxs-lookup"><span data-stu-id="25054-271">'s synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="25054-272">In un determinato momento, il lavoro viene eseguito su un solo thread, con l'impressione di un singolo thread logico.</span><span class="sxs-lookup"><span data-stu-id="25054-272">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="25054-273">Non vengono eseguite contemporaneamente due operazioni.</span><span class="sxs-lookup"><span data-stu-id="25054-273">No two operations execute concurrently.</span></span>

### <a name="avoid-thread-blocking-calls"></a><span data-ttu-id="25054-274">Evitare chiamate di blocco del thread</span><span class="sxs-lookup"><span data-stu-id="25054-274">Avoid thread-blocking calls</span></span>

<span data-ttu-id="25054-275">In genere, non chiamare i metodi seguenti.</span><span class="sxs-lookup"><span data-stu-id="25054-275">Generally, don't call the following methods.</span></span> <span data-ttu-id="25054-276">I metodi seguenti bloccano il thread e quindi impediscono all'app di riprendere il lavoro fino al completamento dell'oggetto sottostante <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="25054-276">The following methods block the thread and thus block the app from resuming work until the underlying <xref:System.Threading.Tasks.Task> is complete:</span></span>

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="25054-277">Richiama i metodi del componente esternamente per aggiornare lo stato</span><span class="sxs-lookup"><span data-stu-id="25054-277">Invoke component methods externally to update state</span></span>

<span data-ttu-id="25054-278">Nel caso in cui un componente deve essere aggiornato in base a un evento esterno, ad esempio un timer o altre notifiche, usare il `InvokeAsync` metodo, che invia il Blazor contesto di sincronizzazione di.</span><span class="sxs-lookup"><span data-stu-id="25054-278">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="25054-279">Si consideri ad esempio un *servizio Notifier* che può inviare notifiche a qualsiasi componente in ascolto dello stato aggiornato:</span><span class="sxs-lookup"><span data-stu-id="25054-279">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

<span data-ttu-id="25054-280">Registrare `NotifierService` :</span><span class="sxs-lookup"><span data-stu-id="25054-280">Register the `NotifierService`:</span></span>

* <span data-ttu-id="25054-281">In Blazor WebAssembly registrare il servizio come singleton in `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="25054-281">In Blazor WebAssembly, register the service as singleton in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="25054-282">In Blazor Server registrare il servizio come ambito in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="25054-282">In Blazor Server, register the service as scoped in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddScoped<NotifierService>();
  ```

<span data-ttu-id="25054-283">Usare `NotifierService` per aggiornare un componente:</span><span class="sxs-lookup"><span data-stu-id="25054-283">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="25054-284">Nell'esempio precedente `NotifierService` richiama il metodo del componente `OnNotify` all'esterno del contesto di Blazor sincronizzazione di.</span><span class="sxs-lookup"><span data-stu-id="25054-284">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="25054-285">`InvokeAsync`viene usato per passare al contesto corretto e accodare un rendering.</span><span class="sxs-lookup"><span data-stu-id="25054-285">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="25054-286">Usare \@ la chiave per controllare la conservazione di elementi e componenti</span><span class="sxs-lookup"><span data-stu-id="25054-286">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="25054-287">Quando si esegue il rendering di un elenco di elementi o componenti e gli elementi o i componenti cambiano successivamente, l' Blazor algoritmo diffing deve decidere quali elementi o componenti precedenti possono essere conservati e come eseguire il mapping degli oggetti modello.</span><span class="sxs-lookup"><span data-stu-id="25054-287">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="25054-288">In genere, questo processo è automatico e può essere ignorato, ma in alcuni casi potrebbe essere necessario controllare il processo.</span><span class="sxs-lookup"><span data-stu-id="25054-288">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="25054-289">Prendere in considerazione gli esempi seguenti:</span><span class="sxs-lookup"><span data-stu-id="25054-289">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="25054-290">Il contenuto della `People` raccolta può cambiare con le voci inserite, eliminate o riordinate.</span><span class="sxs-lookup"><span data-stu-id="25054-290">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="25054-291">Quando viene eseguito il rendering del componente, è `<DetailsEditor>` possibile che il componente venga modificato in modo da ricevere `Details` valori di parametro diversi.</span><span class="sxs-lookup"><span data-stu-id="25054-291">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="25054-292">Questo può causare un rirendering più complesso del previsto.</span><span class="sxs-lookup"><span data-stu-id="25054-292">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="25054-293">In alcuni casi, il rirendering può comportare differenze di comportamento visibili, ad esempio lo stato attivo degli elementi persi.</span><span class="sxs-lookup"><span data-stu-id="25054-293">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="25054-294">È possibile controllare il processo di mapping con l' [`@key`][5] attributo della direttiva.</span><span class="sxs-lookup"><span data-stu-id="25054-294">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="25054-295">[`@key`][5]fa in modo che l'algoritmo di diffing garantisca la conservazione di elementi o componenti in base al valore della chiave:</span><span class="sxs-lookup"><span data-stu-id="25054-295">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="25054-296">Quando la `People` raccolta viene modificata, l'algoritmo diffing mantiene l'associazione tra `<DetailsEditor>` istanze e `person` istanze:</span><span class="sxs-lookup"><span data-stu-id="25054-296">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="25054-297">Se un oggetto `Person` viene eliminato dall' `People` elenco, solo l' `<DetailsEditor>` istanza corrispondente viene rimossa dall'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="25054-297">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="25054-298">Altre istanze rimangono invariate.</span><span class="sxs-lookup"><span data-stu-id="25054-298">Other instances are left unchanged.</span></span>
* <span data-ttu-id="25054-299">Se un oggetto viene inserito in una determinata `Person` posizione nell'elenco, viene inserita una nuova istanza in corrispondenza della `<DetailsEditor>` posizione corrispondente.</span><span class="sxs-lookup"><span data-stu-id="25054-299">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="25054-300">Altre istanze rimangono invariate.</span><span class="sxs-lookup"><span data-stu-id="25054-300">Other instances are left unchanged.</span></span>
* <span data-ttu-id="25054-301">Se `Person` le voci vengono riordinate, le `<DetailsEditor>` istanze corrispondenti vengono mantenute e nuovamente ordinate nell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="25054-301">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="25054-302">In alcuni scenari, l'utilizzo di [`@key`][5] riduce al minimo la complessità del rendering ed evita potenziali problemi con le parti con stato del DOM che cambiano, ad esempio la posizione dello stato attivo.</span><span class="sxs-lookup"><span data-stu-id="25054-302">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="25054-303">Le chiavi sono locali per ogni elemento contenitore o componente.</span><span class="sxs-lookup"><span data-stu-id="25054-303">Keys are local to each container element or component.</span></span> <span data-ttu-id="25054-304">Le chiavi non vengono confrontate globalmente nell'intero documento.</span><span class="sxs-lookup"><span data-stu-id="25054-304">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="25054-305">Quando usare la \@ chiave</span><span class="sxs-lookup"><span data-stu-id="25054-305">When to use \@key</span></span>

<span data-ttu-id="25054-306">In genere, è opportuno usare [`@key`][5] ogni volta che viene eseguito il rendering di un elenco (ad esempio in un blocco [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) ) e un valore appropriato per definire [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="25054-306">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="25054-307">È anche possibile usare [`@key`][5] per impedire a Blazor di mantenere un sottoalbero di un elemento o di un componente quando un oggetto viene modificato:</span><span class="sxs-lookup"><span data-stu-id="25054-307">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="25054-308">Se `@currentPerson` viene modificata, la [`@key`][5] direttiva attribute impone Blazor di rimuovere l'intero oggetto `<div>` e i relativi discendenti e di ricompilare il sottoalbero all'interno dell'interfaccia utente con nuovi elementi e componenti.</span><span class="sxs-lookup"><span data-stu-id="25054-308">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="25054-309">Questo può essere utile se è necessario garantire che lo stato dell'interfaccia utente non venga mantenuto quando viene `@currentPerson` modificato.</span><span class="sxs-lookup"><span data-stu-id="25054-309">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="25054-310">Quando non usare la \@ chiave</span><span class="sxs-lookup"><span data-stu-id="25054-310">When not to use \@key</span></span>

<span data-ttu-id="25054-311">Si verifica un costo in termini di prestazioni quando si verificano differenze con [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="25054-311">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="25054-312">Il costo delle prestazioni non è elevato, ma è sufficiente specificare solo [`@key`][5] se il controllo delle regole di conservazione degli elementi o dei componenti avvantaggia l'app.</span><span class="sxs-lookup"><span data-stu-id="25054-312">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="25054-313">Anche se [`@key`][5] non viene usato, Blazor conserva le istanze di elementi e componenti figlio il più possibile.</span><span class="sxs-lookup"><span data-stu-id="25054-313">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="25054-314">L'unico vantaggio dell'utilizzo di [`@key`][5] è il controllo sulla *modalità* di mapping delle istanze del modello alle istanze dei componenti mantenute, anziché sull'algoritmo di diffing che seleziona il mapping.</span><span class="sxs-lookup"><span data-stu-id="25054-314">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="25054-315">Valori da usare per la \@ chiave</span><span class="sxs-lookup"><span data-stu-id="25054-315">What values to use for \@key</span></span>

<span data-ttu-id="25054-316">In genere, è opportuno fornire uno dei seguenti tipi di valore per [`@key`][5] :</span><span class="sxs-lookup"><span data-stu-id="25054-316">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="25054-317">Istanze di oggetti modello (ad esempio, un' `Person` istanza come nell'esempio precedente).</span><span class="sxs-lookup"><span data-stu-id="25054-317">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="25054-318">In questo modo si garantisce la conservazione in base all'uguaglianza del riferimento all'oggetto.</span><span class="sxs-lookup"><span data-stu-id="25054-318">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="25054-319">Identificatori univoci, ad esempio valori di chiave primaria di tipo `int` , `string` o `Guid` .</span><span class="sxs-lookup"><span data-stu-id="25054-319">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="25054-320">Verificare che i valori usati per non siano in [`@key`][5] conflitto.</span><span class="sxs-lookup"><span data-stu-id="25054-320">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="25054-321">Se vengono rilevati valori di conflitto nello stesso elemento padre, Blazor genera un'eccezione perché non è in grado di eseguire il mapping deterministico di elementi o componenti precedenti a nuovi elementi o componenti.</span><span class="sxs-lookup"><span data-stu-id="25054-321">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="25054-322">Utilizzare solo valori distinti, ad esempio istanze di oggetti o valori di chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="25054-322">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a><span data-ttu-id="25054-323">Non creare componenti che scrivono nelle proprie proprietà dei parametri</span><span class="sxs-lookup"><span data-stu-id="25054-323">Don't create components that write to their own parameter properties</span></span>

<span data-ttu-id="25054-324">I parametri vengono sovrascritti nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="25054-324">Parameters are overwritten under the following conditions:</span></span>

* <span data-ttu-id="25054-325">Viene eseguito il rendering del contenuto di un componente figlio con un oggetto <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="25054-325">A child component's content is rendered with a <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span>
* <span data-ttu-id="25054-326"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>viene chiamato nel componente padre.</span><span class="sxs-lookup"><span data-stu-id="25054-326"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent component.</span></span>

<span data-ttu-id="25054-327">I parametri vengono reimpostati perché il componente padre esegue nuovamente il rendering quando <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> viene chiamato il metodo e vengono specificati nuovi valori di parametro per il componente figlio.</span><span class="sxs-lookup"><span data-stu-id="25054-327">Parameters are reset because the parent component rerenders when <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called and new parameter values are supplied to the child component.</span></span>

<span data-ttu-id="25054-328">Si consideri il `Expander` componente seguente:</span><span class="sxs-lookup"><span data-stu-id="25054-328">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="25054-329">Esegue il rendering del contenuto figlio.</span><span class="sxs-lookup"><span data-stu-id="25054-329">Renders child content.</span></span>
* <span data-ttu-id="25054-330">Consente di visualizzare o disabilitare il contenuto figlio con un parametro component.</span><span class="sxs-lookup"><span data-stu-id="25054-330">Toggles showing child content with a component parameter.</span></span>

```razor
<div @onclick="@Toggle" class="card text-white bg-success mb-3">
    <div class="card-body">
        <div class="panel-heading">
            <h2>Toggle (Expanded = @Expanded)</h2>
        </div>

        @if (Expanded)
        {
            <div class="card-text">
                @ChildContent
            </div>
        }
    </div>
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

<span data-ttu-id="25054-331">Il `Expander` componente viene aggiunto a un componente padre che può chiamare <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> :</span><span class="sxs-lookup"><span data-stu-id="25054-331">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

```razor
@page "/expander"

<Expander Expanded="true">
    Expander 1 content
</Expander>

<Expander Expanded="true" />

<button @onclick="StateHasChanged">
    Call StateHasChanged
</button>
```

<span data-ttu-id="25054-332">Inizialmente, i `Expander` componenti si comportano in modo indipendente quando le proprietà vengono disposte `Expanded` .</span><span class="sxs-lookup"><span data-stu-id="25054-332">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="25054-333">I componenti figlio gestiscono gli Stati come previsto.</span><span class="sxs-lookup"><span data-stu-id="25054-333">The child components maintain their states as expected.</span></span> <span data-ttu-id="25054-334">Quando <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> viene chiamato nell'elemento padre, il `Expanded` parametro del primo componente figlio viene reimpostato sul valore iniziale ( `true` ).</span><span class="sxs-lookup"><span data-stu-id="25054-334">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="25054-335">Il valore del secondo `Expander` componente `Expanded` non viene reimpostato perché nel secondo componente non viene eseguito il rendering del contenuto figlio.</span><span class="sxs-lookup"><span data-stu-id="25054-335">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="25054-336">Per mantenere lo stato nello scenario precedente, usare un *campo privato* nel `Expander` componente per mantenerne lo stato attivato/disattivato.</span><span class="sxs-lookup"><span data-stu-id="25054-336">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="25054-337">Il componente modificato seguente `Expander` :</span><span class="sxs-lookup"><span data-stu-id="25054-337">The following revised `Expander` component:</span></span>

* <span data-ttu-id="25054-338">Accetta il `Expanded` valore del parametro component dall'elemento padre.</span><span class="sxs-lookup"><span data-stu-id="25054-338">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="25054-339">Assegna il valore del parametro component a un *campo privato* ( `expanded` ) nell' [evento OnInitialized](xref:blazor/components/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="25054-339">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/components/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="25054-340">Usa il campo privato per mantenere lo stato di attivazione/disattivo interno.</span><span class="sxs-lookup"><span data-stu-id="25054-340">Uses the private field to maintain its internal toggle state.</span></span>

```razor
<div @onclick="@Toggle" class="card text-white bg-success mb-3">
    <div class="card-body">
        <div class="panel-heading">
            <h2>Toggle (Expanded = @expanded)</h2>
        </div>

        @if (Expanded)
        {
            <div class="card-text">
                @ChildContent
            </div>
        }
    </div>
</div>

@code {
    private bool expanded;

    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

## <a name="apply-an-attribute"></a><span data-ttu-id="25054-341">Applicare un attributo</span><span class="sxs-lookup"><span data-stu-id="25054-341">Apply an attribute</span></span>

<span data-ttu-id="25054-342">Gli attributi possono essere applicati ai Razor componenti con la [`@attribute`][7] direttiva.</span><span class="sxs-lookup"><span data-stu-id="25054-342">Attributes can be applied to Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="25054-343">Nell'esempio seguente viene applicato l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo alla classe Component:</span><span class="sxs-lookup"><span data-stu-id="25054-343">The following example applies the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="25054-344">Attributi dell'elemento HTML condizionale</span><span class="sxs-lookup"><span data-stu-id="25054-344">Conditional HTML element attributes</span></span>

<span data-ttu-id="25054-345">Gli attributi degli elementi HTML vengono sottoposti a rendering in modo condizionale in base al valore .NET.</span><span class="sxs-lookup"><span data-stu-id="25054-345">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="25054-346">Se il valore è `false` o `null` , l'attributo non viene sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="25054-346">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="25054-347">Se il valore è `true` , l'attributo viene sottoposto a rendering ridotto a icona.</span><span class="sxs-lookup"><span data-stu-id="25054-347">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="25054-348">Nell'esempio seguente, `IsCompleted` determina se `checked` viene eseguito il rendering nel markup dell'elemento:</span><span class="sxs-lookup"><span data-stu-id="25054-348">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="25054-349">Se `IsCompleted` è `true` , viene eseguito il rendering della casella di controllo come segue:</span><span class="sxs-lookup"><span data-stu-id="25054-349">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="25054-350">Se `IsCompleted` è `false` , viene eseguito il rendering della casella di controllo come segue:</span><span class="sxs-lookup"><span data-stu-id="25054-350">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="25054-351">Per altre informazioni, vedere <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="25054-351">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="25054-352">Alcuni attributi HTML, ad esempio [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons) , non funzionano correttamente quando il tipo .NET è un `bool` .</span><span class="sxs-lookup"><span data-stu-id="25054-352">Some HTML attributes, such as [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="25054-353">In questi casi, usare un `string` tipo anziché un `bool` .</span><span class="sxs-lookup"><span data-stu-id="25054-353">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="25054-354">HTML non elaborato</span><span class="sxs-lookup"><span data-stu-id="25054-354">Raw HTML</span></span>

<span data-ttu-id="25054-355">Le stringhe vengono in genere sottoposte a rendering usando i nodi di testo DOM, il che significa che qualsiasi markup che può contenere viene ignorato e considerato come testo letterale.</span><span class="sxs-lookup"><span data-stu-id="25054-355">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="25054-356">Per eseguire il rendering del codice HTML non elaborato, eseguire il wrapping del contenuto HTML in un `MarkupString` valore.</span><span class="sxs-lookup"><span data-stu-id="25054-356">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="25054-357">Il valore viene analizzato in formato HTML o SVG e inserito nel DOM.</span><span class="sxs-lookup"><span data-stu-id="25054-357">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="25054-358">Il rendering di codice HTML non elaborato costruito da un'origine non attendibile costituisce un rischio per la **sicurezza** e deve essere evitato.</span><span class="sxs-lookup"><span data-stu-id="25054-358">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="25054-359">Nell'esempio seguente viene illustrato l'utilizzo del `MarkupString` tipo per aggiungere un blocco di contenuto HTML statico all'output sottoposto a rendering di un componente:</span><span class="sxs-lookup"><span data-stu-id="25054-359">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="razor-templates"></a>Razor<span data-ttu-id="25054-360">modelli</span><span class="sxs-lookup"><span data-stu-id="25054-360"> templates</span></span>

<span data-ttu-id="25054-361">I frammenti di rendering possono essere definiti usando la Razor sintassi del modello.</span><span class="sxs-lookup"><span data-stu-id="25054-361">Render fragments can be defined using Razor template syntax.</span></span> Razor<span data-ttu-id="25054-362">i modelli sono un modo per definire un frammento di interfaccia utente e presupporre il formato seguente:</span><span class="sxs-lookup"><span data-stu-id="25054-362"> templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="25054-363">Nell'esempio seguente viene illustrato come specificare <xref:Microsoft.AspNetCore.Components.RenderFragment> <xref:Microsoft.AspNetCore.Components.RenderFragment%601> i valori e ed eseguire il rendering dei modelli direttamente in un componente.</span><span class="sxs-lookup"><span data-stu-id="25054-363">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="25054-364">I frammenti di rendering possono anche essere passati come argomenti ai [componenti basati su modelli](xref:blazor/components/templated-components).</span><span class="sxs-lookup"><span data-stu-id="25054-364">Render fragments can also be passed as arguments to [templated components](xref:blazor/components/templated-components).</span></span>

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="25054-365">Output del codice precedente sottoposto a rendering:</span><span class="sxs-lookup"><span data-stu-id="25054-365">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a><span data-ttu-id="25054-366">Asset statici</span><span class="sxs-lookup"><span data-stu-id="25054-366">Static assets</span></span>

Blazor<span data-ttu-id="25054-367">segue la convenzione di ASP.NET Core app che collocano asset statici nella [ `web root (wwwroot)` cartella](xref:fundamentals/index#web-root)del progetto.</span><span class="sxs-lookup"><span data-stu-id="25054-367"> follows the convention of ASP.NET Core apps placing static assets under the project's [`web root (wwwroot)` folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="25054-368">Usare un percorso relativo di base ( `/` ) per fare riferimento alla radice Web per un asset statico.</span><span class="sxs-lookup"><span data-stu-id="25054-368">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="25054-369">Nell'esempio seguente, `logo.png` si trova fisicamente nella `{PROJECT ROOT}/wwwroot/images` cartella:</span><span class="sxs-lookup"><span data-stu-id="25054-369">In the following example, `logo.png` is physically located in the `{PROJECT ROOT}/wwwroot/images` folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor<span data-ttu-id="25054-370">i componenti **non** supportano la notazione tilde-barra ( `~/` ).</span><span class="sxs-lookup"><span data-stu-id="25054-370"> components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="25054-371">Per informazioni sull'impostazione del percorso di base di un'app, vedere <xref:blazor/host-and-deploy/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="25054-371">For information on setting an app's base path, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="25054-372">Gli helper tag non sono supportati nei componenti</span><span class="sxs-lookup"><span data-stu-id="25054-372">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="25054-373">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro)non sono supportati in Razor componenti ( `.razor` file).</span><span class="sxs-lookup"><span data-stu-id="25054-373">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (`.razor` files).</span></span> <span data-ttu-id="25054-374">Per fornire funzionalità di tipo Helper tag in Blazor , creare un componente con la stessa funzionalità dell'helper tag e utilizzare il componente.</span><span class="sxs-lookup"><span data-stu-id="25054-374">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="25054-375">Immagini SVG (Scalable Vector Graphics)</span><span class="sxs-lookup"><span data-stu-id="25054-375">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="25054-376">Poiché Blazor esegue il rendering di HTML, le immagini supportate dal browser, incluse le immagini SVG (Scalable Vector Graphics) `.svg` , sono supportate tramite il `<img>` Tag:</span><span class="sxs-lookup"><span data-stu-id="25054-376">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (`.svg`), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="25054-377">Analogamente, le immagini SVG sono supportate nelle regole CSS di un file di foglio di stile ( `.css` ):</span><span class="sxs-lookup"><span data-stu-id="25054-377">Similarly, SVG images are supported in the CSS rules of a stylesheet file (`.css`):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="25054-378">Tuttavia, il markup SVG inline non è supportato in tutti gli scenari.</span><span class="sxs-lookup"><span data-stu-id="25054-378">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="25054-379">Se si inserisce un `<svg>` tag direttamente in un file di componente ( `.razor` ), il rendering delle immagini di base è supportato, ma molti scenari avanzati non sono ancora supportati.</span><span class="sxs-lookup"><span data-stu-id="25054-379">If you place an `<svg>` tag directly into a component file (`.razor`), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="25054-380">Ad esempio, i `<use>` tag non sono attualmente rispettati e [`@bind`][10] non possono essere usati con alcuni tag svg.</span><span class="sxs-lookup"><span data-stu-id="25054-380">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="25054-381">Per ulteriori informazioni, vedere [supporto per SVG in Blazor (#18271 DotNet/aspnetcore)](https://github.com/dotnet/aspnetcore/issues/18271).</span><span class="sxs-lookup"><span data-stu-id="25054-381">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="25054-382">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="25054-382">Additional resources</span></span>

* <span data-ttu-id="25054-383"><xref:blazor/security/server/threat-mitigation>: Include informazioni aggiuntive sulla creazione di Blazor Server app che devono essere confrontate con l'esaurimento delle risorse.</span><span class="sxs-lookup"><span data-stu-id="25054-383"><xref:blazor/security/server/threat-mitigation>: Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
