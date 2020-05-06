---
title: Razor Pagine con impalcatura in ASP.NET Core
author: rick-anderson
description: Illustra le Razor pagine generate dall'impalcatura.
ms.author: riande
ms.date: 08/17/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/page
ms.openlocfilehash: 22afbc729cc73427b3d04bee379534cda38b39bd
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774847"
---
# <a name="scaffolded-razor-pages-in-aspnet-core"></a><span data-ttu-id="08893-103">Pagine Razor create in ASP.NET Core tramite scaffolding</span><span class="sxs-lookup"><span data-stu-id="08893-103">Scaffolded Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="08893-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="08893-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="08893-105">Questa esercitazione esamina i Razor Pages creati dall'impalcatura nell' [esercitazione precedente](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="08893-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="08893-106">Pagine di creazione, eliminazione, dettagli e modifica</span><span class="sxs-lookup"><span data-stu-id="08893-106">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="08893-107">Esaminare il modello di pagina *Pages/Movies/Index.cshtml.cs*: </span><span class="sxs-lookup"><span data-stu-id="08893-107">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="08893-108">Le pagine Razor vengono derivate da `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="08893-108">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="08893-109">Per convenzione, la classe derivata `PageModel` viene denominata `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="08893-109">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="08893-110">Il costruttore usa l'[inserimento delle dipendenze](xref:fundamentals/dependency-injection) per aggiungere `RazorPagesMovieContext` alla pagina.</span><span class="sxs-lookup"><span data-stu-id="08893-110">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="08893-111">Tutte le pagine create tramite scaffolding seguono questo schema.</span><span class="sxs-lookup"><span data-stu-id="08893-111">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="08893-112">Vedere [Codice asincrono](xref:data/ef-rp/intro#asynchronous-code) per altre informazioni sulla programmazione asincrona con Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="08893-112">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="08893-113">Quando per la pagina viene eseguita una richiesta, il metodo `OnGetAsync` restituisce un elenco di filmati alla pagina Razor.</span><span class="sxs-lookup"><span data-stu-id="08893-113">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="08893-114">`OnGetAsync`viene `OnGet` chiamato o per inizializzare lo stato della pagina.</span><span class="sxs-lookup"><span data-stu-id="08893-114">`OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="08893-115">In questo caso, `OnGetAsync` ottiene un elenco di filmati e li visualizza.</span><span class="sxs-lookup"><span data-stu-id="08893-115">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="08893-116">Quando `OnGet` restituisce `void` o `OnGetAsync` restituisce`Task`, non viene utilizzata alcuna istruzione return.</span><span class="sxs-lookup"><span data-stu-id="08893-116">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return statement is used.</span></span> <span data-ttu-id="08893-117">Quando il tipo restituito è `IActionResult` o `Task<IActionResult>`, è necessario specificare un'istruzione return.</span><span class="sxs-lookup"><span data-stu-id="08893-117">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="08893-118">Ad esempio, il metodo *Pages/Movies/Create.cshtml.cs* `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="08893-118">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="08893-119">Esaminare la pagina Razor *pages/movies/index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="08893-119">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="08893-120">Con Razor è possibile passare da HTML a C# o scegliere un markup specifico per Razor.</span><span class="sxs-lookup"><span data-stu-id="08893-120">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="08893-121">Quando il simbolo `@` è seguito da una [parola chiave riservata Razor ](xref:mvc/views/razor#razor-reserved-keywords), la transizione avviene in un markup specifico per Razor, altrimenti in C#.</span><span class="sxs-lookup"><span data-stu-id="08893-121">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="08893-122">La direttiva @page</span><span class="sxs-lookup"><span data-stu-id="08893-122">The @page directive</span></span>

<span data-ttu-id="08893-123">La `@page` direttiva Razor rende il file un'azione MVC, il che significa che può gestire le richieste.</span><span class="sxs-lookup"><span data-stu-id="08893-123">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="08893-124">`@page` deve essere la prima direttiva Razor in una pagina.</span><span class="sxs-lookup"><span data-stu-id="08893-124">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="08893-125">`@page` è un esempio di transizione nel markup specifico per Razor.</span><span class="sxs-lookup"><span data-stu-id="08893-125">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="08893-126">Vedere [Razor syntax](xref:mvc/views/razor#razor-syntax) (Sintassi Razor) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="08893-126">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="08893-127">Esaminare l'espressione lambda usata nell'helper HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="08893-127">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="08893-128">L'helper HTML `DisplayNameFor` controlla la proprietà `Title` a cui fa riferimento nell'espressione lambda per determinare il nome visualizzato.</span><span class="sxs-lookup"><span data-stu-id="08893-128">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="08893-129">L'espressione lambda viene controllata anziché valutata.</span><span class="sxs-lookup"><span data-stu-id="08893-129">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="08893-130">Ciò significa che non esiste alcuna violazione di `model`accesso `model.Movie`quando, `model.Movie[0]` o `null` è o vuoto.</span><span class="sxs-lookup"><span data-stu-id="08893-130">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="08893-131">Quando invece l'espressione lambda viene valutata (ad esempio con `@Html.DisplayFor(modelItem => item.Title)`), vengono valutati i valori proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="08893-131">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="08893-132">La direttiva @model</span><span class="sxs-lookup"><span data-stu-id="08893-132">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="08893-133">La direttiva `@model` specifica il tipo di modello passato alla pagina Razor.</span><span class="sxs-lookup"><span data-stu-id="08893-133">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="08893-134">Nell'esempio precedente la riga `@model` rende disponibile la classe derivata `PageModel` alla pagina Razor.</span><span class="sxs-lookup"><span data-stu-id="08893-134">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="08893-135">Il modello viene usato negli  [helper HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)`@Html.DisplayNameFor` e `@Html.DisplayFor` nella pagina.</span><span class="sxs-lookup"><span data-stu-id="08893-135">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="08893-136">Pagina di layout</span><span class="sxs-lookup"><span data-stu-id="08893-136">The layout page</span></span>

<span data-ttu-id="08893-137">Selezionare i collegamenti di menu: **RazorPagesMovie**, **Home** e **Privacy**.</span><span class="sxs-lookup"><span data-stu-id="08893-137">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="08893-138">Ogni pagina mostra lo stesso layout di menu.</span><span class="sxs-lookup"><span data-stu-id="08893-138">Each page shows the same menu layout.</span></span> <span data-ttu-id="08893-139">Il layout di menu viene implementato nel file *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="08893-139">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="08893-140">Aprire il file *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="08893-140">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="08893-141">I modelli [Layout](xref:mvc/views/layout) consentono di:</span><span class="sxs-lookup"><span data-stu-id="08893-141">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="08893-142">Specificare il layout del contenitore HTML in un'unica posizione.</span><span class="sxs-lookup"><span data-stu-id="08893-142">Specified in one place.</span></span>
* <span data-ttu-id="08893-143">Applicare il layout in più pagine del sito.</span><span class="sxs-lookup"><span data-stu-id="08893-143">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="08893-144">Trovare la riga `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="08893-144">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="08893-145">`RenderBody` è un segnaposto dove tutte le visualizzazioni specifiche della pagina vengono presentate, *incapsulate* nella pagina di layout.</span><span class="sxs-lookup"><span data-stu-id="08893-145">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="08893-146">Ad esempio, selezionare il collegamento **Privacy** per eseguire il rendering della visualizzazione *Pages/Privacy.cshtml* all'interno del metodo `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="08893-146">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="08893-147">ViewData e layout</span><span class="sxs-lookup"><span data-stu-id="08893-147">ViewData and layout</span></span>

<span data-ttu-id="08893-148">Si consideri il markup seguente dal file *Pages/Movies/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="08893-148">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="08893-149">Il markup evidenziato sopra è un esempio di transizione Razor in C#.</span><span class="sxs-lookup"><span data-stu-id="08893-149">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="08893-150">Tra i caratteri `{` e `}` è racchiuso un blocco di codice C#.</span><span class="sxs-lookup"><span data-stu-id="08893-150">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="08893-151">La `PageModel` classe base contiene una `ViewData` proprietà Dictionary che può essere utilizzata per passare dati a una visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="08893-151">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="08893-152">Gli oggetti vengono aggiunti al dizionario `ViewData` usando uno schema chiave/valore.</span><span class="sxs-lookup"><span data-stu-id="08893-152">Objects are added to the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="08893-153">Nell'esempio precedente la proprietà `"Title"` viene aggiunta al dizionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="08893-153">In the preceding sample, the `"Title"` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="08893-154">La proprietà `"Title"` viene usata nel file *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="08893-154">The `"Title"` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="08893-155">Il markup seguente illustra le prime righe del file *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="08893-155">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="08893-156">La riga `@*Markup removed for brevity.*@` è un commento Razor.</span><span class="sxs-lookup"><span data-stu-id="08893-156">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="08893-157">A differenza dei commenti HTML (`<!-- -->`), i commenti Razor non vengono inviati al client.</span><span class="sxs-lookup"><span data-stu-id="08893-157">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="08893-158">Aggiornare il layout</span><span class="sxs-lookup"><span data-stu-id="08893-158">Update the layout</span></span>

<span data-ttu-id="08893-159">Modificare l'elemento `<title>` nel file *Pages/Shared/_Layout.cshtml* per visualizzare **Movie** anziché **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="08893-159">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="08893-160">Trovare l'elemento di ancoraggio seguente nel file *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="08893-160">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="08893-161">Sostituire l'elemento precedente con il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="08893-161">Replace the preceding element with the following markup:</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="08893-162">L'elemento di ancoraggio precedente è un [helper tag](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="08893-162">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="08893-163">In questo caso, si tratta dell'[helper tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="08893-163">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="08893-164">L'attributo e il valore dell'helper tag `asp-page="/Movies/Index"` creano un collegamento alla pagina Razor `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="08893-164">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="08893-165">Poiché il valore dell'attributo `asp-area` è vuoto, l'area non viene usata nel collegamento.</span><span class="sxs-lookup"><span data-stu-id="08893-165">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="08893-166">Per altre informazioni, vedere [Aree](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="08893-166">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="08893-167">Salvare le modifiche e testare l'app selezionando il collegamento **RpMovie**.</span><span class="sxs-lookup"><span data-stu-id="08893-167">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="08893-168">In caso di problemi, vedere il file [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) in GitHub.</span><span class="sxs-lookup"><span data-stu-id="08893-168">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="08893-169">Testare gli altri collegamenti (**Home**, **RpMovie**, **Create**, **Edit** e **Delete**).</span><span class="sxs-lookup"><span data-stu-id="08893-169">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="08893-170">Ogni pagina imposta il titolo, che è possibile visualizzare nella scheda del browser. Quando si aggiunge un segnalibro a una pagina, il titolo viene usato per il segnalibro.</span><span class="sxs-lookup"><span data-stu-id="08893-170">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="08893-171">Potrebbe non essere possibile immettere virgole decimali nel campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="08893-171">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="08893-172">Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese degli Stati Uniti, è necessario eseguire alcuni passaggi per globalizzare l'app.</span><span class="sxs-lookup"><span data-stu-id="08893-172">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="08893-173">Vedere questo [problema 4076 su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) per istruzioni sull'aggiunta della virgola decimale.</span><span class="sxs-lookup"><span data-stu-id="08893-173">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="08893-174">La proprietà `Layout` viene impostata nel file *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="08893-174">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="08893-175">Il markup precedente imposta il file di layout su *Pages/Shared/_Layout.cshtml* per tutti i file Razor contenuti nella cartella *Pages*.</span><span class="sxs-lookup"><span data-stu-id="08893-175">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="08893-176">Vedere [Layout](xref:razor-pages/index#layout) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="08893-176">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="08893-177">Modello di pagina di creazione</span><span class="sxs-lookup"><span data-stu-id="08893-177">The Create page model</span></span>

<span data-ttu-id="08893-178">Esaminare il modello di pagina *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="08893-178">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="08893-179">Il metodo `OnGet` inizializza lo stato necessario per la pagina.</span><span class="sxs-lookup"><span data-stu-id="08893-179">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="08893-180">La pagina di creazione non possiede uno stato da inizializzare. Viene restituito `Page`.</span><span class="sxs-lookup"><span data-stu-id="08893-180">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="08893-181">Più avanti nell'esercitazione viene visualizzato un esempio di inizializzazione dello stato con `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="08893-181">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="08893-182">Il metodo `Page` crea un oggetto `PageResult` che esegue il rendering della pagina *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="08893-182">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="08893-183">La proprietà `Movie` usa l'attributo `[BindProperty]` per consentire l'[associazione di modelli](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="08893-183">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="08893-184">Dopo che il modulo di creazione registra i valori, il runtime di ASP.NET Core associa i valori registrati al modello `Movie`.</span><span class="sxs-lookup"><span data-stu-id="08893-184">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="08893-185">Il metodo `OnPostAsync` viene eseguito quando la pagina inserisce i dati del modulo:</span><span class="sxs-lookup"><span data-stu-id="08893-185">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="08893-186">Se il modello contiene errori, il modulo viene nuovamente visualizzato insieme ai dati del modulo inviati.</span><span class="sxs-lookup"><span data-stu-id="08893-186">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="08893-187">La maggior parte degli errori del modello sono riscontrabili sul lato client prima che il modulo sia inviato.</span><span class="sxs-lookup"><span data-stu-id="08893-187">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="08893-188">La registrazione di un valore per il campo data non convertibile in una data è un esempio di errore del modello.</span><span class="sxs-lookup"><span data-stu-id="08893-188">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="08893-189">Durante l'esercitazione saranno poi trattate nel dettaglio la convalida lato client e la convalida del modello.</span><span class="sxs-lookup"><span data-stu-id="08893-189">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="08893-190">Se non sono presenti errori nel modello, i dati vengono salvati e il browser viene reindirizzato alla pagina di indice.</span><span class="sxs-lookup"><span data-stu-id="08893-190">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="08893-191">Pagina di creazione di Razor</span><span class="sxs-lookup"><span data-stu-id="08893-191">The Create Razor Page</span></span>

<span data-ttu-id="08893-192">Esaminare il file della pagina Razor *Pages/Movies/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="08893-192">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="08893-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="08893-193">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="08893-194">Visual Studio visualizza i tag seguenti con un tipo di carattere in grassetto distintivo specifico per gli helper tag:</span><span class="sxs-lookup"><span data-stu-id="08893-194">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Visualizzazione di VS17 della pagina Create.cshtml](page/_static/th3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="08893-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="08893-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="08893-197">Nel markup precedente sono visualizzati gli helper tag seguenti:</span><span class="sxs-lookup"><span data-stu-id="08893-197">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="08893-198">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="08893-198">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="08893-199">Visual Studio visualizza i tag seguenti con un tipo di carattere in grassetto distintivo specifico per gli helper tag:</span><span class="sxs-lookup"><span data-stu-id="08893-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="08893-200">L'elemento `<form method="post">` è un [helper tag del modulo](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="08893-200">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="08893-201">L'helper tag del modulo include automaticamente un [token antifalsificazione](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="08893-201">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="08893-202">Il motore di scaffolding crea un markup Razor per ogni campo nel modello (tranne l'ID) simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="08893-202">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="08893-203">Gli [Helper tag di convalida](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` e `<span asp-validation-for`) visualizzano gli errori di convalida.</span><span class="sxs-lookup"><span data-stu-id="08893-203">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="08893-204">Il tema della convalida viene trattato in modo più dettagliato successivamente in questa serie.</span><span class="sxs-lookup"><span data-stu-id="08893-204">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="08893-205">L' [Helper tag di etichetta](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) genera la didascalia dell' `for` etichetta e l' `Title` attributo per la proprietà.</span><span class="sxs-lookup"><span data-stu-id="08893-205">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="08893-206">L' [Helper tag di input](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) USA gli attributi [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produce gli attributi HTML necessari per la convalida jQuery sul lato client.</span><span class="sxs-lookup"><span data-stu-id="08893-206">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="08893-207">Per altre informazioni sugli helper tag, ad esempio `<form method="post">`, vedere [Helper tag in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="08893-207">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="08893-208">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="08893-208">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="08893-209">[Precedente: aggiunta di un modello](xref:tutorials/razor-pages/model)
> [successivo: database](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="08893-209">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="08893-210">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="08893-210">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="08893-211">Questa esercitazione esamina i Razor Pages creati dall'impalcatura nell' [esercitazione precedente](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="08893-211">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="08893-212">[Visualizzare o scaricare il campione ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22).</span><span class="sxs-lookup"><span data-stu-id="08893-212">[View or download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="08893-213">Pagine di creazione, eliminazione, dettagli e modifica</span><span class="sxs-lookup"><span data-stu-id="08893-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="08893-214">Esaminare il modello di pagina *Pages/Movies/Index.cshtml.cs*: </span><span class="sxs-lookup"><span data-stu-id="08893-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="08893-215">Le pagine Razor vengono derivate da `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="08893-215">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="08893-216">Per convenzione, la classe derivata `PageModel` viene denominata `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="08893-216">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="08893-217">Il costruttore usa l'[inserimento delle dipendenze](xref:fundamentals/dependency-injection) per aggiungere `RazorPagesMovieContext` alla pagina.</span><span class="sxs-lookup"><span data-stu-id="08893-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="08893-218">Tutte le pagine create tramite scaffolding seguono questo schema.</span><span class="sxs-lookup"><span data-stu-id="08893-218">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="08893-219">Vedere [Codice asincrono](xref:data/ef-rp/intro#asynchronous-code) per altre informazioni sulla programmazione asincrona con Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="08893-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="08893-220">Quando per la pagina viene eseguita una richiesta, il metodo `OnGetAsync` restituisce un elenco di filmati alla pagina Razor.</span><span class="sxs-lookup"><span data-stu-id="08893-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="08893-221">Nella pagina Razor viene chiamato `OnGetAsync`o `OnGet` per inizializzare lo stato della pagina.</span><span class="sxs-lookup"><span data-stu-id="08893-221">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="08893-222">In questo caso, `OnGetAsync` ottiene un elenco di filmati e li visualizza.</span><span class="sxs-lookup"><span data-stu-id="08893-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="08893-223">Quando `OnGet` restituisce `void` o `OnGetAsync` restituisce`Task`, non viene usato alcun metodo restituito.</span><span class="sxs-lookup"><span data-stu-id="08893-223">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="08893-224">Quando il tipo restituito è `IActionResult` o `Task<IActionResult>`, è necessario specificare un'istruzione return.</span><span class="sxs-lookup"><span data-stu-id="08893-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="08893-225">Ad esempio, il metodo *Pages/Movies/Create.cshtml.cs* `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="08893-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="08893-226">Esaminare la pagina Razor *pages/movies/index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="08893-226">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="08893-227">Con Razor è possibile passare da HTML a C# o scegliere un markup specifico per Razor.</span><span class="sxs-lookup"><span data-stu-id="08893-227">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="08893-228">Quando il simbolo `@` è seguito da una [parola chiave riservata Razor ](xref:mvc/views/razor#razor-reserved-keywords), la transizione avviene in un markup specifico per Razor, altrimenti in C#.</span><span class="sxs-lookup"><span data-stu-id="08893-228">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="08893-229">La direttiva Razor `@page` trasforma il file in un'azione MVC in modo che possa gestire le richieste.</span><span class="sxs-lookup"><span data-stu-id="08893-229">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="08893-230">`@page` deve essere la prima direttiva Razor in una pagina.</span><span class="sxs-lookup"><span data-stu-id="08893-230">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="08893-231">`@page` è un esempio di transizione nel markup specifico per Razor.</span><span class="sxs-lookup"><span data-stu-id="08893-231">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="08893-232">Vedere [Razor syntax](xref:mvc/views/razor#razor-syntax) (Sintassi Razor) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="08893-232">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="08893-233">Esaminare l'espressione lambda usata nell'helper HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="08893-233">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="08893-234">L'helper HTML `DisplayNameFor` controlla la proprietà `Title` a cui fa riferimento nell'espressione lambda per determinare il nome visualizzato.</span><span class="sxs-lookup"><span data-stu-id="08893-234">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="08893-235">L'espressione lambda viene controllata anziché valutata.</span><span class="sxs-lookup"><span data-stu-id="08893-235">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="08893-236">Non sussiste pertanto violazione di accesso quando `model`, `model.Movie`, o `model.Movie[0]` sono `null` o vuoti.</span><span class="sxs-lookup"><span data-stu-id="08893-236">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="08893-237">Quando invece l'espressione lambda viene valutata (ad esempio con `@Html.DisplayFor(modelItem => item.Title)`), vengono valutati i valori proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="08893-237">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="08893-238">La direttiva @model</span><span class="sxs-lookup"><span data-stu-id="08893-238">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="08893-239">La direttiva `@model` specifica il tipo di modello passato alla pagina Razor.</span><span class="sxs-lookup"><span data-stu-id="08893-239">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="08893-240">Nell'esempio precedente la riga `@model` rende disponibile la classe derivata `PageModel` alla pagina Razor.</span><span class="sxs-lookup"><span data-stu-id="08893-240">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="08893-241">Il modello viene usato negli  [helper HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)`@Html.DisplayNameFor` e `@Html.DisplayFor` nella pagina.</span><span class="sxs-lookup"><span data-stu-id="08893-241">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="08893-242">Pagina di layout</span><span class="sxs-lookup"><span data-stu-id="08893-242">The layout page</span></span>

<span data-ttu-id="08893-243">Selezionare i collegamenti di menu: **RazorPagesMovie**, **Home** e **Privacy**.</span><span class="sxs-lookup"><span data-stu-id="08893-243">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="08893-244">Ogni pagina mostra lo stesso layout di menu.</span><span class="sxs-lookup"><span data-stu-id="08893-244">Each page shows the same menu layout.</span></span> <span data-ttu-id="08893-245">Il layout di menu viene implementato nel file *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="08893-245">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="08893-246">Aprire il file *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="08893-246">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="08893-247">I modelli di [layout](xref:mvc/views/layout) consentono di specificare il layout del contenitore HTML del sito in un'unica posizione e quindi di applicarlo in più pagine del sito.</span><span class="sxs-lookup"><span data-stu-id="08893-247">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="08893-248">Trovare la riga `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="08893-248">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="08893-249">`RenderBody` è un segnaposto dove tutte le visualizzazioni specifiche della pagina vengono presentate, *incapsulate* nella pagina di layout.</span><span class="sxs-lookup"><span data-stu-id="08893-249">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="08893-250">Ad esempio, se si seleziona il collegamento **Privacy**, il rendering della visualizzazione **Pages/Privacy.cshtml** viene eseguito all'interno del metodo `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="08893-250">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="08893-251">ViewData e layout</span><span class="sxs-lookup"><span data-stu-id="08893-251">ViewData and layout</span></span>

<span data-ttu-id="08893-252">Si consideri il seguente codice del file *Pages/Movies/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="08893-252">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="08893-253">Il codice evidenziato sopra è un esempio di transizione Razor in C#.</span><span class="sxs-lookup"><span data-stu-id="08893-253">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="08893-254">Tra i caratteri `{` e `}` è racchiuso un blocco di codice C#.</span><span class="sxs-lookup"><span data-stu-id="08893-254">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="08893-255">La classe di base `PageModel` ha una proprietà del dizionario `ViewData` che può essere usata per aggiungere i dati da passare a una visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="08893-255">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="08893-256">Gli oggetti vengono aggiunti al dizionario `ViewData` usando uno schema chiave/valore.</span><span class="sxs-lookup"><span data-stu-id="08893-256">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="08893-257">Nell'esempio precedente la proprietà "Title" viene aggiunta al dizionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="08893-257">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="08893-258">La proprietà "Title" viene usata nel file *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="08893-258">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="08893-259">Il markup seguente illustra le prime righe del file *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="08893-259">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="08893-260">La riga `@*Markup removed for brevity.*@` è un commento Razor che non viene visualizzato nel file di layout.</span><span class="sxs-lookup"><span data-stu-id="08893-260">The line `@*Markup removed for brevity.*@` is a Razor comment which doesn't appear in your layout file.</span></span> <span data-ttu-id="08893-261">A differenza dei commenti HTML (`<!-- -->`), i commenti Razor non vengono inviati al client.</span><span class="sxs-lookup"><span data-stu-id="08893-261">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="08893-262">Aggiornare il layout</span><span class="sxs-lookup"><span data-stu-id="08893-262">Update the layout</span></span>

<span data-ttu-id="08893-263">Modificare l'elemento `<title>` nel file *Pages/Shared/_Layout.cshtml* per visualizzare **Movie** anziché **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="08893-263">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="08893-264">Trovare l'elemento di ancoraggio seguente nel file *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="08893-264">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="08893-265">Sostituire l'elemento precedente con il markup seguente.</span><span class="sxs-lookup"><span data-stu-id="08893-265">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="08893-266">L'elemento di ancoraggio precedente è un [helper tag](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="08893-266">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="08893-267">In questo caso, si tratta dell'[helper tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="08893-267">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="08893-268">L'attributo e il valore dell'helper tag `asp-page="/Movies/Index"` creano un collegamento alla pagina Razor `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="08893-268">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="08893-269">Poiché il valore dell'attributo `asp-area` è vuoto, l'area non viene usata nel collegamento.</span><span class="sxs-lookup"><span data-stu-id="08893-269">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="08893-270">Per altre informazioni, vedere [Aree](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="08893-270">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="08893-271">Salvare le modifiche e testare l'app selezionando il collegamento **RpMovie**.</span><span class="sxs-lookup"><span data-stu-id="08893-271">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="08893-272">In caso di problemi, vedere il file [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) in GitHub.</span><span class="sxs-lookup"><span data-stu-id="08893-272">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="08893-273">Testare gli altri collegamenti (**Home**, **RpMovie**, **Create**, **Edit** e **Delete**).</span><span class="sxs-lookup"><span data-stu-id="08893-273">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="08893-274">Ogni pagina imposta il titolo, che è possibile visualizzare nella scheda del browser. Quando si aggiunge un segnalibro a una pagina, il titolo viene usato per il segnalibro.</span><span class="sxs-lookup"><span data-stu-id="08893-274">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="08893-275">Potrebbe non essere possibile immettere virgole decimali nel campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="08893-275">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="08893-276">Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese degli Stati Uniti, è necessario eseguire alcuni passaggi per globalizzare l'app.</span><span class="sxs-lookup"><span data-stu-id="08893-276">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="08893-277">[Problema 4076 su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) per istruzioni sull'aggiunta della virgola decimale.</span><span class="sxs-lookup"><span data-stu-id="08893-277">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="08893-278">La proprietà `Layout` viene impostata nel file *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="08893-278">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="08893-279">Il markup precedente imposta il file di layout su *Pages/Shared/_Layout.cshtml* per tutti i file Razor contenuti nella cartella *Pages*.</span><span class="sxs-lookup"><span data-stu-id="08893-279">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="08893-280">Vedere [Layout](xref:razor-pages/index#layout) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="08893-280">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="08893-281">Modello di pagina di creazione</span><span class="sxs-lookup"><span data-stu-id="08893-281">The Create page model</span></span>

<span data-ttu-id="08893-282">Esaminare il modello di pagina *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="08893-282">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="08893-283">Il metodo `OnGet` inizializza lo stato necessario per la pagina.</span><span class="sxs-lookup"><span data-stu-id="08893-283">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="08893-284">La pagina di creazione non possiede uno stato da inizializzare. Viene restituito `Page`.</span><span class="sxs-lookup"><span data-stu-id="08893-284">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="08893-285">Più avanti nell'esercitazione viene illustrato lo stato di inizializzazione del metodo `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="08893-285">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="08893-286">Il metodo `Page` crea un oggetto `PageResult` che esegue il rendering della pagina *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="08893-286">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="08893-287">La proprietà `Movie` usa l'attributo `[BindProperty]` per consentire l'[associazione di modelli](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="08893-287">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="08893-288">Dopo che il modulo di creazione registra i valori, il runtime di ASP.NET Core associa i valori registrati al modello `Movie`.</span><span class="sxs-lookup"><span data-stu-id="08893-288">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="08893-289">Il metodo `OnPostAsync` viene eseguito quando la pagina inserisce i dati del modulo:</span><span class="sxs-lookup"><span data-stu-id="08893-289">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="08893-290">Se il modello contiene errori, il modulo viene nuovamente visualizzato insieme ai dati del modulo inviati.</span><span class="sxs-lookup"><span data-stu-id="08893-290">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="08893-291">La maggior parte degli errori del modello sono riscontrabili sul lato client prima che il modulo sia inviato.</span><span class="sxs-lookup"><span data-stu-id="08893-291">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="08893-292">La registrazione di un valore per il campo data non convertibile in una data è un esempio di errore del modello.</span><span class="sxs-lookup"><span data-stu-id="08893-292">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="08893-293">Durante l'esercitazione saranno poi trattate nel dettaglio la convalida lato client e la convalida del modello.</span><span class="sxs-lookup"><span data-stu-id="08893-293">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="08893-294">Se non sono presenti errori nel modello, i dati vengono salvati e il browser viene reindirizzato alla pagina di indice.</span><span class="sxs-lookup"><span data-stu-id="08893-294">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="08893-295">Pagina di creazione di Razor</span><span class="sxs-lookup"><span data-stu-id="08893-295">The Create Razor Page</span></span>

<span data-ttu-id="08893-296">Esaminare il file della pagina Razor *Pages/Movies/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="08893-296">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="08893-297">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="08893-297">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="08893-298">Per visualizzare il tag `<form method="post">`, Visual Studio usa un tipo di carattere in grassetto distintivo specifico per gli helper tag:</span><span class="sxs-lookup"><span data-stu-id="08893-298">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Visualizzazione di VS17 della pagina Create.cshtml](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="08893-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="08893-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="08893-301">Per altre informazioni sugli helper tag, ad esempio `<form method="post">`, vedere [Helper tag in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="08893-301">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="08893-302">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="08893-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="08893-303">Per visualizzare il tag `<form method="post">`, Visual Studio per Mac usa un tipo di carattere in grassetto distintivo specifico per gli helper tag.</span><span class="sxs-lookup"><span data-stu-id="08893-303">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="08893-304">L'elemento `<form method="post">` è un [helper tag del modulo](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="08893-304">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="08893-305">L'helper tag del modulo include automaticamente un [token antifalsificazione](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="08893-305">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="08893-306">Il motore di ponteggi crea Razor markup per ogni campo nel modello (ad eccezione dell'ID) simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="08893-306">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="08893-307">Gli [Helper tag di convalida](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` e `<span asp-validation-for`) visualizzano gli errori di convalida.</span><span class="sxs-lookup"><span data-stu-id="08893-307">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="08893-308">Il tema della convalida viene trattato in modo più dettagliato successivamente in questa serie.</span><span class="sxs-lookup"><span data-stu-id="08893-308">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="08893-309">L' [Helper tag di etichetta](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) genera la didascalia dell' `for` etichetta e l' `Title` attributo per la proprietà.</span><span class="sxs-lookup"><span data-stu-id="08893-309">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="08893-310">L' [Helper tag di input](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) USA gli attributi [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produce gli attributi HTML necessari per la convalida jQuery sul lato client.</span><span class="sxs-lookup"><span data-stu-id="08893-310">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="08893-311">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="08893-311">Additional resources</span></span>

* [<span data-ttu-id="08893-312">Versione YouTube dell'esercitazione</span><span class="sxs-lookup"><span data-stu-id="08893-312">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="08893-313">[Precedente: aggiunta di un modello](xref:tutorials/razor-pages/model)
> [successivo: database](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="08893-313">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
