---
title: 'Parte 3, pagine con impalcature Razor'
author: rick-anderson
description: 'Parte 3 della serie di esercitazioni sulle Razor pagine.'
ms.author: riande
ms.date: 09/25/2020
no-loc:
- 'Index'
- 'Create'
- 'Delete'
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
uid: tutorials/razor-pages/page
ms.openlocfilehash: a9494feacbe783b20a9f5eb98ef9e481f2c713fa
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360892"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="0289a-103">Parte 3, Razor pagine con impalcatura in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0289a-103">Part 3, scaffolded Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="0289a-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0289a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0289a-105">Questa esercitazione esamina le Razor pagine create dall'impalcatura nell' [esercitazione precedente](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="0289a-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0289a-106">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="0289a-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="0289a-107">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="0289a-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="the-no-loccreate-no-locdelete-details-and-edit-pages"></a><span data-ttu-id="0289a-108">Le Create Delete pagine,, Details e Edit</span><span class="sxs-lookup"><span data-stu-id="0289a-108">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="0289a-109">Esaminare il modello di pagina *pages/Movies/ Index . cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="0289a-109">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="0289a-110">Razor Le pagine sono derivate da `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="0289a-110">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="0289a-111">Per convenzione, la `PageModel` classe derivata da è denominata `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="0289a-111">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="0289a-112">Il costruttore usa l' [inserimento delle dipendenze](xref:fundamentals/dependency-injection) per aggiungere la `RazorPagesMovieContext` alla pagina:</span><span class="sxs-lookup"><span data-stu-id="0289a-112">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet1&highlight=5)]

<span data-ttu-id="0289a-113">Vedere [Codice asincrono](xref:data/ef-rp/intro#asynchronous-code) per altre informazioni sulla programmazione asincrona con Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="0289a-113">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="0289a-114">Quando viene effettuata una richiesta per la pagina, il `OnGetAsync` metodo restituisce un elenco di filmati alla Razor pagina.</span><span class="sxs-lookup"><span data-stu-id="0289a-114">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="0289a-115">In una Razor pagina `OnGetAsync` o `OnGet` viene chiamato per inizializzare lo stato della pagina.</span><span class="sxs-lookup"><span data-stu-id="0289a-115">On a Razor Page, `OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="0289a-116">In questo caso, `OnGetAsync` ottiene un elenco di filmati e li visualizza.</span><span class="sxs-lookup"><span data-stu-id="0289a-116">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="0289a-117">Quando `OnGet` restituisce `void` o `OnGetAsync` restituisce `Task` , non viene utilizzata alcuna istruzione return.</span><span class="sxs-lookup"><span data-stu-id="0289a-117">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return statement is used.</span></span> <span data-ttu-id="0289a-118">Ad esempio la pagina privacy:</span><span class="sxs-lookup"><span data-stu-id="0289a-118">For example the Privacy Page:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="0289a-119">Quando il tipo restituito è `IActionResult` o `Task<IActionResult>`, è necessario specificare un'istruzione return.</span><span class="sxs-lookup"><span data-stu-id="0289a-119">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="0289a-120">Ad esempio, il metodo *pages/Movies/ Create . cshtml.cs* `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="0289a-120">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="0289a-121">Esaminare la pagina *pages/Movies/ Index . cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="0289a-121">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="0289a-122">Razor consente di eseguire la transizione da HTML a C# o a un Razor markup specifico.</span><span class="sxs-lookup"><span data-stu-id="0289a-122">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="0289a-123">Quando un `@` simbolo è seguito da una [ Razor parola chiave riservata](xref:mvc/views/razor#razor-reserved-keywords), esegue la transizione in un Razor markup specifico, in caso contrario passa a C#.</span><span class="sxs-lookup"><span data-stu-id="0289a-123">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="0289a-124">La direttiva @page</span><span class="sxs-lookup"><span data-stu-id="0289a-124">The @page directive</span></span>

<span data-ttu-id="0289a-125">La `@page` Razor direttiva rende il file un'azione MVC, il che significa che può gestire le richieste.</span><span class="sxs-lookup"><span data-stu-id="0289a-125">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="0289a-126">`@page` deve essere la prima Razor direttiva in una pagina.</span><span class="sxs-lookup"><span data-stu-id="0289a-126">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="0289a-127">`@page` e `@model` sono esempi di transizione al Razor markup specifico.</span><span class="sxs-lookup"><span data-stu-id="0289a-127">`@page` and `@model` are examples of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="0289a-128">Per ulteriori informazioni, vedere la [ Razor sintassi](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="0289a-128">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="0289a-129">La direttiva @model</span><span class="sxs-lookup"><span data-stu-id="0289a-129">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="0289a-130">La `@model` direttiva specifica il tipo del modello passato alla Razor pagina.</span><span class="sxs-lookup"><span data-stu-id="0289a-130">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="0289a-131">Nell'esempio precedente la `@model` riga rende `PageModel` disponibile la classe derivata da per la Razor pagina.</span><span class="sxs-lookup"><span data-stu-id="0289a-131">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="0289a-132">Il modello viene usato negli  [helper HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)`@Html.DisplayNameFor` e `@Html.DisplayFor` nella pagina.</span><span class="sxs-lookup"><span data-stu-id="0289a-132">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>


<span data-ttu-id="0289a-133">Esaminare l'espressione lambda usata nell'helper HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="0289a-133">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="0289a-134">L'helper HTML <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> controlla la proprietà `Title` a cui fa riferimento nell'espressione lambda per determinare il nome visualizzato.</span><span class="sxs-lookup"><span data-stu-id="0289a-134">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="0289a-135">L'espressione lambda viene controllata anziché valutata.</span><span class="sxs-lookup"><span data-stu-id="0289a-135">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="0289a-136">Ciò significa che non esiste alcuna violazione di accesso quando `model` , `model.Movie` o `model.Movie[0]` è `null` o vuoto.</span><span class="sxs-lookup"><span data-stu-id="0289a-136">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="0289a-137">Quando viene valutata l'espressione lambda, ad esempio con `@Html.DisplayFor(modelItem => item.Title)` , vengono valutati i valori delle proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="0289a-137">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="0289a-138">Pagina di layout</span><span class="sxs-lookup"><span data-stu-id="0289a-138">The layout page</span></span>

<span data-ttu-id="0289a-139">Selezionare il menu collegamenti **Razor PagesMovie** , **Home** e **privacy**.</span><span class="sxs-lookup"><span data-stu-id="0289a-139">Select the menu links **RazorPagesMovie** , **Home** , and **Privacy**.</span></span> <span data-ttu-id="0289a-140">Ogni pagina mostra lo stesso layout di menu.</span><span class="sxs-lookup"><span data-stu-id="0289a-140">Each page shows the same menu layout.</span></span> <span data-ttu-id="0289a-141">Il layout di menu viene implementato nel file *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0289a-141">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="0289a-142">Aprire ed esaminare il file *pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="0289a-142">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="0289a-143">I modelli [Layout](xref:mvc/views/layout) consentono di:</span><span class="sxs-lookup"><span data-stu-id="0289a-143">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="0289a-144">Specificare il layout del contenitore HTML in un'unica posizione.</span><span class="sxs-lookup"><span data-stu-id="0289a-144">Specified in one place.</span></span>
* <span data-ttu-id="0289a-145">Applicare il layout in più pagine del sito.</span><span class="sxs-lookup"><span data-stu-id="0289a-145">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="0289a-146">Trovare la riga `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="0289a-146">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="0289a-147">`RenderBody` è un segnaposto dove tutte le visualizzazioni specifiche della pagina vengono presentate, *incapsulate* nella pagina di layout.</span><span class="sxs-lookup"><span data-stu-id="0289a-147">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="0289a-148">Ad esempio, selezionare il collegamento **Privacy** per eseguire il rendering della visualizzazione *Pages/Privacy.cshtml* all'interno del metodo `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="0289a-148">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="0289a-149">ViewData e layout</span><span class="sxs-lookup"><span data-stu-id="0289a-149">ViewData and layout</span></span>

<span data-ttu-id="0289a-150">Si consideri il markup seguente dal file *pages/Movies/ Index . cshtml* :</span><span class="sxs-lookup"><span data-stu-id="0289a-150">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="0289a-151">Il markup evidenziato sopra è un esempio di Razor transizione in C#.</span><span class="sxs-lookup"><span data-stu-id="0289a-151">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="0289a-152">Tra i caratteri `{` e `}` è racchiuso un blocco di codice C#.</span><span class="sxs-lookup"><span data-stu-id="0289a-152">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="0289a-153">La `PageModel` classe base contiene una `ViewData` Proprietà Dictionary che può essere utilizzata per passare dati a una visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="0289a-153">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="0289a-154">Gli oggetti vengono aggiunti al `ViewData` dizionario usando un criterio \* **valore chiave** _.</span><span class="sxs-lookup"><span data-stu-id="0289a-154">Objects are added to the `ViewData` dictionary using a \* **key value** _ pattern.</span></span> <span data-ttu-id="0289a-155">Nell'esempio precedente la proprietà `Title` viene aggiunta al dizionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="0289a-155">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="0289a-156">La `Title` proprietà viene usata nel file _Pages/shared/_Layout. cshtml \*.</span><span class="sxs-lookup"><span data-stu-id="0289a-156">The `Title` property is used in the _Pages/Shared/_Layout.cshtml\* file.</span></span> <span data-ttu-id="0289a-157">Il markup seguente illustra le prime righe del file *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0289a-157">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="0289a-158">La riga `@*Markup removed for brevity.*@` è un Razor commento.</span><span class="sxs-lookup"><span data-stu-id="0289a-158">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="0289a-159">Diversamente dai commenti HTML `<!-- -->` , Razor i commenti non vengono inviati al client.</span><span class="sxs-lookup"><span data-stu-id="0289a-159">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="0289a-160">Per ulteriori informazioni, vedere la pagina relativa all'introduzione [al codice HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) .</span><span class="sxs-lookup"><span data-stu-id="0289a-160">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="0289a-161">Aggiornare il layout</span><span class="sxs-lookup"><span data-stu-id="0289a-161">Update the layout</span></span>

1. <span data-ttu-id="0289a-162">Modificare l' `<title>` elemento nel file *pages/Shared/_Layout. cshtml* per visualizzare il **film** anziché **Razor PagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="0289a-162">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

1. <span data-ttu-id="0289a-163">Trovare l'elemento di ancoraggio seguente nel file *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0289a-163">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

   ```cshtml
   <a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
   ```

1. <span data-ttu-id="0289a-164">Sostituire l'elemento precedente con il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="0289a-164">Replace the preceding element with the following markup:</span></span>

   ```cshtml
   <a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
   ```

   <span data-ttu-id="0289a-165">L'elemento di ancoraggio precedente è un [helper tag](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="0289a-165">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="0289a-166">In questo caso, si tratta dell'[helper tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="0289a-166">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="0289a-167">L' `asp-page="/Movies/Index"` attributo e il valore dell'helper Tag creano un collegamento alla `/Movies/Index` Razor pagina.</span><span class="sxs-lookup"><span data-stu-id="0289a-167">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="0289a-168">Poiché il valore dell'attributo `asp-area` è vuoto, l'area non viene usata nel collegamento.</span><span class="sxs-lookup"><span data-stu-id="0289a-168">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="0289a-169">Per altre informazioni, vedere [Aree](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="0289a-169">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

1. <span data-ttu-id="0289a-170">Salvare le modifiche ed eseguire il test dell'app selezionando il collegamento **RpMovie** .</span><span class="sxs-lookup"><span data-stu-id="0289a-170">Save the changes and test the app by selecting the **RpMovie** link.</span></span> <span data-ttu-id="0289a-171">In caso di problemi, vedere il file [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) in GitHub.</span><span class="sxs-lookup"><span data-stu-id="0289a-171">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

1. <span data-ttu-id="0289a-172">Testare i collegamenti **Home** , **RpMovie** , **Create** , **Edit** e **Delete** .</span><span class="sxs-lookup"><span data-stu-id="0289a-172">Test the **Home** , **RpMovie** , **Create** , **Edit** , and **Delete** links.</span></span> <span data-ttu-id="0289a-173">Ogni pagina imposta il titolo, che è possibile visualizzare nella scheda del browser. Quando si aggiunge un segnalibro a una pagina, il titolo viene usato per il segnalibro.</span><span class="sxs-lookup"><span data-stu-id="0289a-173">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="0289a-174">Potrebbe non essere possibile immettere virgole decimali nel campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="0289a-174">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="0289a-175">Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per le impostazioni locali diverse dall'inglese che usano una virgola (",") per un separatore decimale e formati non US-English data, è necessario eseguire le operazioni per globalizzare l'app.</span><span class="sxs-lookup"><span data-stu-id="0289a-175">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="0289a-176">Vedere questo [problema 4076 su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) per istruzioni sull'aggiunta della virgola decimale.</span><span class="sxs-lookup"><span data-stu-id="0289a-176">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="0289a-177">La proprietà `Layout` viene impostata nel file *Pages/_ViewStart.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="0289a-177">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="0289a-178">Il markup precedente imposta il file di layout su *pages/Shared/_Layout. cshtml* per tutti Razor i file nella cartella *pages* .</span><span class="sxs-lookup"><span data-stu-id="0289a-178">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="0289a-179">Vedere [Layout](xref:razor-pages/index#layout) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="0289a-179">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-no-loccreate-page-model"></a><span data-ttu-id="0289a-180">CreateModello di pagina</span><span class="sxs-lookup"><span data-stu-id="0289a-180">The Create page model</span></span>

<span data-ttu-id="0289a-181">Esaminare il modello di pagina *pages/Movies/ Create . cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="0289a-181">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="0289a-182">Il metodo `OnGet` inizializza lo stato necessario per la pagina.</span><span class="sxs-lookup"><span data-stu-id="0289a-182">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="0289a-183">La Create pagina non dispone di alcuno stato da inizializzare, pertanto `Page` viene restituito.</span><span class="sxs-lookup"><span data-stu-id="0289a-183">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="0289a-184">Più avanti nell'esercitazione viene visualizzato un esempio di inizializzazione dello stato con `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="0289a-184">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="0289a-185">Il `Page` metodo crea un `PageResult` oggetto che esegue il rendering della pagina *Create . cshtml* .</span><span class="sxs-lookup"><span data-stu-id="0289a-185">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="0289a-186">La `Movie` Proprietà usa l'attributo [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) per acconsentire esplicitamente all' [associazione di modelli](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="0289a-186">The `Movie` property uses the [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="0289a-187">Quando il Create form inserisce i valori del form, il runtime di ASP.NET Core associa i valori inviati al `Movie` modello.</span><span class="sxs-lookup"><span data-stu-id="0289a-187">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="0289a-188">Il metodo `OnPostAsync` viene eseguito quando la pagina inserisce i dati del modulo:</span><span class="sxs-lookup"><span data-stu-id="0289a-188">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="0289a-189">Se il modello contiene errori, il modulo viene nuovamente visualizzato insieme ai dati del modulo inviati.</span><span class="sxs-lookup"><span data-stu-id="0289a-189">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="0289a-190">La maggior parte degli errori del modello sono riscontrabili sul lato client prima che il modulo sia inviato.</span><span class="sxs-lookup"><span data-stu-id="0289a-190">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="0289a-191">La registrazione di un valore per il campo data non convertibile in una data è un esempio di errore del modello.</span><span class="sxs-lookup"><span data-stu-id="0289a-191">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="0289a-192">Durante l'esercitazione saranno poi trattate nel dettaglio la convalida lato client e la convalida del modello.</span><span class="sxs-lookup"><span data-stu-id="0289a-192">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="0289a-193">Se non sono presenti errori del modello:</span><span class="sxs-lookup"><span data-stu-id="0289a-193">If there are no model errors:</span></span>

* <span data-ttu-id="0289a-194">I dati vengono salvati.</span><span class="sxs-lookup"><span data-stu-id="0289a-194">The data is saved.</span></span>
* <span data-ttu-id="0289a-195">Il browser viene reindirizzato alla Index pagina.</span><span class="sxs-lookup"><span data-stu-id="0289a-195">The browser is redirected to the Index page.</span></span>

### <a name="the-no-loccreate-no-locrazor-page"></a><span data-ttu-id="0289a-196">Create Razor Pagina</span><span class="sxs-lookup"><span data-stu-id="0289a-196">The Create Razor Page</span></span>

<span data-ttu-id="0289a-197">Esaminare il file di paging *pages/Movies/ Create . cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="0289a-197">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="0289a-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0289a-198">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0289a-199">Visual Studio visualizza i tag seguenti con un tipo di carattere in grassetto distintivo specifico per gli helper tag:</span><span class="sxs-lookup"><span data-stu-id="0289a-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Visualizzazione VS17 della pagina::: NO-LOC (Create):::. cshtml](page/_static/th3.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0289a-201">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="0289a-201">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="0289a-202">Nel markup precedente sono visualizzati gli helper tag seguenti:</span><span class="sxs-lookup"><span data-stu-id="0289a-202">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="0289a-203">L'elemento `<form method="post">` è un [helper tag del modulo](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="0289a-203">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="0289a-204">L'helper tag del modulo include automaticamente un [token antifalsificazione](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="0289a-204">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="0289a-205">Il motore di ponteggi crea Razor markup per ogni campo nel modello, ad eccezione dell'ID, simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="0289a-205">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="0289a-206">Gli [Helper tag di convalida](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` e `<span asp-validation-for` ) visualizzano gli errori di convalida.</span><span class="sxs-lookup"><span data-stu-id="0289a-206">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="0289a-207">Il tema della convalida viene trattato in modo più dettagliato successivamente in questa serie.</span><span class="sxs-lookup"><span data-stu-id="0289a-207">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="0289a-208">L' [Helper tag di etichetta](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) genera la didascalia dell'etichetta e l' `[for]` attributo per la `Title` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="0289a-208">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="0289a-209">L' [Helper tag di input](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) USA gli attributi [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produce gli attributi HTML necessari per la convalida jQuery sul lato client.</span><span class="sxs-lookup"><span data-stu-id="0289a-209">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="0289a-210">Per altre informazioni sugli helper tag, ad esempio `<form method="post">`, vedere [Helper tag in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="0289a-210">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0289a-211">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="0289a-211">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="0289a-212">[Precedente: aggiungere un modello](xref:tutorials/razor-pages/model) 
>  Passaggio [successivo: utilizzo di un database](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="0289a-212">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0289a-213">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0289a-213">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0289a-214">Questa esercitazione esamina le Razor pagine create dall'impalcatura nell' [esercitazione precedente](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="0289a-214">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="0289a-215">[Visualizzare o scaricare il campione ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22).</span><span class="sxs-lookup"><span data-stu-id="0289a-215">[View or download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) sample.</span></span>

## <a name="the-no-loccreate-no-locdelete-details-and-edit-pages"></a><span data-ttu-id="0289a-216">Le Create Delete pagine,, Details e Edit</span><span class="sxs-lookup"><span data-stu-id="0289a-216">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="0289a-217">Esaminare il modello di pagina *pages/Movies/ Index . cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="0289a-217">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="0289a-218">Razor Le pagine sono derivate da `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="0289a-218">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="0289a-219">Per convenzione, la `PageModel` classe derivata da è denominata `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="0289a-219">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="0289a-220">Il costruttore usa l'[inserimento delle dipendenze](xref:fundamentals/dependency-injection) per aggiungere `RazorPagesMovieContext` alla pagina.</span><span class="sxs-lookup"><span data-stu-id="0289a-220">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="0289a-221">Le pagine con impalcature seguono questo modello.</span><span class="sxs-lookup"><span data-stu-id="0289a-221">The scaffolded pages follow this pattern.</span></span> <span data-ttu-id="0289a-222">Vedere [Codice asincrono](xref:data/ef-rp/intro#asynchronous-code) per altre informazioni sulla programmazione asincrona con Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="0289a-222">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="0289a-223">Quando viene effettuata una richiesta per la pagina, il `OnGetAsync` metodo restituisce un elenco di filmati alla Razor pagina.</span><span class="sxs-lookup"><span data-stu-id="0289a-223">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="0289a-224">`OnGetAsync` o `OnGet` viene chiamato su una Razor pagina per inizializzare lo stato della pagina.</span><span class="sxs-lookup"><span data-stu-id="0289a-224">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="0289a-225">In questo caso, `OnGetAsync` ottiene un elenco di filmati e li visualizza.</span><span class="sxs-lookup"><span data-stu-id="0289a-225">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="0289a-226">Quando `OnGet` restituisce `void` o `OnGetAsync` restituisce `Task` , non viene utilizzato alcun metodo restituito.</span><span class="sxs-lookup"><span data-stu-id="0289a-226">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return method is used.</span></span> <span data-ttu-id="0289a-227">Quando il tipo restituito è `IActionResult` o `Task<IActionResult>`, è necessario specificare un'istruzione return.</span><span class="sxs-lookup"><span data-stu-id="0289a-227">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="0289a-228">Ad esempio, il metodo *pages/Movies/ Create . cshtml.cs* `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="0289a-228">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="0289a-229">Esaminare la pagina *pages/Movies/ Index . cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="0289a-229">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="0289a-230">Razor consente di eseguire la transizione da HTML a C# o a un Razor markup specifico.</span><span class="sxs-lookup"><span data-stu-id="0289a-230">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="0289a-231">Quando un `@` simbolo è seguito da una [ Razor parola chiave riservata](xref:mvc/views/razor#razor-reserved-keywords), esegue la transizione in un Razor markup specifico, in caso contrario passa a C#.</span><span class="sxs-lookup"><span data-stu-id="0289a-231">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="0289a-232">La `@page` Razor direttiva trasforma il file in un'azione MVC, il che significa che può gestire le richieste.</span><span class="sxs-lookup"><span data-stu-id="0289a-232">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="0289a-233">`@page` deve essere la prima Razor direttiva in una pagina.</span><span class="sxs-lookup"><span data-stu-id="0289a-233">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="0289a-234">`@page` è un esempio di transizione di Razor markup specifico a.</span><span class="sxs-lookup"><span data-stu-id="0289a-234">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="0289a-235">Per ulteriori informazioni, vedere la [ Razor sintassi](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="0289a-235">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="0289a-236">La direttiva @model</span><span class="sxs-lookup"><span data-stu-id="0289a-236">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="0289a-237">La `@model` direttiva specifica il tipo del modello passato alla Razor pagina.</span><span class="sxs-lookup"><span data-stu-id="0289a-237">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="0289a-238">Nell'esempio precedente la `@model` riga rende `PageModel` disponibile la classe derivata da per la Razor pagina.</span><span class="sxs-lookup"><span data-stu-id="0289a-238">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="0289a-239">Il modello viene usato negli  [helper HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)`@Html.DisplayNameFor` e `@Html.DisplayFor` nella pagina.</span><span class="sxs-lookup"><span data-stu-id="0289a-239">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="html-helpers"></a><span data-ttu-id="0289a-240">Helper HTML</span><span class="sxs-lookup"><span data-stu-id="0289a-240">HTML Helpers</span></span>

<span data-ttu-id="0289a-241">Esaminare l'espressione lambda usata nell'helper HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="0289a-241">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="0289a-242">L'helper HTML <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> controlla la proprietà `Title` a cui fa riferimento nell'espressione lambda per determinare il nome visualizzato.</span><span class="sxs-lookup"><span data-stu-id="0289a-242">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="0289a-243">L'espressione lambda viene controllata anziché valutata.</span><span class="sxs-lookup"><span data-stu-id="0289a-243">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="0289a-244">Non sussiste pertanto violazione di accesso quando `model`, `model.Movie`, o `model.Movie[0]` sono `null` o vuoti.</span><span class="sxs-lookup"><span data-stu-id="0289a-244">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="0289a-245">Quando viene valutata l'espressione lambda, ad esempio con `@Html.DisplayFor(modelItem => item.Title)` , vengono valutati i valori delle proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="0289a-245">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>
### <a name="the-layout-page"></a><span data-ttu-id="0289a-246">Pagina di layout</span><span class="sxs-lookup"><span data-stu-id="0289a-246">The layout page</span></span>

<span data-ttu-id="0289a-247">Selezionare il menu collegamenti **Razor PagesMovie** , **Home** e **privacy**.</span><span class="sxs-lookup"><span data-stu-id="0289a-247">Select the menu links **RazorPagesMovie** , **Home** , and **Privacy**.</span></span> <span data-ttu-id="0289a-248">Ogni pagina mostra lo stesso layout di menu.</span><span class="sxs-lookup"><span data-stu-id="0289a-248">Each page shows the same menu layout.</span></span> <span data-ttu-id="0289a-249">Il layout di menu viene implementato nel file *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0289a-249">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="0289a-250">Aprire ed esaminare il file *pages/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="0289a-250">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="0289a-251">I modelli di [layout](xref:mvc/views/layout) consentono di specificare il layout del contenitore HTML di un sito in un'unica posizione e quindi di applicarlo in più pagine del sito.</span><span class="sxs-lookup"><span data-stu-id="0289a-251">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of a site in one place and then apply it across multiple pages in the site.</span></span> <span data-ttu-id="0289a-252">Trovare la riga `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="0289a-252">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="0289a-253">`RenderBody` è un segnaposto dove tutte le visualizzazioni specifiche della pagina vengono presentate, *incapsulate* nella pagina di layout.</span><span class="sxs-lookup"><span data-stu-id="0289a-253">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="0289a-254">Ad esempio, se si seleziona il collegamento **Privacy** , il rendering della visualizzazione **Pages/Privacy.cshtml** viene eseguito all'interno del metodo `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="0289a-254">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="0289a-255">ViewData e layout</span><span class="sxs-lookup"><span data-stu-id="0289a-255">ViewData and layout</span></span>

<span data-ttu-id="0289a-256">Si consideri il codice seguente dal file *pages/Movies/ Index . cshtml* :</span><span class="sxs-lookup"><span data-stu-id="0289a-256">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="0289a-257">Il codice evidenziato precedente è un esempio di Razor transizione in C#.</span><span class="sxs-lookup"><span data-stu-id="0289a-257">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="0289a-258">Tra i caratteri `{` e `}` è racchiuso un blocco di codice C#.</span><span class="sxs-lookup"><span data-stu-id="0289a-258">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="0289a-259">La classe di base `PageModel` ha una proprietà del dizionario `ViewData` che può essere usata per aggiungere i dati da passare a una visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="0289a-259">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="0289a-260">Gli oggetti vengono aggiunti al dizionario `ViewData` usando uno schema chiave/valore.</span><span class="sxs-lookup"><span data-stu-id="0289a-260">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="0289a-261">Nell'esempio precedente la proprietà `Title` viene aggiunta al dizionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="0289a-261">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="0289a-262">La proprietà `Title` viene usata nel file *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0289a-262">The `Title` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="0289a-263">Il markup seguente illustra le prime righe del file *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0289a-263">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="0289a-264">La riga `@*Markup removed for brevity.*@` è un Razor commento.</span><span class="sxs-lookup"><span data-stu-id="0289a-264">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="0289a-265">Diversamente dai commenti HTML `<!-- -->` , Razor i commenti non vengono inviati al client.</span><span class="sxs-lookup"><span data-stu-id="0289a-265">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="0289a-266">Per ulteriori informazioni, vedere la pagina relativa all'introduzione [al codice HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) .</span><span class="sxs-lookup"><span data-stu-id="0289a-266">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="0289a-267">Aggiornare il layout</span><span class="sxs-lookup"><span data-stu-id="0289a-267">Update the layout</span></span>

<span data-ttu-id="0289a-268">Modificare l' `<title>` elemento nel file *pages/Shared/_Layout. cshtml* per visualizzare il **film** anziché **Razor PagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="0289a-268">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="0289a-269">Trovare l'elemento di ancoraggio seguente nel file *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0289a-269">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="0289a-270">Sostituire l'elemento precedente con il markup seguente.</span><span class="sxs-lookup"><span data-stu-id="0289a-270">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="0289a-271">L'elemento di ancoraggio precedente è un [helper tag](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="0289a-271">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="0289a-272">In questo caso, si tratta dell'[helper tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="0289a-272">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="0289a-273">L' `asp-page="/Movies/Index"` attributo e il valore dell'helper Tag creano un collegamento alla `/Movies/Index` Razor pagina.</span><span class="sxs-lookup"><span data-stu-id="0289a-273">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="0289a-274">Poiché il valore dell'attributo `asp-area` è vuoto, l'area non viene usata nel collegamento.</span><span class="sxs-lookup"><span data-stu-id="0289a-274">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="0289a-275">Per altre informazioni, vedere [Aree](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="0289a-275">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="0289a-276">Salvare le modifiche e testare l'app selezionando il collegamento **RpMovie**.</span><span class="sxs-lookup"><span data-stu-id="0289a-276">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="0289a-277">In caso di problemi, vedere il file [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) in GitHub.</span><span class="sxs-lookup"><span data-stu-id="0289a-277">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="0289a-278">Testare gli altri collegamenti ( **Home** , **RpMovie** , **Create** , **Edit** e **Delete** ).</span><span class="sxs-lookup"><span data-stu-id="0289a-278">Test the other links ( **Home** , **RpMovie** , **Create** , **Edit** , and **Delete** ).</span></span> <span data-ttu-id="0289a-279">Ogni pagina imposta il titolo, che è possibile visualizzare nella scheda del browser. Quando si aggiunge un segnalibro a una pagina, il titolo viene usato per il segnalibro.</span><span class="sxs-lookup"><span data-stu-id="0289a-279">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="0289a-280">Potrebbe non essere possibile immettere virgole decimali nel campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="0289a-280">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="0289a-281">Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per le impostazioni locali diverse dall'inglese che usano una virgola (",") per un separatore decimale e formati non US-English data, è necessario eseguire le operazioni per globalizzare l'app.</span><span class="sxs-lookup"><span data-stu-id="0289a-281">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="0289a-282">[Problema 4076 su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) per istruzioni sull'aggiunta della virgola decimale.</span><span class="sxs-lookup"><span data-stu-id="0289a-282">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="0289a-283">La proprietà `Layout` viene impostata nel file *Pages/_ViewStart.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="0289a-283">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="0289a-284">Il markup precedente imposta il file di layout su *pages/Shared/_Layout. cshtml* per tutti Razor i file nella cartella *pages* .</span><span class="sxs-lookup"><span data-stu-id="0289a-284">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="0289a-285">Vedere [Layout](xref:razor-pages/index#layout) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="0289a-285">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-no-loccreate-page-model"></a><span data-ttu-id="0289a-286">CreateModello di pagina</span><span class="sxs-lookup"><span data-stu-id="0289a-286">The Create page model</span></span>

<span data-ttu-id="0289a-287">Esaminare il modello di pagina *pages/Movies/ Create . cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="0289a-287">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="0289a-288">Il metodo `OnGet` inizializza lo stato necessario per la pagina.</span><span class="sxs-lookup"><span data-stu-id="0289a-288">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="0289a-289">La Create pagina non dispone di alcuno stato da inizializzare, pertanto `Page` viene restituito.</span><span class="sxs-lookup"><span data-stu-id="0289a-289">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="0289a-290">Più avanti nell'esercitazione viene illustrato lo stato di inizializzazione del metodo `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="0289a-290">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="0289a-291">Il `Page` metodo crea un `PageResult` oggetto che esegue il rendering della pagina *Create . cshtml* .</span><span class="sxs-lookup"><span data-stu-id="0289a-291">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="0289a-292">La `Movie` Proprietà usa l'attributo [[BindProperty]] <xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> per acconsentire esplicitamente all' [associazione di modelli](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="0289a-292">The `Movie` property uses the [[BindProperty]]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="0289a-293">Quando il Create form inserisce i valori del form, il runtime di ASP.NET Core associa i valori inviati al `Movie` modello.</span><span class="sxs-lookup"><span data-stu-id="0289a-293">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="0289a-294">Il metodo `OnPostAsync` viene eseguito quando la pagina inserisce i dati del modulo:</span><span class="sxs-lookup"><span data-stu-id="0289a-294">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="0289a-295">Se il modello contiene errori, il modulo viene nuovamente visualizzato insieme ai dati del modulo inviati.</span><span class="sxs-lookup"><span data-stu-id="0289a-295">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="0289a-296">La maggior parte degli errori del modello sono riscontrabili sul lato client prima che il modulo sia inviato.</span><span class="sxs-lookup"><span data-stu-id="0289a-296">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="0289a-297">La registrazione di un valore per il campo data non convertibile in una data è un esempio di errore del modello.</span><span class="sxs-lookup"><span data-stu-id="0289a-297">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="0289a-298">Durante l'esercitazione saranno poi trattate nel dettaglio la convalida lato client e la convalida del modello.</span><span class="sxs-lookup"><span data-stu-id="0289a-298">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="0289a-299">Se non sono presenti errori del modello, i dati vengono salvati e il browser viene reindirizzato alla Index pagina.</span><span class="sxs-lookup"><span data-stu-id="0289a-299">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-no-loccreate-no-locrazor-page"></a><span data-ttu-id="0289a-300">Create Razor Pagina</span><span class="sxs-lookup"><span data-stu-id="0289a-300">The Create Razor Page</span></span>

<span data-ttu-id="0289a-301">Esaminare il file di paging *pages/Movies/ Create . cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="0289a-301">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="0289a-302">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0289a-302">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0289a-303">Per visualizzare il tag `<form method="post">`, Visual Studio usa un tipo di carattere in grassetto distintivo specifico per gli helper tag:</span><span class="sxs-lookup"><span data-stu-id="0289a-303">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Visualizzazione VS17 della pagina::: NO-LOC (Create):::. cshtml](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="0289a-305">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0289a-305">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0289a-306">Per altre informazioni sugli helper tag, ad esempio `<form method="post">`, vedere [Helper tag in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="0289a-306">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0289a-307">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="0289a-307">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0289a-308">Per visualizzare il tag `<form method="post">`, Visual Studio per Mac usa un tipo di carattere in grassetto distintivo specifico per gli helper tag.</span><span class="sxs-lookup"><span data-stu-id="0289a-308">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="0289a-309">L'elemento `<form method="post">` è un [helper tag del modulo](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="0289a-309">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="0289a-310">L'helper tag del modulo include automaticamente un [token antifalsificazione](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="0289a-310">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="0289a-311">Il motore di ponteggi crea Razor markup per ogni campo nel modello, ad eccezione dell'ID, simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="0289a-311">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="0289a-312">Gli [Helper tag di convalida](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` e `<span asp-validation-for` ) visualizzano gli errori di convalida.</span><span class="sxs-lookup"><span data-stu-id="0289a-312">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="0289a-313">Il tema della convalida viene trattato in modo più dettagliato successivamente in questa serie.</span><span class="sxs-lookup"><span data-stu-id="0289a-313">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="0289a-314">L' [Helper tag di etichetta](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) genera la didascalia dell'etichetta e l' `[for]` attributo per la `Title` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="0289a-314">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="0289a-315">L' [Helper tag di input](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) USA gli attributi [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produce gli attributi HTML necessari per la convalida jQuery sul lato client.</span><span class="sxs-lookup"><span data-stu-id="0289a-315">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0289a-316">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="0289a-316">Additional resources</span></span>

* [<span data-ttu-id="0289a-317">Versione YouTube dell'esercitazione</span><span class="sxs-lookup"><span data-stu-id="0289a-317">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="0289a-318">[Precedente: aggiungere un modello](xref:tutorials/razor-pages/model) 
>  Passaggio [successivo: utilizzo di un database](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="0289a-318">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
