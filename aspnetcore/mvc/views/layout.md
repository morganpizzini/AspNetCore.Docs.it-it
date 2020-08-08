---
title: Layout in ASP.NET Core
author: ardalis
description: Informazioni su come usare layout comuni, condividere direttive ed eseguire codice comune prima di eseguire il rendering delle visualizzazioni in un'applicazione ASP.NET Core.
ms.author: riande
ms.date: 07/30/2019
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
uid: mvc/views/layout
ms.openlocfilehash: 4d5032f02db28341d7781dd57d58d776636fd16d
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020444"
---
# <a name="layout-in-aspnet-core"></a><span data-ttu-id="56936-103">Layout in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="56936-103">Layout in ASP.NET Core</span></span>

<span data-ttu-id="56936-104">Di [Steve Smith](https://ardalis.com/) e [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="56936-104">By [Steve Smith](https://ardalis.com/) and [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="56936-105">Le pagine e le visualizzazioni condividono spesso elementi visivi e programmatici.</span><span class="sxs-lookup"><span data-stu-id="56936-105">Pages and views frequently share visual and programmatic elements.</span></span> <span data-ttu-id="56936-106">Questo articolo illustra come:</span><span class="sxs-lookup"><span data-stu-id="56936-106">This article demonstrates how to:</span></span>

* <span data-ttu-id="56936-107">Usare layout comuni.</span><span class="sxs-lookup"><span data-stu-id="56936-107">Use common layouts.</span></span>
* <span data-ttu-id="56936-108">Condividere direttive.</span><span class="sxs-lookup"><span data-stu-id="56936-108">Share directives.</span></span>
* <span data-ttu-id="56936-109">Eseguire codice comune prima del rendering di pagine o visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="56936-109">Run common code before rendering pages or views.</span></span>

<span data-ttu-id="56936-110">Questo documento illustra i layout per i due diversi approcci per ASP.NET Core MVC: Razor pagine e controller con le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="56936-110">This document discusses layouts for the two different approaches to ASP.NET Core MVC: Razor Pages and controllers with views.</span></span> <span data-ttu-id="56936-111">Per questo argomento le differenze siano minime:</span><span class="sxs-lookup"><span data-stu-id="56936-111">For this topic, the differences are minimal:</span></span>

* <span data-ttu-id="56936-112">RazorLe pagine si trovano nella cartella *pages* .</span><span class="sxs-lookup"><span data-stu-id="56936-112">Razor Pages are in the *Pages* folder.</span></span>
* <span data-ttu-id="56936-113">I controller con visualizzazioni usano una cartella *Views* per le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="56936-113">Controllers with views uses a *Views* folder for views.</span></span>

## <a name="what-is-a-layout"></a><span data-ttu-id="56936-114">Che cos'è il layout?</span><span class="sxs-lookup"><span data-stu-id="56936-114">What is a Layout</span></span>

<span data-ttu-id="56936-115">La maggior parte delle applicazioni web presenta un layout comune che fornisce all'utente un'esperienza omogenea, nel passare da una pagina a un'altra.</span><span class="sxs-lookup"><span data-stu-id="56936-115">Most web apps have a common layout that provides the user with a consistent experience as they navigate from page to page.</span></span> <span data-ttu-id="56936-116">In genere, il layout comprende elementi dell'interfaccia utente comune, ad esempio l'intestazione dell'app, la navigazione o elementi di menu e piè di pagina.</span><span class="sxs-lookup"><span data-stu-id="56936-116">The layout typically includes common user interface elements such as the app header, navigation or menu elements, and footer.</span></span>

![Esempio di layout di pagina](layout/_static/page-layout.png)

<span data-ttu-id="56936-118">Anche le strutture HTML comuni, ad esempio gli script e i fogli di stile, vengono spesso usate da molte pagine all'interno di un'app.</span><span class="sxs-lookup"><span data-stu-id="56936-118">Common HTML structures such as scripts and stylesheets are also frequently used by many pages within an app.</span></span> <span data-ttu-id="56936-119">Tutti questi elementi condivisi possono essere definiti in un file di *layout* , a cui è possibile fare riferimento da qualsiasi visualizzazione usata nell'app.</span><span class="sxs-lookup"><span data-stu-id="56936-119">All of these shared elements may be defined in a *layout* file, which can then be referenced by any view used within the app.</span></span> <span data-ttu-id="56936-120">I layout riducono il codice duplicato nelle visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="56936-120">Layouts reduce duplicate code in views.</span></span>

<span data-ttu-id="56936-121">Per convenzione, il layout predefinito per un'app ASP.NET Core è denominato *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="56936-121">By convention, the default layout for an ASP.NET Core app is named *_Layout.cshtml*.</span></span> <span data-ttu-id="56936-122">I file di layout per i nuovi progetti ASP.NET Core creati con i modelli sono:</span><span class="sxs-lookup"><span data-stu-id="56936-122">The layout files for new ASP.NET Core projects created with the templates are:</span></span>

* <span data-ttu-id="56936-123">RazorPagine: *pagine/condivise/_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="56936-123">Razor Pages: *Pages/Shared/_Layout.cshtml*</span></span>

  ![Cartella Pages in Esplora soluzioni](layout/_static/rp-web-project-views.png)

* <span data-ttu-id="56936-125">Controller con visualizzazioni: *Views/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="56936-125">Controller with views: *Views/Shared/_Layout.cshtml*</span></span>

  ![Cartella Views in Esplora soluzioni](layout/_static/mvc-web-project-views.png)

<span data-ttu-id="56936-127">Il layout definisce un modello di primo livello per le visualizzazioni nell'app.</span><span class="sxs-lookup"><span data-stu-id="56936-127">The layout defines a top level template for views in the app.</span></span> <span data-ttu-id="56936-128">Le app non richiedono un layout.</span><span class="sxs-lookup"><span data-stu-id="56936-128">Apps don't require a layout.</span></span> <span data-ttu-id="56936-129">Le app possono definire più di un layout, con visualizzazioni diverse che specificano layout diversi.</span><span class="sxs-lookup"><span data-stu-id="56936-129">Apps can define more than one layout, with different views specifying different layouts.</span></span>

<span data-ttu-id="56936-130">Il codice seguente mostra il file di layout per un progetto creato da modello con un controller e visualizzazioni:</span><span class="sxs-lookup"><span data-stu-id="56936-130">The following code shows the layout file for a template created project with a controller and views:</span></span>

[!code-cshtml[](~/common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=44,72)]

## <a name="specifying-a-layout"></a><span data-ttu-id="56936-131">Definizione di un layout</span><span class="sxs-lookup"><span data-stu-id="56936-131">Specifying a Layout</span></span>

<span data-ttu-id="56936-132">Razorle visualizzazioni hanno una `Layout` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="56936-132">Razor views have a `Layout` property.</span></span> <span data-ttu-id="56936-133">Le visualizzazioni singole specificano un layout impostando la seguente proprietà:</span><span class="sxs-lookup"><span data-stu-id="56936-133">Individual views specify a layout by setting this property:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

<span data-ttu-id="56936-134">Il layout specificato può usare un percorso completo (ad esempio, */Pages/Shared/_Layout.cshtml* o */Views/Shared/_Layout.cshtml*) oppure un nome parziale (ad esempio: `_Layout`).</span><span class="sxs-lookup"><span data-stu-id="56936-134">The layout specified can use a full path (for example, */Pages/Shared/_Layout.cshtml* or */Views/Shared/_Layout.cshtml*) or a partial name (example: `_Layout`).</span></span> <span data-ttu-id="56936-135">Quando viene specificato un nome parziale, il Razor motore di visualizzazione Cerca il file di layout utilizzando il processo di individuazione standard.</span><span class="sxs-lookup"><span data-stu-id="56936-135">When a partial name is provided, the Razor view engine searches for the layout file using its standard discovery process.</span></span> <span data-ttu-id="56936-136">La ricerca viene eseguita prima nella cartella in cui è presente il metodo gestore (o controller) e poi nella cartella *Shared*.</span><span class="sxs-lookup"><span data-stu-id="56936-136">The folder where the handler method (or controller) exists is searched first, followed by the *Shared* folder.</span></span> <span data-ttu-id="56936-137">Questo processo di individuazione è identico a quello usato per individuare le [visualizzazioni parziali](xref:mvc/views/partial#partial-view-discovery).</span><span class="sxs-lookup"><span data-stu-id="56936-137">This discovery process is identical to the process used to discover [partial views](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="56936-138">Per impostazione predefinita, ogni layout deve chiamare `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="56936-138">By default, every layout must call `RenderBody`.</span></span> <span data-ttu-id="56936-139">Quando viene eseguita la chiamata a `RenderBody`, viene eseguito il rendering del contenuto della visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="56936-139">Wherever the call to `RenderBody` is placed, the contents of the view will be rendered.</span></span>

<a name="layout-sections-label"></a>
<!-- https://stackoverflow.com/questions/23327578 -->
### <a name="sections"></a><span data-ttu-id="56936-140">Sezioni</span><span class="sxs-lookup"><span data-stu-id="56936-140">Sections</span></span>

<span data-ttu-id="56936-141">Un layout può facoltativamente fare riferimento a una o più *sezioni*, chiamando `RenderSection`.</span><span class="sxs-lookup"><span data-stu-id="56936-141">A layout can optionally reference one or more *sections*, by calling `RenderSection`.</span></span> <span data-ttu-id="56936-142">Le sezioni forniscono un modo per organizzare la posizione in cui devono essere inseriti determinati elementi di pagina.</span><span class="sxs-lookup"><span data-stu-id="56936-142">Sections provide a way to organize where certain page elements should be placed.</span></span> <span data-ttu-id="56936-143">Ogni chiamata a `RenderSection` può specificare se tale sezione è obbligatoria o facoltativa:</span><span class="sxs-lookup"><span data-stu-id="56936-143">Each call to `RenderSection` can specify whether that section is required or optional:</span></span>

```html
<script type="text/javascript" src="~/scripts/global.js"></script>

@RenderSection("Scripts", required: false)
```

<span data-ttu-id="56936-144">Se una sezione richiesta non viene trovata, viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="56936-144">If a required section isn't found, an exception is thrown.</span></span> <span data-ttu-id="56936-145">Le singole visualizzazioni specificano il contenuto di cui eseguire il rendering all'interno di una sezione usando la `@section` Razor sintassi.</span><span class="sxs-lookup"><span data-stu-id="56936-145">Individual views specify the content to be rendered within a section using the `@section` Razor syntax.</span></span> <span data-ttu-id="56936-146">Se una pagina o una visualizzazione definisce una sezione, è necessario eseguirne il rendering (o si verifica un errore).</span><span class="sxs-lookup"><span data-stu-id="56936-146">If a page or view defines a section, it must be rendered (or an error will occur).</span></span>

<span data-ttu-id="56936-147">Una `@section` definizione di esempio nella Razor visualizzazione pagine:</span><span class="sxs-lookup"><span data-stu-id="56936-147">An example `@section` definition in Razor Pages view:</span></span>

```html
@section Scripts {
     <script type="text/javascript" src="~/scripts/main.js"></script>
}
```

<span data-ttu-id="56936-148">Nel codice precedente, *scripts/main.js* viene aggiunto alla sezione `scripts` in una pagina o visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="56936-148">In the preceding code, *scripts/main.js* is added to the `scripts` section on a page or view.</span></span> <span data-ttu-id="56936-149">Altre pagine o visualizzazioni nella stessa app potrebbero non richiedere questo script e non definire una sezione scripts.</span><span class="sxs-lookup"><span data-stu-id="56936-149">Other pages or views in the same app might not require this script and wouldn't define a scripts section.</span></span>

<span data-ttu-id="56936-150">Il markup seguente usa l'[helper tag parziale](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) per eseguire il rendering di *_ValidationScriptsPartial.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="56936-150">The following markup uses the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) to render  *_ValidationScriptsPartial.cshtml*:</span></span>

```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

<span data-ttu-id="56936-151">Il markup precedente è stato generato dall' [impalcatura Identity ](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="56936-151">The preceding markup was generated by [scaffolding Identity](xref:security/authentication/scaffold-identity).</span></span>

<span data-ttu-id="56936-152">Le sezioni definite in una pagina o in una visualizzazione sono disponibili solo nella relativa pagina di layout immediato.</span><span class="sxs-lookup"><span data-stu-id="56936-152">Sections defined in a page or view are available only in its immediate layout page.</span></span> <span data-ttu-id="56936-153">Non è possibile farvi riferimento da righe parzialmente eseguite, componenti di visualizzazione o altre parti del sistema di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="56936-153">They cannot be referenced from partials, view components, or other parts of the view system.</span></span>

### <a name="ignoring-sections"></a><span data-ttu-id="56936-154">Esclusione di sezioni</span><span class="sxs-lookup"><span data-stu-id="56936-154">Ignoring sections</span></span>

<span data-ttu-id="56936-155">Per impostazione predefinita, la pagina di layout deve eseguire il rendering della parte principale e di tutte le sezioni di una pagina di contenuto.</span><span class="sxs-lookup"><span data-stu-id="56936-155">By default, the body and all sections in a content page must all be rendered by the layout page.</span></span> <span data-ttu-id="56936-156">Il Razor motore di visualizzazione impone questo oggetto tenendo traccia dell'eventuale rendering del corpo e di ogni sezione.</span><span class="sxs-lookup"><span data-stu-id="56936-156">The Razor view engine enforces this by tracking whether the body and each section have been rendered.</span></span>

<span data-ttu-id="56936-157">Per indicare al motore di visualizzazione di escludere la parte principale o le sezioni, chiamare i metodi `IgnoreBody` e `IgnoreSection`.</span><span class="sxs-lookup"><span data-stu-id="56936-157">To instruct the view engine to ignore the body or sections, call the `IgnoreBody` and `IgnoreSection` methods.</span></span>

<span data-ttu-id="56936-158">Il corpo e ogni sezione di una Razor pagina devono essere sottoposti a rendering o ignorati.</span><span class="sxs-lookup"><span data-stu-id="56936-158">The body and every section in a Razor page must be either rendered or ignored.</span></span>

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a><span data-ttu-id="56936-159">Importazione delle direttive condivise</span><span class="sxs-lookup"><span data-stu-id="56936-159">Importing Shared Directives</span></span>

<span data-ttu-id="56936-160">Le viste e le pagine possono utilizzare Razor le direttive per importare gli spazi dei nomi e utilizzare l' [inserimento delle dipendenze](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="56936-160">Views and pages can use Razor directives to import namespaces and use [dependency injection](dependency-injection.md).</span></span> <span data-ttu-id="56936-161">Le direttive condivise da numerose visualizzazioni possono essere specificate in un file *_ViewImports.cshtml* comune.</span><span class="sxs-lookup"><span data-stu-id="56936-161">Directives shared by many views may be specified in a common *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="56936-162">Il file `_ViewImports` supporta le direttive seguenti:</span><span class="sxs-lookup"><span data-stu-id="56936-162">The `_ViewImports` file supports the following directives:</span></span>

* `@addTagHelper`
* `@removeTagHelper`
* `@tagHelperPrefix`
* `@using`
* `@model`
* `@inherits`
* `@inject`

<span data-ttu-id="56936-163">Il file non supporta altre Razor funzionalità, ad esempio le funzioni e le definizioni delle sezioni.</span><span class="sxs-lookup"><span data-stu-id="56936-163">The file doesn't support other Razor features, such as functions and section definitions.</span></span>

<span data-ttu-id="56936-164">Esempio di file `_ViewImports.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="56936-164">A sample `_ViewImports.cshtml` file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

<span data-ttu-id="56936-165">Il file *_ViewImports.cshtml* per un'app ASP.NET Core MVC viene generalmente posizionato nella cartella *Pages* (o *Views*).</span><span class="sxs-lookup"><span data-stu-id="56936-165">The *_ViewImports.cshtml* file for an ASP.NET Core MVC app is typically placed in the *Pages* (or *Views*) folder.</span></span> <span data-ttu-id="56936-166">È possibile posizionare un file *_ViewImports.cshtml* in qualsiasi cartella, nel qual caso verrà applicato unicamente alle pagine o alle visualizzazioni all'interno di tale cartella e nelle relative sottocartelle.</span><span class="sxs-lookup"><span data-stu-id="56936-166">A *_ViewImports.cshtml* file can be placed within any folder, in which case it will only be applied to pages or views within that folder and its subfolders.</span></span> <span data-ttu-id="56936-167">I file `_ViewImports` vengono elaborati a partire dal livello radice e quindi per ogni cartella fino alla posizione della pagina o della visualizzazione stessa.</span><span class="sxs-lookup"><span data-stu-id="56936-167">`_ViewImports` files are processed starting at the root level and then for each folder leading up to the location of the page or view itself.</span></span> <span data-ttu-id="56936-168">Le impostazioni `_ViewImports` specificate al livello radice possono essere sottoposto a override a livello di cartella.</span><span class="sxs-lookup"><span data-stu-id="56936-168">`_ViewImports` settings specified at the root level may be overridden at the folder level.</span></span>

<span data-ttu-id="56936-169">Si supponga ad esempio che:</span><span class="sxs-lookup"><span data-stu-id="56936-169">For example, suppose:</span></span>

* <span data-ttu-id="56936-170">Il file *_ViewImports.cshtml* al livello radice includa `@model MyModel1` e `@addTagHelper *, MyTagHelper1`.</span><span class="sxs-lookup"><span data-stu-id="56936-170">The  root level *_ViewImports.cshtml* file includes `@model MyModel1` and `@addTagHelper *, MyTagHelper1`.</span></span>
* <span data-ttu-id="56936-171">Un file *_ViewImports.cshtml* in una sottocartella includa `@model MyModel2` e `@addTagHelper *, MyTagHelper2`.</span><span class="sxs-lookup"><span data-stu-id="56936-171">A subfolder  *_ViewImports.cshtml* file includes `@model MyModel2` and `@addTagHelper *, MyTagHelper2`.</span></span>

<span data-ttu-id="56936-172">Le pagine e le visualizzazioni nella sottocartella avranno accesso sia agli helper tag che al modello `MyModel2`.</span><span class="sxs-lookup"><span data-stu-id="56936-172">Pages and views in the subfolder will have access to both Tag Helpers and the `MyModel2` model.</span></span>

<span data-ttu-id="56936-173">Se vengono trovati più file *_ViewImports.cshtml* nella gerarchia di file, il comportamento combinato delle direttive è il seguente:</span><span class="sxs-lookup"><span data-stu-id="56936-173">If multiple *_ViewImports.cshtml* files are found in the file hierarchy, the combined behavior of the directives are:</span></span>

* <span data-ttu-id="56936-174">`@addTagHelper`, `@removeTagHelper`: eseguiti nell'ordine</span><span class="sxs-lookup"><span data-stu-id="56936-174">`@addTagHelper`, `@removeTagHelper`: all run, in order</span></span>
* <span data-ttu-id="56936-175">`@tagHelperPrefix`: il file più vicino alla visualizzazione esegue l'override di tutti gli altri</span><span class="sxs-lookup"><span data-stu-id="56936-175">`@tagHelperPrefix`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="56936-176">`@model`: il file più vicino alla visualizzazione esegue l'override di tutti gli altri</span><span class="sxs-lookup"><span data-stu-id="56936-176">`@model`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="56936-177">`@inherits`: il file più vicino alla visualizzazione esegue l'override di tutti gli altri</span><span class="sxs-lookup"><span data-stu-id="56936-177">`@inherits`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="56936-178">`@using`: vengono inclusi tutti; i duplicati vengono esclusi</span><span class="sxs-lookup"><span data-stu-id="56936-178">`@using`: all are included; duplicates are ignored</span></span>
* <span data-ttu-id="56936-179">`@inject`: per ogni proprietà, quella più vicina alla visualizzazione esegue l'override di tutte le altre con lo stesso nome di proprietà</span><span class="sxs-lookup"><span data-stu-id="56936-179">`@inject`: for each property, the closest one to the view overrides any others with the same property name</span></span>

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a><span data-ttu-id="56936-180">Esecuzione di codice prima di ogni visualizzazione</span><span class="sxs-lookup"><span data-stu-id="56936-180">Running Code Before Each View</span></span>

<span data-ttu-id="56936-181">Il codice che deve essere eseguito prima di ogni visualizzazione o pagina deve essere posizionato nel file *_ViewStart.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="56936-181">Code that needs to run before each view or page should be placed in the *_ViewStart.cshtml* file.</span></span> <span data-ttu-id="56936-182">Per convenzione, il file *_ViewStart.cshtml* si trova nella cartella *Pages* (o *Views*).</span><span class="sxs-lookup"><span data-stu-id="56936-182">By convention, the *_ViewStart.cshtml* file is located in the *Pages* (or *Views*) folder.</span></span> <span data-ttu-id="56936-183">Le istruzioni elencate in *_ViewStart.cshtml* vengono eseguite prima di ogni visualizzazione completa (non dei layout e delle visualizzazioni parziali).</span><span class="sxs-lookup"><span data-stu-id="56936-183">The statements listed in *_ViewStart.cshtml* are run before every full view (not layouts, and not partial views).</span></span> <span data-ttu-id="56936-184">Come [ViewImports.cshtml](xref:mvc/views/layout#viewimports), *_ViewStart.cshtml* è gerarchico.</span><span class="sxs-lookup"><span data-stu-id="56936-184">Like [ViewImports.cshtml](xref:mvc/views/layout#viewimports), *_ViewStart.cshtml* is hierarchical.</span></span> <span data-ttu-id="56936-185">Se un file *_ViewStart.cshtml* viene definito nella cartella delle visualizzazioni o delle pagine, verrà eseguito dopo quello definito nella radice della cartella *Pages* (o *Views*) (se presente).</span><span class="sxs-lookup"><span data-stu-id="56936-185">If a *_ViewStart.cshtml* file is defined in the view or pages folder, it will be run after the one defined in the root of the *Pages* (or *Views*) folder (if any).</span></span>

<span data-ttu-id="56936-186">File *_ViewStart.cshtml* di esempio:</span><span class="sxs-lookup"><span data-stu-id="56936-186">A sample *_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

<span data-ttu-id="56936-187">Il file precedente specifica che tutte le visualizzazioni useranno il layout *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="56936-187">The file above specifies that all views will use the *_Layout.cshtml* layout.</span></span>

<span data-ttu-id="56936-188">*_ViewStart.cshtml* e *_ViewImports.cshtml\*\*\*non*\* vengono in genere posizionati nella cartella */Pages/Shared* (o */Views/Shared*).</span><span class="sxs-lookup"><span data-stu-id="56936-188">*_ViewStart.cshtml* and *_ViewImports.cshtml* are **not** typically placed in the */Pages/Shared* (or */Views/Shared*) folder.</span></span> <span data-ttu-id="56936-189">Le versioni a livello di app di questi file devono essere posizionate direttamente nella cartella */Pages* (o */Views*).</span><span class="sxs-lookup"><span data-stu-id="56936-189">The app-level versions of these files should be placed directly in the */Pages* (or */Views*) folder.</span></span>
