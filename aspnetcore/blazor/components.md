---
title: Creazione e utilizzo di Razor componenti ASP.NET Core
author: guardrex
description: Informazioni su come creare e usare Razor i componenti, tra cui la modalità di associazione ai dati, la gestione degli eventi e la gestione dei cicli di vita dei componenti.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components
ms.openlocfilehash: a7009bf1cf99a15f3617b47a904d52f5787b9ce1
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153510"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="d299a-103">Creazione e utilizzo di Razor componenti ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d299a-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="d299a-104">Di [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)e [Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="d299a-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="d299a-105">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d299a-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="d299a-106">le app vengono compilate usando i *componenti*.</span><span class="sxs-lookup"><span data-stu-id="d299a-106"> apps are built using *components*.</span></span> <span data-ttu-id="d299a-107">Un componente è un blocco di interfaccia utente (UI) autonomo, ad esempio una pagina, una finestra di dialogo o un form.</span><span class="sxs-lookup"><span data-stu-id="d299a-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="d299a-108">Un componente include il markup HTML e la logica di elaborazione necessaria per inserire i dati o rispondere agli eventi dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="d299a-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="d299a-109">I componenti sono flessibili e leggeri.</span><span class="sxs-lookup"><span data-stu-id="d299a-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="d299a-110">Possono essere annidati, riutilizzati e condivisi tra i progetti.</span><span class="sxs-lookup"><span data-stu-id="d299a-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="d299a-111">Classi di componenti</span><span class="sxs-lookup"><span data-stu-id="d299a-111">Component classes</span></span>

<span data-ttu-id="d299a-112">I componenti vengono implementati in [Razor](xref:mvc/views/razor) file componente (*Razor*) utilizzando una combinazione di markup C# e HTML.</span><span class="sxs-lookup"><span data-stu-id="d299a-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="d299a-113">Un componente in Blazor viene definito formalmente come \* Razor componente\*.</span><span class="sxs-lookup"><span data-stu-id="d299a-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="d299a-114">Il nome di un componente deve iniziare con un carattere maiuscolo.</span><span class="sxs-lookup"><span data-stu-id="d299a-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="d299a-115">Ad esempio, *MyCoolComponent. Razor* è valido e *MyCoolComponent. Razor* non è valido.</span><span class="sxs-lookup"><span data-stu-id="d299a-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="d299a-116">L'interfaccia utente per un componente viene definita utilizzando HTML.</span><span class="sxs-lookup"><span data-stu-id="d299a-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="d299a-117">La logica di rendering dinamica (ad esempio, cicli, condizionali, espressioni) viene aggiunta usando una sintassi C# incorporata denominata [Razor](xref:mvc/views/razor) .</span><span class="sxs-lookup"><span data-stu-id="d299a-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="d299a-118">Quando viene compilata un'app, il markup HTML e la logica di rendering C# vengono convertiti in una classe Component.</span><span class="sxs-lookup"><span data-stu-id="d299a-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="d299a-119">Il nome della classe generata corrisponde al nome del file.</span><span class="sxs-lookup"><span data-stu-id="d299a-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="d299a-120">I membri della classe del componente vengono definiti in un blocco `@code`.</span><span class="sxs-lookup"><span data-stu-id="d299a-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="d299a-121">Nel `@code` blocco, lo stato del componente (proprietà, campi) viene specificato con i metodi per la gestione degli eventi o per la definizione di altre logiche dei componenti.</span><span class="sxs-lookup"><span data-stu-id="d299a-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="d299a-122">È consentito più di un blocco `@code`.</span><span class="sxs-lookup"><span data-stu-id="d299a-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="d299a-123">I membri dei componenti possono essere usati come parte della logica di rendering del componente usando espressioni C# che iniziano con `@` .</span><span class="sxs-lookup"><span data-stu-id="d299a-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="d299a-124">Ad esempio, viene eseguito il rendering di un campo C# anteponendo `@` il nome del campo.</span><span class="sxs-lookup"><span data-stu-id="d299a-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="d299a-125">Nell'esempio seguente viene valutato ed eseguito il rendering:</span><span class="sxs-lookup"><span data-stu-id="d299a-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="d299a-126">`headingFontStyle`al valore della proprietà CSS per `font-style` .</span><span class="sxs-lookup"><span data-stu-id="d299a-126">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="d299a-127">`headingText`al contenuto dell' `<h1>` elemento.</span><span class="sxs-lookup"><span data-stu-id="d299a-127">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="d299a-128">Una volta eseguito il rendering iniziale del componente, il componente rigenera l'albero di rendering in risposta agli eventi.</span><span class="sxs-lookup"><span data-stu-id="d299a-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="d299a-129">Confronta quindi il nuovo albero di rendering con quello precedente e applica le modifiche apportate al Document Object Model (DOM) del browser.</span><span class="sxs-lookup"><span data-stu-id="d299a-129"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="d299a-130">I componenti sono classi C# ordinarie e possono essere inseriti in qualsiasi punto all'interno di un progetto.</span><span class="sxs-lookup"><span data-stu-id="d299a-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="d299a-131">I componenti che producono pagine Web in genere risiedono nella cartella *pages* .</span><span class="sxs-lookup"><span data-stu-id="d299a-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="d299a-132">I componenti non di pagina vengono spesso inseriti nella cartella *condivisa* o in una cartella personalizzata aggiunta al progetto.</span><span class="sxs-lookup"><span data-stu-id="d299a-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="d299a-133">Lo spazio dei nomi di un componente viene in genere derivato dallo spazio dei nomi radice dell'app e dal percorso (cartella) del componente all'interno dell'app.</span><span class="sxs-lookup"><span data-stu-id="d299a-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="d299a-134">Se lo spazio dei nomi radice dell'app è `BlazorApp` e il componente si trova `Counter` nella cartella *pages* :</span><span class="sxs-lookup"><span data-stu-id="d299a-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="d299a-135">Lo `Counter` spazio dei nomi del componente è `BlazorApp.Pages` .</span><span class="sxs-lookup"><span data-stu-id="d299a-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="d299a-136">Il nome completo del tipo del componente è `BlazorApp.Pages.Counter` .</span><span class="sxs-lookup"><span data-stu-id="d299a-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="d299a-137">Per le cartelle personalizzate che contengono componenti, aggiungere un' `using` istruzione al componente padre o al file *_Imports. Razor* dell'app.</span><span class="sxs-lookup"><span data-stu-id="d299a-137">For custom folders that hold components, add a `using` statement to the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="d299a-138">Nell'esempio seguente vengono resi disponibili i componenti nella cartella dei *componenti* :</span><span class="sxs-lookup"><span data-stu-id="d299a-138">The following example makes components in the *Components* folder available:</span></span>

```razor
@using BlazorApp.Components
```

<span data-ttu-id="d299a-139">In alternativa, è possibile fare riferimento direttamente a un componente:</span><span class="sxs-lookup"><span data-stu-id="d299a-139">Alternatively, a component can be directly referenced:</span></span>

```razor
<BlazorApp.Components.MyCoolComponent />
```

<span data-ttu-id="d299a-140">Per ulteriori informazioni, vedere la sezione [Import Components](#import-components) .</span><span class="sxs-lookup"><span data-stu-id="d299a-140">For more information, see the [Import components](#import-components) section.</span></span>

## <a name="static-assets"></a><span data-ttu-id="d299a-141">Asset statici</span><span class="sxs-lookup"><span data-stu-id="d299a-141">Static assets</span></span>

Blazor<span data-ttu-id="d299a-142">segue la convenzione di ASP.NET Core app che collocano asset statici nella [cartella radice Web del progetto (wwwroot)](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="d299a-142"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="d299a-143">Usare un percorso relativo di base ( `/` ) per fare riferimento alla radice Web per un asset statico.</span><span class="sxs-lookup"><span data-stu-id="d299a-143">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="d299a-144">Nell'esempio seguente *logo. png* si trova fisicamente nella cartella *{Project root}/wwwroot/images* :</span><span class="sxs-lookup"><span data-stu-id="d299a-144">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor<span data-ttu-id="d299a-145">i componenti **non** supportano la notazione tilde-barra ( `~/` ).</span><span class="sxs-lookup"><span data-stu-id="d299a-145"> components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="d299a-146">Per informazioni sull'impostazione del percorso di base di un'app, vedere <xref:host-and-deploy/blazor/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="d299a-146">For information on setting an app's base path, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="d299a-147">Gli helper tag non sono supportati nei componenti</span><span class="sxs-lookup"><span data-stu-id="d299a-147">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="d299a-148">Gli [Helper Tag](xref:mvc/views/tag-helpers/intro) non sono supportati nei Razor componenti (file*Razor* ).</span><span class="sxs-lookup"><span data-stu-id="d299a-148">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="d299a-149">Per fornire funzionalità di tipo Helper tag in Blazor , creare un componente con la stessa funzionalità dell'helper tag e utilizzare il componente.</span><span class="sxs-lookup"><span data-stu-id="d299a-149">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="d299a-150">Usare i componenti</span><span class="sxs-lookup"><span data-stu-id="d299a-150">Use components</span></span>

<span data-ttu-id="d299a-151">I componenti possono includere altri componenti dichiarando questi ultimi usando la sintassi degli elementi HTML.</span><span class="sxs-lookup"><span data-stu-id="d299a-151">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="d299a-152">Il markup per l'uso di un componente è simile a un tag HTML, in cui il nome del tag è il tipo di componente.</span><span class="sxs-lookup"><span data-stu-id="d299a-152">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="d299a-153">Il markup seguente in *index. Razor* esegue il rendering di un' `HeadingComponent` istanza:</span><span class="sxs-lookup"><span data-stu-id="d299a-153">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="d299a-154">*Components/HeadingComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="d299a-154">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="d299a-155">Se un componente contiene un elemento HTML con una prima lettera maiuscola che non corrisponde a un nome di componente, viene emesso un avviso che indica che l'elemento ha un nome imprevisto.</span><span class="sxs-lookup"><span data-stu-id="d299a-155">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="d299a-156">L'aggiunta di una `@using` direttiva per lo spazio dei nomi del componente rende disponibile il componente, che risolve l'avviso.</span><span class="sxs-lookup"><span data-stu-id="d299a-156">Adding an `@using` directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="d299a-157">Routing.</span><span class="sxs-lookup"><span data-stu-id="d299a-157">Routing</span></span>

<span data-ttu-id="d299a-158">Il routing in Blazor viene effettuato fornendo un modello di route a ogni componente accessibile nell'app.</span><span class="sxs-lookup"><span data-stu-id="d299a-158">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="d299a-159">Quando Razor viene compilato un file con una `@page` direttiva, alla classe generata viene assegnato un oggetto che <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifica il modello di route.</span><span class="sxs-lookup"><span data-stu-id="d299a-159">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="d299a-160">In fase di esecuzione, il router cerca le classi di componenti con un oggetto `RouteAttribute` ed esegue il rendering di qualsiasi componente con un modello di route corrispondente all'URL richiesto.</span><span class="sxs-lookup"><span data-stu-id="d299a-160">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="d299a-161">Per altre informazioni, vedere <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="d299a-161">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="d299a-162">Parametri</span><span class="sxs-lookup"><span data-stu-id="d299a-162">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="d299a-163">Parametri di route</span><span class="sxs-lookup"><span data-stu-id="d299a-163">Route parameters</span></span>

<span data-ttu-id="d299a-164">I componenti possono ricevere parametri di route dal modello di route fornito nella `@page` direttiva.</span><span class="sxs-lookup"><span data-stu-id="d299a-164">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="d299a-165">Il router usa parametri di route per popolare i parametri del componente corrispondente.</span><span class="sxs-lookup"><span data-stu-id="d299a-165">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="d299a-166">*Pages/RouteParameter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="d299a-166">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="d299a-167">I parametri facoltativi non sono supportati, quindi `@page` vengono applicate due direttive nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="d299a-167">Optional parameters aren't supported, so two `@page` directives are applied in the preceding example.</span></span> <span data-ttu-id="d299a-168">Il primo consente la navigazione al componente senza un parametro.</span><span class="sxs-lookup"><span data-stu-id="d299a-168">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="d299a-169">La seconda `@page` direttiva riceve il `{text}` parametro di route e assegna il valore alla `Text` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="d299a-169">The second `@page` directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="d299a-170">La sintassi dei parametri *catch-all* ( `*` / `**` ), che acquisisce il percorso tra più limiti di cartella, **non** è supportata nei Razor componenti (*Razor*).</span><span class="sxs-lookup"><span data-stu-id="d299a-170">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="d299a-171">Parametri del componente</span><span class="sxs-lookup"><span data-stu-id="d299a-171">Component parameters</span></span>

<span data-ttu-id="d299a-172">I componenti possono avere *parametri del componente*, che vengono definiti usando proprietà pubbliche nella classe Component con l' `[Parameter]` attributo.</span><span class="sxs-lookup"><span data-stu-id="d299a-172">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="d299a-173">Usare gli attributi per specificare gli argomenti per un componente nel markup.</span><span class="sxs-lookup"><span data-stu-id="d299a-173">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="d299a-174">*Components/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="d299a-174">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="d299a-175">Nell'esempio seguente dall'app di esempio, `ParentComponent` imposta il valore della `Title` proprietà dell'oggetto `ChildComponent` .</span><span class="sxs-lookup"><span data-stu-id="d299a-175">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="d299a-176">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="d299a-176">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="d299a-177">Non creare componenti che scrivono nei propri *parametri del componente*, usare invece un campo privato.</span><span class="sxs-lookup"><span data-stu-id="d299a-177">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="d299a-178">Per ulteriori informazioni, vedere la sezione [non creare componenti che scrivono nella relativa proprietà dei parametri](#dont-create-components-that-write-to-their-own-parameter-properties) .</span><span class="sxs-lookup"><span data-stu-id="d299a-178">For more information, see the [Don't create components that write to their own parameter properties](#dont-create-components-that-write-to-their-own-parameter-properties) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="d299a-179">Contenuto figlio</span><span class="sxs-lookup"><span data-stu-id="d299a-179">Child content</span></span>

<span data-ttu-id="d299a-180">I componenti possono impostare il contenuto di un altro componente.</span><span class="sxs-lookup"><span data-stu-id="d299a-180">Components can set the content of another component.</span></span> <span data-ttu-id="d299a-181">Il componente di assegnazione fornisce il contenuto tra i tag che specificano il componente di destinazione.</span><span class="sxs-lookup"><span data-stu-id="d299a-181">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="d299a-182">Nell'esempio seguente, `ChildComponent` ha una `ChildContent` proprietà che rappresenta un oggetto `RenderFragment` , che rappresenta un segmento di interfaccia utente di cui eseguire il rendering.</span><span class="sxs-lookup"><span data-stu-id="d299a-182">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="d299a-183">Il valore di `ChildContent` viene posizionato nel markup del componente in cui deve essere eseguito il rendering del contenuto.</span><span class="sxs-lookup"><span data-stu-id="d299a-183">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="d299a-184">Il valore di `ChildContent` viene ricevuto dal componente padre e ne viene eseguito il rendering all'interno del pannello bootstrap `panel-body` .</span><span class="sxs-lookup"><span data-stu-id="d299a-184">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="d299a-185">*Components/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="d299a-185">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="d299a-186">La proprietà che riceve il `RenderFragment` contenuto deve essere denominata `ChildContent` per convenzione.</span><span class="sxs-lookup"><span data-stu-id="d299a-186">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="d299a-187">`ParentComponent`Nell'app di esempio può fornire contenuto per il rendering `ChildComponent` di inserendo il contenuto all'interno dei `<ChildComponent>` tag.</span><span class="sxs-lookup"><span data-stu-id="d299a-187">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="d299a-188">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="d299a-188">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="d299a-189">Attributo splatting e parametri arbitrari</span><span class="sxs-lookup"><span data-stu-id="d299a-189">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="d299a-190">I componenti possono acquisire ed eseguire il rendering di attributi aggiuntivi oltre ai parametri dichiarati del componente.</span><span class="sxs-lookup"><span data-stu-id="d299a-190">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="d299a-191">È possibile acquisire attributi aggiuntivi in un dizionario e quindi *Splatted* su un elemento quando il componente viene sottoposto a rendering usando la [`@attributes`](xref:mvc/views/razor#attributes) Razor direttiva.</span><span class="sxs-lookup"><span data-stu-id="d299a-191">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="d299a-192">Questo scenario è utile quando si definisce un componente che produce un elemento di markup che supporta un'ampia gamma di personalizzazioni.</span><span class="sxs-lookup"><span data-stu-id="d299a-192">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="d299a-193">Ad esempio, può essere noioso definire gli attributi separatamente per un `<input>` che supporta molti parametri.</span><span class="sxs-lookup"><span data-stu-id="d299a-193">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="d299a-194">Nell'esempio seguente, il primo `<input>` elemento ( `id="useIndividualParams"` ) USA parametri dei singoli componenti, mentre il secondo `<input>` elemento ( `id="useAttributesDict"` ) usa l'attributo splatting:</span><span class="sxs-lookup"><span data-stu-id="d299a-194">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="d299a-195">Il tipo del parametro deve implementare `IEnumerable<KeyValuePair<string, object>>` con chiavi di stringa.</span><span class="sxs-lookup"><span data-stu-id="d299a-195">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="d299a-196">L'uso `IReadOnlyDictionary<string, object>` di è anche un'opzione in questo scenario.</span><span class="sxs-lookup"><span data-stu-id="d299a-196">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="d299a-197">Gli elementi sottoposti a rendering `<input>` usando entrambi gli approcci sono identici:</span><span class="sxs-lookup"><span data-stu-id="d299a-197">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="d299a-198">Per accettare attributi arbitrari, definire un parametro component usando l' `[Parameter]` attributo con la `CaptureUnmatchedValues` proprietà impostata su `true` :</span><span class="sxs-lookup"><span data-stu-id="d299a-198">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="d299a-199">La `CaptureUnmatchedValues` proprietà su `[Parameter]` consente al parametro di trovare la corrispondenza con tutti gli attributi che non corrispondono ad altri parametri.</span><span class="sxs-lookup"><span data-stu-id="d299a-199">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="d299a-200">Un componente può definire solo un singolo parametro con `CaptureUnmatchedValues` .</span><span class="sxs-lookup"><span data-stu-id="d299a-200">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="d299a-201">Il tipo di proprietà utilizzato con `CaptureUnmatchedValues` deve essere assegnabile da `Dictionary<string, object>` con chiavi di stringa.</span><span class="sxs-lookup"><span data-stu-id="d299a-201">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="d299a-202">`IEnumerable<KeyValuePair<string, object>>`o `IReadOnlyDictionary<string, object>` sono anche opzioni in questo scenario.</span><span class="sxs-lookup"><span data-stu-id="d299a-202">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="d299a-203">La posizione di `@attributes` relativa alla posizione degli attributi degli elementi è importante.</span><span class="sxs-lookup"><span data-stu-id="d299a-203">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="d299a-204">Quando `@attributes` Splatted sull'elemento, gli attributi vengono elaborati da destra a sinistra (da ultimo a primo).</span><span class="sxs-lookup"><span data-stu-id="d299a-204">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="d299a-205">Si consideri l'esempio seguente di un componente che utilizza un `Child` componente:</span><span class="sxs-lookup"><span data-stu-id="d299a-205">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="d299a-206">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="d299a-206">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="d299a-207">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="d299a-207">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="d299a-208">L' `Child` attributo del componente `extra` viene impostato a destra di `@attributes` .</span><span class="sxs-lookup"><span data-stu-id="d299a-208">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="d299a-209">Il `Parent` rendering del componente `<div>` contiene `extra="5"` quando viene passato attraverso l'attributo aggiuntivo perché gli attributi vengono elaborati da destra a sinistra (da ultimo a primo):</span><span class="sxs-lookup"><span data-stu-id="d299a-209">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="d299a-210">Nell'esempio seguente, l'ordine di `extra` e `@attributes` viene invertito nell'oggetto del `Child` componente `<div>` :</span><span class="sxs-lookup"><span data-stu-id="d299a-210">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="d299a-211">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="d299a-211">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="d299a-212">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="d299a-212">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="d299a-213">Il rendering `<div>` nel `Parent` componente contiene `extra="10"` quando viene passato tramite l'attributo aggiuntivo:</span><span class="sxs-lookup"><span data-stu-id="d299a-213">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="d299a-214">Acquisisci riferimenti ai componenti</span><span class="sxs-lookup"><span data-stu-id="d299a-214">Capture references to components</span></span>

<span data-ttu-id="d299a-215">I riferimenti ai componenti forniscono un modo per fare riferimento a un'istanza del componente in modo da poter emettere comandi per tale istanza, ad esempio `Show` o `Reset` .</span><span class="sxs-lookup"><span data-stu-id="d299a-215">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="d299a-216">Per acquisire un riferimento a un componente:</span><span class="sxs-lookup"><span data-stu-id="d299a-216">To capture a component reference:</span></span>

* <span data-ttu-id="d299a-217">Aggiungere un [`@ref`](xref:mvc/views/razor#ref) attributo al componente figlio.</span><span class="sxs-lookup"><span data-stu-id="d299a-217">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="d299a-218">Definire un campo con lo stesso tipo del componente figlio.</span><span class="sxs-lookup"><span data-stu-id="d299a-218">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="d299a-219">Quando viene eseguito il rendering del componente, il `loginDialog` campo viene popolato con l' `MyLoginDialog` istanza del componente figlio.</span><span class="sxs-lookup"><span data-stu-id="d299a-219">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="d299a-220">È quindi possibile richiamare i metodi .NET nell'istanza del componente.</span><span class="sxs-lookup"><span data-stu-id="d299a-220">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d299a-221">La `loginDialog` variabile viene popolata solo dopo il rendering del componente e l'output include `MyLoginDialog` l'elemento.</span><span class="sxs-lookup"><span data-stu-id="d299a-221">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="d299a-222">Fino a quel momento, non c'è niente a cui fare riferimento.</span><span class="sxs-lookup"><span data-stu-id="d299a-222">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="d299a-223">Per modificare i riferimenti ai componenti dopo che il componente ha terminato il rendering, usare i [Metodi OnAfterRenderAsync o OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="d299a-223">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="d299a-224">Per fare riferimento ai componenti in un ciclo, vedere [Capture References to multiple analogous Child-Components (DotNet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="d299a-224">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="d299a-225">Mentre l'acquisizione di riferimenti ai componenti usa una sintassi simile per l' [acquisizione di riferimenti a elementi](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), non è una funzionalità di interoperabilità di JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d299a-225">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="d299a-226">I riferimenti ai componenti non vengono passati al codice JavaScript &mdash; che vengono usati solo nel codice .NET.</span><span class="sxs-lookup"><span data-stu-id="d299a-226">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="d299a-227">**Non** usare i riferimenti ai componenti per mutare lo stato dei componenti figlio.</span><span class="sxs-lookup"><span data-stu-id="d299a-227">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="d299a-228">Usare invece i normali parametri dichiarativi per passare i dati ai componenti figlio.</span><span class="sxs-lookup"><span data-stu-id="d299a-228">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="d299a-229">L'utilizzo di normali parametri dichiarativi restituisce automaticamente i componenti figlio che eseguono il rendering alle ore corrette.</span><span class="sxs-lookup"><span data-stu-id="d299a-229">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="d299a-230">Richiama i metodi del componente esternamente per aggiornare lo stato</span><span class="sxs-lookup"><span data-stu-id="d299a-230">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="d299a-231">Usa un contesto di sincronizzazione ( `SynchronizationContext` ) per applicare un singolo thread logico di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="d299a-231"> uses a synchronization context (`SynchronizationContext`) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="d299a-232">I metodi del [ciclo](xref:blazor/lifecycle) di vita di un componente e tutti i callback di evento generati da Blazor vengono eseguiti nel contesto di sincronizzazione.</span><span class="sxs-lookup"><span data-stu-id="d299a-232">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

Blazor<span data-ttu-id="d299a-233">Il contesto di sincronizzazione del server tenta di emulare un ambiente a thread singolo in modo che corrisponda strettamente al modello webassembly nel browser, ovvero a thread singolo.</span><span class="sxs-lookup"><span data-stu-id="d299a-233"> Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="d299a-234">In un determinato momento, il lavoro viene eseguito su un solo thread, con l'impressione di un singolo thread logico.</span><span class="sxs-lookup"><span data-stu-id="d299a-234">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="d299a-235">Non vengono eseguite contemporaneamente due operazioni.</span><span class="sxs-lookup"><span data-stu-id="d299a-235">No two operations execute concurrently.</span></span>

<span data-ttu-id="d299a-236">Nel caso in cui un componente deve essere aggiornato in base a un evento esterno, ad esempio un timer o altre notifiche, usare il `InvokeAsync` metodo, che invierà al Blazor contesto di sincronizzazione.</span><span class="sxs-lookup"><span data-stu-id="d299a-236">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's synchronization context.</span></span> <span data-ttu-id="d299a-237">Si consideri ad esempio un *servizio Notifier* che può inviare notifiche a qualsiasi componente in ascolto dello stato aggiornato:</span><span class="sxs-lookup"><span data-stu-id="d299a-237">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="d299a-238">Registrare il `NotifierService` come singletion:</span><span class="sxs-lookup"><span data-stu-id="d299a-238">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="d299a-239">In Blazor webassembly registrare il servizio in `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="d299a-239">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="d299a-240">In Blazor server registrare il servizio in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d299a-240">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="d299a-241">Usare `NotifierService` per aggiornare un componente:</span><span class="sxs-lookup"><span data-stu-id="d299a-241">Use the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="d299a-242">Nell'esempio precedente `NotifierService` richiama il metodo del componente `OnNotify` all'esterno del contesto di Blazor sincronizzazione di.</span><span class="sxs-lookup"><span data-stu-id="d299a-242">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="d299a-243">`InvokeAsync`viene usato per passare al contesto corretto e accodare un rendering.</span><span class="sxs-lookup"><span data-stu-id="d299a-243">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="d299a-244">Usare \@ la chiave per controllare la conservazione di elementi e componenti</span><span class="sxs-lookup"><span data-stu-id="d299a-244">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="d299a-245">Quando si esegue il rendering di un elenco di elementi o componenti e gli elementi o i componenti cambiano successivamente, l' Blazor algoritmo diffing deve decidere quali elementi o componenti precedenti possono essere conservati e come eseguire il mapping degli oggetti modello.</span><span class="sxs-lookup"><span data-stu-id="d299a-245">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="d299a-246">In genere, questo processo è automatico e può essere ignorato, ma in alcuni casi potrebbe essere necessario controllare il processo.</span><span class="sxs-lookup"><span data-stu-id="d299a-246">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="d299a-247">Prendere in considerazione gli esempi seguenti:</span><span class="sxs-lookup"><span data-stu-id="d299a-247">Consider the following example:</span></span>

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

<span data-ttu-id="d299a-248">Il contenuto della `People` raccolta può cambiare con le voci inserite, eliminate o riordinate.</span><span class="sxs-lookup"><span data-stu-id="d299a-248">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="d299a-249">Quando viene eseguito il rendering del componente, è `<DetailsEditor>` possibile che il componente venga modificato in modo da ricevere `Details` valori di parametro diversi.</span><span class="sxs-lookup"><span data-stu-id="d299a-249">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="d299a-250">Questo può causare un rirendering più complesso del previsto.</span><span class="sxs-lookup"><span data-stu-id="d299a-250">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="d299a-251">In alcuni casi, il rirendering può comportare differenze di comportamento visibili, ad esempio lo stato attivo degli elementi persi.</span><span class="sxs-lookup"><span data-stu-id="d299a-251">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="d299a-252">È possibile controllare il processo di mapping con l' [`@key`](xref:mvc/views/razor#key) attributo della direttiva.</span><span class="sxs-lookup"><span data-stu-id="d299a-252">The mapping process can be controlled with the [`@key`](xref:mvc/views/razor#key) directive attribute.</span></span> <span data-ttu-id="d299a-253">`@key`fa in modo che l'algoritmo di diffing garantisca la conservazione di elementi o componenti in base al valore della chiave:</span><span class="sxs-lookup"><span data-stu-id="d299a-253">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="d299a-254">Quando la `People` raccolta viene modificata, l'algoritmo diffing mantiene l'associazione tra `<DetailsEditor>` istanze e `person` istanze:</span><span class="sxs-lookup"><span data-stu-id="d299a-254">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="d299a-255">Se un oggetto `Person` viene eliminato dall' `People` elenco, solo l' `<DetailsEditor>` istanza corrispondente viene rimossa dall'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="d299a-255">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="d299a-256">Altre istanze rimangono invariate.</span><span class="sxs-lookup"><span data-stu-id="d299a-256">Other instances are left unchanged.</span></span>
* <span data-ttu-id="d299a-257">Se un oggetto viene inserito in una determinata `Person` posizione nell'elenco, viene inserita una nuova istanza in corrispondenza della `<DetailsEditor>` posizione corrispondente.</span><span class="sxs-lookup"><span data-stu-id="d299a-257">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="d299a-258">Altre istanze rimangono invariate.</span><span class="sxs-lookup"><span data-stu-id="d299a-258">Other instances are left unchanged.</span></span>
* <span data-ttu-id="d299a-259">Se `Person` le voci vengono riordinate, le `<DetailsEditor>` istanze corrispondenti vengono mantenute e nuovamente ordinate nell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="d299a-259">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="d299a-260">In alcuni scenari, l'utilizzo di `@key` riduce al minimo la complessità del rendering ed evita potenziali problemi con le parti con stato del DOM che cambiano, ad esempio la posizione dello stato attivo.</span><span class="sxs-lookup"><span data-stu-id="d299a-260">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d299a-261">Le chiavi sono locali per ogni elemento contenitore o componente.</span><span class="sxs-lookup"><span data-stu-id="d299a-261">Keys are local to each container element or component.</span></span> <span data-ttu-id="d299a-262">Le chiavi non vengono confrontate globalmente nell'intero documento.</span><span class="sxs-lookup"><span data-stu-id="d299a-262">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="d299a-263">Quando usare la \@ chiave</span><span class="sxs-lookup"><span data-stu-id="d299a-263">When to use \@key</span></span>

<span data-ttu-id="d299a-264">In genere, è opportuno usare `@key` ogni volta che viene eseguito il rendering di un elenco (ad esempio, in un `@foreach` blocco) e un valore appropriato per definire `@key` .</span><span class="sxs-lookup"><span data-stu-id="d299a-264">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="d299a-265">È anche possibile usare `@key` per impedire a Blazor di mantenere un sottoalbero di un elemento o di un componente quando un oggetto viene modificato:</span><span class="sxs-lookup"><span data-stu-id="d299a-265">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="d299a-266">Se `@currentPerson` viene modificata, la `@key` direttiva attribute impone Blazor di rimuovere l'intero oggetto `<div>` e i relativi discendenti e di ricompilare il sottoalbero all'interno dell'interfaccia utente con nuovi elementi e componenti.</span><span class="sxs-lookup"><span data-stu-id="d299a-266">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="d299a-267">Questo può essere utile se è necessario garantire che lo stato dell'interfaccia utente non venga mantenuto quando viene `@currentPerson` modificato.</span><span class="sxs-lookup"><span data-stu-id="d299a-267">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="d299a-268">Quando non usare la \@ chiave</span><span class="sxs-lookup"><span data-stu-id="d299a-268">When not to use \@key</span></span>

<span data-ttu-id="d299a-269">Si verifica un costo in termini di prestazioni quando si verificano differenze con `@key` .</span><span class="sxs-lookup"><span data-stu-id="d299a-269">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="d299a-270">Il costo delle prestazioni non è elevato, ma è sufficiente specificare solo `@key` se il controllo delle regole di conservazione degli elementi o dei componenti avvantaggia l'app.</span><span class="sxs-lookup"><span data-stu-id="d299a-270">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="d299a-271">Anche se `@key` non viene usato, Blazor conserva le istanze di elementi e componenti figlio il più possibile.</span><span class="sxs-lookup"><span data-stu-id="d299a-271">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="d299a-272">L'unico vantaggio dell'utilizzo di `@key` è il controllo sulla *modalità* di mapping delle istanze del modello alle istanze dei componenti mantenute, anziché sull'algoritmo di diffing che seleziona il mapping.</span><span class="sxs-lookup"><span data-stu-id="d299a-272">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="d299a-273">Valori da usare per la \@ chiave</span><span class="sxs-lookup"><span data-stu-id="d299a-273">What values to use for \@key</span></span>

<span data-ttu-id="d299a-274">In genere, è opportuno fornire uno dei seguenti tipi di valore per `@key` :</span><span class="sxs-lookup"><span data-stu-id="d299a-274">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="d299a-275">Istanze di oggetti modello (ad esempio, un' `Person` istanza come nell'esempio precedente).</span><span class="sxs-lookup"><span data-stu-id="d299a-275">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="d299a-276">In questo modo si garantisce la conservazione in base all'uguaglianza del riferimento all'oggetto.</span><span class="sxs-lookup"><span data-stu-id="d299a-276">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="d299a-277">Identificatori univoci, ad esempio valori di chiave primaria di tipo `int` , `string` o `Guid` .</span><span class="sxs-lookup"><span data-stu-id="d299a-277">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="d299a-278">Verificare che i valori usati per non siano in `@key` conflitto.</span><span class="sxs-lookup"><span data-stu-id="d299a-278">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="d299a-279">Se vengono rilevati valori di conflitto nello stesso elemento padre, Blazor genera un'eccezione perché non è in grado di eseguire il mapping deterministico di elementi o componenti precedenti a nuovi elementi o componenti.</span><span class="sxs-lookup"><span data-stu-id="d299a-279">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="d299a-280">Utilizzare solo valori distinti, ad esempio istanze di oggetti o valori di chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="d299a-280">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a><span data-ttu-id="d299a-281">Non creare componenti che scrivono nelle proprie proprietà dei parametri</span><span class="sxs-lookup"><span data-stu-id="d299a-281">Don't create components that write to their own parameter properties</span></span>

<span data-ttu-id="d299a-282">I parametri vengono sovrascritti nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="d299a-282">Parameters are overwritten under the following conditions:</span></span>

* <span data-ttu-id="d299a-283">Viene eseguito il rendering del contenuto di un componente figlio con un oggetto `RenderFragment` .</span><span class="sxs-lookup"><span data-stu-id="d299a-283">A child component's content is rendered with a `RenderFragment`.</span></span>
* <span data-ttu-id="d299a-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>viene chiamato nel componente padre.</span><span class="sxs-lookup"><span data-stu-id="d299a-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent component.</span></span>

<span data-ttu-id="d299a-285">I parametri vengono reimpostati perché il componente padre esegue nuovamente il rendering quando <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> viene chiamato il metodo e vengono specificati nuovi valori di parametro per il componente figlio.</span><span class="sxs-lookup"><span data-stu-id="d299a-285">Parameters are reset because the parent component rerenders when <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called and new parameter values are supplied to the child component.</span></span>

<span data-ttu-id="d299a-286">Si consideri il `Expander` componente seguente:</span><span class="sxs-lookup"><span data-stu-id="d299a-286">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="d299a-287">Esegue il rendering del contenuto figlio.</span><span class="sxs-lookup"><span data-stu-id="d299a-287">Renders child content.</span></span>
* <span data-ttu-id="d299a-288">Consente di visualizzare o disabilitare il contenuto figlio con un parametro component.</span><span class="sxs-lookup"><span data-stu-id="d299a-288">Toggles showing child content with a component parameter.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @Expanded)

    @if (Expanded)
    {
        @ChildContent
    }
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

<span data-ttu-id="d299a-289">Il `Expander` componente viene aggiunto a un componente padre che può chiamare `StateHasChanged` :</span><span class="sxs-lookup"><span data-stu-id="d299a-289">The `Expander` component is added to a parent component that may call `StateHasChanged`:</span></span>

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

<span data-ttu-id="d299a-290">Inizialmente, i `Expander` componenti si comportano in modo indipendente quando le proprietà vengono disposte `Expanded` .</span><span class="sxs-lookup"><span data-stu-id="d299a-290">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="d299a-291">I componenti figlio gestiscono gli Stati come previsto.</span><span class="sxs-lookup"><span data-stu-id="d299a-291">The child components maintain their states as expected.</span></span> <span data-ttu-id="d299a-292">Quando `StateHasChanged` viene chiamato nell'elemento padre, il `Expanded` parametro del primo componente figlio viene reimpostato sul valore iniziale ( `true` ).</span><span class="sxs-lookup"><span data-stu-id="d299a-292">When `StateHasChanged` is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="d299a-293">Il valore del secondo `Expander` componente `Expanded` non viene reimpostato perché nel secondo componente non viene eseguito il rendering del contenuto figlio.</span><span class="sxs-lookup"><span data-stu-id="d299a-293">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="d299a-294">Per mantenere lo stato nello scenario precedente, usare un *campo privato* nel `Expander` componente per mantenerne lo stato attivato/disattivato.</span><span class="sxs-lookup"><span data-stu-id="d299a-294">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="d299a-295">Il `Expander` componente seguente:</span><span class="sxs-lookup"><span data-stu-id="d299a-295">The following `Expander` component:</span></span>

* <span data-ttu-id="d299a-296">Accetta il `Expanded` valore del parametro component dall'elemento padre.</span><span class="sxs-lookup"><span data-stu-id="d299a-296">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="d299a-297">Assegna il valore del parametro component a un *campo privato* ( `expanded` ) nell' [evento OnInitialized](xref:blazor/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="d299a-297">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="d299a-298">Usa il campo privato per mantenere lo stato di attivazione/disattivo interno.</span><span class="sxs-lookup"><span data-stu-id="d299a-298">Uses the private field to maintain its internal toggle state.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @expanded)

    @if (expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool expanded;

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

## <a name="partial-class-support"></a><span data-ttu-id="d299a-299">Supporto di classi parziali</span><span class="sxs-lookup"><span data-stu-id="d299a-299">Partial class support</span></span>

Razor<span data-ttu-id="d299a-300">i componenti vengono generati come classi parziali.</span><span class="sxs-lookup"><span data-stu-id="d299a-300"> components are generated as partial classes.</span></span> Razor<span data-ttu-id="d299a-301">i componenti vengono creati utilizzando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="d299a-301"> components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="d299a-302">Il codice C# è definito in un [`@code`](xref:mvc/views/razor#code) blocco con markup HTML e Razor codice in un unico file.</span><span class="sxs-lookup"><span data-stu-id="d299a-302">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="d299a-303">i modelli definiscono i Razor componenti usando questo approccio.</span><span class="sxs-lookup"><span data-stu-id="d299a-303"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="d299a-304">Il codice C# viene inserito in un file code-behind definito come classe parziale.</span><span class="sxs-lookup"><span data-stu-id="d299a-304">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="d299a-305">Nell'esempio seguente viene illustrato il `Counter` componente predefinito con un `@code` blocco in un'app generata da un Blazor modello.</span><span class="sxs-lookup"><span data-stu-id="d299a-305">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="d299a-306">Il markup HTML, Razor il codice e il codice C# si trovano nello stesso file:</span><span class="sxs-lookup"><span data-stu-id="d299a-306">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="d299a-307">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="d299a-307">*Counter.razor*:</span></span>

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

<span data-ttu-id="d299a-308">Il `Counter` componente può essere creato anche usando un file code-behind con una classe parziale:</span><span class="sxs-lookup"><span data-stu-id="d299a-308">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="d299a-309">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="d299a-309">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="d299a-310">*Counter.Razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="d299a-310">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
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

<span data-ttu-id="d299a-311">Aggiungere gli spazi dei nomi necessari al file di classe parziale in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="d299a-311">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="d299a-312">Gli spazi dei nomi tipici usati dai Razor componenti includono:</span><span class="sxs-lookup"><span data-stu-id="d299a-312">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="d299a-313">Specificare una classe di base</span><span class="sxs-lookup"><span data-stu-id="d299a-313">Specify a base class</span></span>

<span data-ttu-id="d299a-314">La [`@inherits`](xref:mvc/views/razor#inherits) direttiva può essere usata per specificare una classe di base per un componente.</span><span class="sxs-lookup"><span data-stu-id="d299a-314">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="d299a-315">Nell'esempio seguente viene illustrato come un componente può ereditare una classe base, `BlazorRocksBase` , per fornire le proprietà e i metodi del componente.</span><span class="sxs-lookup"><span data-stu-id="d299a-315">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="d299a-316">La classe base deve derivare da `ComponentBase` .</span><span class="sxs-lookup"><span data-stu-id="d299a-316">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="d299a-317">*Pages/BlazorRocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="d299a-317">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="d299a-318">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="d299a-318">*BlazorRocksBase.cs*:</span></span>

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

## <a name="specify-an-attribute"></a><span data-ttu-id="d299a-319">Specificare un attributo</span><span class="sxs-lookup"><span data-stu-id="d299a-319">Specify an attribute</span></span>

<span data-ttu-id="d299a-320">Gli attributi possono essere specificati in Razor componenti con la [`@attribute`](xref:mvc/views/razor#attribute) direttiva.</span><span class="sxs-lookup"><span data-stu-id="d299a-320">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="d299a-321">Nell'esempio seguente viene applicato l' `[Authorize]` attributo alla classe Component:</span><span class="sxs-lookup"><span data-stu-id="d299a-321">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="d299a-322">Importa componenti</span><span class="sxs-lookup"><span data-stu-id="d299a-322">Import components</span></span>

<span data-ttu-id="d299a-323">Lo spazio dei nomi di un componente creato con Razor è basato su (in ordine di priorità):</span><span class="sxs-lookup"><span data-stu-id="d299a-323">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="d299a-324">[`@namespace`](xref:mvc/views/razor#namespace)designazione nel Razor markup file (*. Razor*) ( `@namespace BlazorSample.MyNamespace` ).</span><span class="sxs-lookup"><span data-stu-id="d299a-324">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="d299a-325">Il progetto `RootNamespace` nel file di progetto ( `<RootNamespace>BlazorSample</RootNamespace>` ).</span><span class="sxs-lookup"><span data-stu-id="d299a-325">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="d299a-326">Il nome del progetto, tratto dal nome file del file di progetto (con*estensione csproj*), e il percorso dalla radice del progetto al componente.</span><span class="sxs-lookup"><span data-stu-id="d299a-326">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="d299a-327">Ad esempio, il Framework risolve *{Project root}/pages/index.Razor* (*BlazorSample. csproj*) nello spazio dei nomi `BlazorSample.Pages` .</span><span class="sxs-lookup"><span data-stu-id="d299a-327">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="d299a-328">I componenti seguono le regole di associazione dei nomi in C#.</span><span class="sxs-lookup"><span data-stu-id="d299a-328">Components follow C# name binding rules.</span></span> <span data-ttu-id="d299a-329">Per il `Index` componente in questo esempio, i componenti nell'ambito sono tutti componenti:</span><span class="sxs-lookup"><span data-stu-id="d299a-329">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="d299a-330">Nella stessa cartella, *pagine*.</span><span class="sxs-lookup"><span data-stu-id="d299a-330">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="d299a-331">Componenti nella radice del progetto che non specificano in modo esplicito uno spazio dei nomi diverso.</span><span class="sxs-lookup"><span data-stu-id="d299a-331">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="d299a-332">I componenti definiti in uno spazio dei nomi diverso vengono introdotti nell'ambito usando Razor la [`@using`](xref:mvc/views/razor#using) direttiva.</span><span class="sxs-lookup"><span data-stu-id="d299a-332">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="d299a-333">Se un altro componente, `NavMenu.razor` , è presente nella cartella *BlazorSample/Shared/* , il componente può essere utilizzato in `Index.razor` con l' `@using` istruzione seguente:</span><span class="sxs-lookup"><span data-stu-id="d299a-333">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="d299a-334">È anche possibile fare riferimento ai componenti usando i relativi nomi completi, che non richiedono la [`@using`](xref:mvc/views/razor#using) direttiva:</span><span class="sxs-lookup"><span data-stu-id="d299a-334">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="d299a-335">La `global::` qualificazione non è supportata.</span><span class="sxs-lookup"><span data-stu-id="d299a-335">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="d299a-336">L'importazione di componenti con `using` istruzioni con alias (ad esempio, `@using Foo = Bar` ) non è supportata.</span><span class="sxs-lookup"><span data-stu-id="d299a-336">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="d299a-337">I nomi parzialmente qualificati non sono supportati.</span><span class="sxs-lookup"><span data-stu-id="d299a-337">Partially qualified names aren't supported.</span></span> <span data-ttu-id="d299a-338">Ad esempio, l'aggiunta `@using BlazorSample` e il riferimento `NavMenu.razor` a non sono `<Shared.NavMenu></Shared.NavMenu>` supportate.</span><span class="sxs-lookup"><span data-stu-id="d299a-338">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="d299a-339">Attributi dell'elemento HTML condizionale</span><span class="sxs-lookup"><span data-stu-id="d299a-339">Conditional HTML element attributes</span></span>

<span data-ttu-id="d299a-340">Gli attributi degli elementi HTML vengono sottoposti a rendering in modo condizionale in base al valore .NET.</span><span class="sxs-lookup"><span data-stu-id="d299a-340">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="d299a-341">Se il valore è `false` o `null` , l'attributo non viene sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="d299a-341">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="d299a-342">Se il valore è `true` , l'attributo viene sottoposto a rendering ridotto a icona.</span><span class="sxs-lookup"><span data-stu-id="d299a-342">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="d299a-343">Nell'esempio seguente, `IsCompleted` determina se `checked` viene eseguito il rendering nel markup dell'elemento:</span><span class="sxs-lookup"><span data-stu-id="d299a-343">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="d299a-344">Se `IsCompleted` è `true` , viene eseguito il rendering della casella di controllo come segue:</span><span class="sxs-lookup"><span data-stu-id="d299a-344">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="d299a-345">Se `IsCompleted` è `false` , viene eseguito il rendering della casella di controllo come segue:</span><span class="sxs-lookup"><span data-stu-id="d299a-345">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="d299a-346">Per altre informazioni, vedere <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="d299a-346">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="d299a-347">Alcuni attributi HTML, ad esempio [aria-Pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), non funzionano correttamente quando il tipo .NET è `bool` .</span><span class="sxs-lookup"><span data-stu-id="d299a-347">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="d299a-348">In questi casi, usare un `string` tipo anziché un `bool` .</span><span class="sxs-lookup"><span data-stu-id="d299a-348">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="d299a-349">HTML non elaborato</span><span class="sxs-lookup"><span data-stu-id="d299a-349">Raw HTML</span></span>

<span data-ttu-id="d299a-350">Le stringhe vengono in genere sottoposte a rendering usando i nodi di testo DOM, il che significa che qualsiasi markup che può contenere viene ignorato e considerato come testo letterale.</span><span class="sxs-lookup"><span data-stu-id="d299a-350">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="d299a-351">Per eseguire il rendering del codice HTML non elaborato, eseguire il wrapping del contenuto HTML in un `MarkupString` valore.</span><span class="sxs-lookup"><span data-stu-id="d299a-351">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="d299a-352">Il valore viene analizzato in formato HTML o SVG e inserito nel DOM.</span><span class="sxs-lookup"><span data-stu-id="d299a-352">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="d299a-353">Il rendering di codice HTML non elaborato costruito da un'origine non attendibile costituisce un rischio per la **sicurezza** e deve essere evitato.</span><span class="sxs-lookup"><span data-stu-id="d299a-353">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="d299a-354">Nell'esempio seguente viene illustrato l'utilizzo del `MarkupString` tipo per aggiungere un blocco di contenuto HTML statico all'output sottoposto a rendering di un componente:</span><span class="sxs-lookup"><span data-stu-id="d299a-354">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="d299a-355">Parametri e valori di propagazione</span><span class="sxs-lookup"><span data-stu-id="d299a-355">Cascading values and parameters</span></span>

<span data-ttu-id="d299a-356">In alcuni scenari, non è pratico eseguire il flusso dei dati da un componente predecessore a un componente discendente usando i [parametri del componente](#component-parameters), soprattutto quando sono presenti diversi livelli di componenti.</span><span class="sxs-lookup"><span data-stu-id="d299a-356">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="d299a-357">I valori e i parametri di propagazione consentono di risolvere questo problema fornendo un modo pratico per un componente predecessore per fornire un valore a tutti i relativi componenti discendenti.</span><span class="sxs-lookup"><span data-stu-id="d299a-357">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="d299a-358">I parametri e i valori di propagazione forniscono anche un approccio per la coordinazione dei componenti.</span><span class="sxs-lookup"><span data-stu-id="d299a-358">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="d299a-359">Esempio di tema</span><span class="sxs-lookup"><span data-stu-id="d299a-359">Theme example</span></span>

<span data-ttu-id="d299a-360">Nell'esempio seguente dall'app di esempio, la `ThemeInfo` classe specifica le informazioni sul tema per scorrere la gerarchia dei componenti in modo che tutti i pulsanti all'interno di una determinata parte dell'app condividono lo stesso stile.</span><span class="sxs-lookup"><span data-stu-id="d299a-360">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="d299a-361">*UIThemeClasses/themeinfo. cs*:</span><span class="sxs-lookup"><span data-stu-id="d299a-361">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="d299a-362">Un componente predecessore può fornire un valore di propagazione utilizzando il componente valore di propagazione.</span><span class="sxs-lookup"><span data-stu-id="d299a-362">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="d299a-363">Il `CascadingValue` componente esegue il wrapping di un sottoalbero della gerarchia dei componenti e fornisce un singolo valore a tutti i componenti all'interno di tale sottoalbero.</span><span class="sxs-lookup"><span data-stu-id="d299a-363">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="d299a-364">Ad esempio, l'app di esempio specifica le informazioni sul tema ( `ThemeInfo` ) in uno dei layout dell'app come parametro di propagazione per tutti i componenti che costituiscono il corpo del layout della `@Body` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="d299a-364">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="d299a-365">`ButtonClass`viene assegnato un valore di `btn-success` nel componente layout.</span><span class="sxs-lookup"><span data-stu-id="d299a-365">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="d299a-366">Qualsiasi componente discendente può utilizzare questa proprietà tramite l' `ThemeInfo` oggetto a cascata.</span><span class="sxs-lookup"><span data-stu-id="d299a-366">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="d299a-367">`CascadingValuesParametersLayout`componente</span><span class="sxs-lookup"><span data-stu-id="d299a-367">`CascadingValuesParametersLayout` component:</span></span>

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

<span data-ttu-id="d299a-368">Per utilizzare i valori di propagazione, i componenti dichiarano i parametri di propagazione utilizzando l' `[CascadingParameter]` attributo.</span><span class="sxs-lookup"><span data-stu-id="d299a-368">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="d299a-369">I valori a cascata vengono associati ai parametri di propagazione per tipo.</span><span class="sxs-lookup"><span data-stu-id="d299a-369">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="d299a-370">Nell'app di esempio, il `CascadingValuesParametersTheme` componente associa il `ThemeInfo` valore a cascata a un parametro di propagazione.</span><span class="sxs-lookup"><span data-stu-id="d299a-370">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="d299a-371">Il parametro viene usato per impostare la classe CSS per uno dei pulsanti visualizzati dal componente.</span><span class="sxs-lookup"><span data-stu-id="d299a-371">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="d299a-372">`CascadingValuesParametersTheme`componente</span><span class="sxs-lookup"><span data-stu-id="d299a-372">`CascadingValuesParametersTheme` component:</span></span>

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

<span data-ttu-id="d299a-373">Per eseguire il propagazione di più valori dello stesso tipo all'interno dello stesso sottoalbero, fornire una stringa univoca `Name` a ogni `CascadingValue` componente e la corrispondente `CascadingParameter` .</span><span class="sxs-lookup"><span data-stu-id="d299a-373">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="d299a-374">Nell'esempio seguente due componenti propagano `CascadingValue` istanze diverse di `MyCascadingType` per nome:</span><span class="sxs-lookup"><span data-stu-id="d299a-374">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

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

<span data-ttu-id="d299a-375">In un componente discendente i parametri a cascata ricevono i rispettivi valori dai valori a cascata corrispondenti nel componente predecessore per nome:</span><span class="sxs-lookup"><span data-stu-id="d299a-375">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="d299a-376">Esempio di tabulazione</span><span class="sxs-lookup"><span data-stu-id="d299a-376">TabSet example</span></span>

<span data-ttu-id="d299a-377">I parametri di propagazione consentono inoltre ai componenti di collaborare attraverso la gerarchia dei componenti.</span><span class="sxs-lookup"><span data-stu-id="d299a-377">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="d299a-378">Si consideri, ad esempio, l'esempio di *tabulazione* seguente nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="d299a-378">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="d299a-379">L'app di esempio ha un' `ITab` interfaccia implementata dalle schede:</span><span class="sxs-lookup"><span data-stu-id="d299a-379">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="d299a-380">Il `CascadingValuesParametersTabSet` componente usa il `TabSet` componente, che contiene diversi `Tab` componenti:</span><span class="sxs-lookup"><span data-stu-id="d299a-380">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

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

<span data-ttu-id="d299a-381">I `Tab` componenti figlio non vengono passati in modo esplicito come parametri a `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="d299a-381">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="d299a-382">Al contrario, i `Tab` componenti figlio fanno parte del contenuto figlio di `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="d299a-382">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="d299a-383">Tuttavia, `TabSet` è comunque necessario conoscere ogni componente in `Tab` modo che sia in grado di eseguire il rendering delle intestazioni e della scheda attiva. Per abilitare questo coordinamento senza richiedere codice aggiuntivo, il `TabSet` componente *può fornire se stesso come valore* di propagazione che viene quindi prelevato dai `Tab` componenti discendenti.</span><span class="sxs-lookup"><span data-stu-id="d299a-383">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="d299a-384">`TabSet`componente</span><span class="sxs-lookup"><span data-stu-id="d299a-384">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="d299a-385">I componenti discendenti `Tab` acquisiscono l'oggetto che contiene `TabSet` come parametro di propagazione, in modo `Tab` che i componenti si aggiungano alla `TabSet` coordinata e della scheda attiva.</span><span class="sxs-lookup"><span data-stu-id="d299a-385">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="d299a-386">`Tab`componente</span><span class="sxs-lookup"><span data-stu-id="d299a-386">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor<span data-ttu-id="d299a-387">modelli</span><span class="sxs-lookup"><span data-stu-id="d299a-387"> templates</span></span>

<span data-ttu-id="d299a-388">I frammenti di rendering possono essere definiti usando la Razor sintassi del modello.</span><span class="sxs-lookup"><span data-stu-id="d299a-388">Render fragments can be defined using Razor template syntax.</span></span> Razor<span data-ttu-id="d299a-389">i modelli sono un modo per definire un frammento di interfaccia utente e presupporre il formato seguente:</span><span class="sxs-lookup"><span data-stu-id="d299a-389"> templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="d299a-390">Nell'esempio seguente viene illustrato come specificare `RenderFragment` `RenderFragment<T>` i valori e ed eseguire il rendering dei modelli direttamente in un componente.</span><span class="sxs-lookup"><span data-stu-id="d299a-390">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="d299a-391">I frammenti di rendering possono anche essere passati come argomenti ai [componenti basati su modelli](xref:blazor/templated-components).</span><span class="sxs-lookup"><span data-stu-id="d299a-391">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

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

<span data-ttu-id="d299a-392">Output del codice precedente sottoposto a rendering:</span><span class="sxs-lookup"><span data-stu-id="d299a-392">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="d299a-393">Immagini SVG (Scalable Vector Graphics)</span><span class="sxs-lookup"><span data-stu-id="d299a-393">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="d299a-394">Poiché Blazor esegue il rendering del codice HTML, le immagini supportate dal browser, incluse le immagini SVG (Scalable Vector Graphics) (con*estensione SVG*), sono supportate tramite il `<img>` Tag:</span><span class="sxs-lookup"><span data-stu-id="d299a-394">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="d299a-395">Analogamente, le immagini SVG sono supportate nelle regole CSS di un file di foglio di stile (*CSS*):</span><span class="sxs-lookup"><span data-stu-id="d299a-395">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="d299a-396">Tuttavia, il markup SVG inline non è supportato in tutti gli scenari.</span><span class="sxs-lookup"><span data-stu-id="d299a-396">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="d299a-397">Se si inserisce un `<svg>` tag direttamente in un file di componente (*Razor*), il rendering delle immagini di base è supportato, ma molti scenari avanzati non sono ancora supportati.</span><span class="sxs-lookup"><span data-stu-id="d299a-397">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="d299a-398">Ad esempio, i `<use>` tag non sono attualmente rispettati e `@bind` non possono essere usati con alcuni tag svg.</span><span class="sxs-lookup"><span data-stu-id="d299a-398">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="d299a-399">Queste limitazioni verranno affrontate in una versione futura.</span><span class="sxs-lookup"><span data-stu-id="d299a-399">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d299a-400">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="d299a-400">Additional resources</span></span>

* <span data-ttu-id="d299a-401"><xref:security/blazor/server/threat-mitigation>&ndash;Include informazioni aggiuntive sulla compilazione Blazor App server che devono essere confrontate con l'esaurimento delle risorse.</span><span class="sxs-lookup"><span data-stu-id="d299a-401"><xref:security/blazor/server/threat-mitigation> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
