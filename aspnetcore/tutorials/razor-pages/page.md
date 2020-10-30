---
title: 'Parte 3, :::no-loc(Razor)::: pagine con impalcatura in ASP.NET Core'
author: rick-anderson
description: 'Parte 3 della serie di esercitazioni sulle :::no-loc(Razor)::: pagine.'
ms.author: riande
ms.date: 08/17/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/razor-pages/page
ms.openlocfilehash: 7d0085e1d444de30ca124ef544668122ab350c93
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060053"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="f7543-103">Parte 3, :::no-loc(Razor)::: pagine con impalcatura in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f7543-103">Part 3, scaffolded :::no-loc(Razor)::: Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f7543-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f7543-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f7543-105">Questa esercitazione esamina le :::no-loc(Razor)::: pagine create dall'impalcatura nell' [esercitazione precedente](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="f7543-105">This tutorial examines the :::no-loc(Razor)::: Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="f7543-106">Pagine di creazione, eliminazione, dettagli e modifica</span><span class="sxs-lookup"><span data-stu-id="f7543-106">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="f7543-107">Esaminare il modello di pagina *Pages/Movies/Index.cshtml.cs* : </span><span class="sxs-lookup"><span data-stu-id="f7543-107">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="f7543-108">:::no-loc(Razor)::: Le pagine sono derivate da `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="f7543-108">:::no-loc(Razor)::: Pages are derived from `PageModel`.</span></span> <span data-ttu-id="f7543-109">Per convenzione, la classe derivata `PageModel` viene denominata `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="f7543-109">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="f7543-110">Il costruttore usa l'[inserimento delle dipendenze](xref:fundamentals/dependency-injection) per aggiungere `:::no-loc(Razor):::PagesMovieContext` alla pagina.</span><span class="sxs-lookup"><span data-stu-id="f7543-110">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `:::no-loc(Razor):::PagesMovieContext` to the page.</span></span> <span data-ttu-id="f7543-111">Tutte le pagine create tramite scaffolding seguono questo schema.</span><span class="sxs-lookup"><span data-stu-id="f7543-111">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="f7543-112">Vedere [Codice asincrono](xref:data/ef-rp/intro#asynchronous-code) per altre informazioni sulla programmazione asincrona con Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="f7543-112">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="f7543-113">Quando viene effettuata una richiesta per la pagina, il `OnGetAsync` metodo restituisce un elenco di filmati alla :::no-loc(Razor)::: pagina.</span><span class="sxs-lookup"><span data-stu-id="f7543-113">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="f7543-114">`OnGetAsync``OnGet`viene chiamato o per inizializzare lo stato della pagina.</span><span class="sxs-lookup"><span data-stu-id="f7543-114">`OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="f7543-115">In questo caso, `OnGetAsync` ottiene un elenco di filmati e li visualizza.</span><span class="sxs-lookup"><span data-stu-id="f7543-115">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="f7543-116">Quando `OnGet` restituisce `void` o `OnGetAsync` restituisce `Task` , non viene utilizzata alcuna istruzione return.</span><span class="sxs-lookup"><span data-stu-id="f7543-116">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return statement is used.</span></span> <span data-ttu-id="f7543-117">Quando il tipo restituito è `IActionResult` o `Task<IActionResult>`, è necessario specificare un'istruzione return.</span><span class="sxs-lookup"><span data-stu-id="f7543-117">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="f7543-118">Ad esempio, il metodo *Pages/Movies/Create.cshtml.cs* `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="f7543-118">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="f7543-119">Esaminare la pagina *pages/movies/index. cshtml* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="f7543-119">Examine the *Pages/Movies/Index.cshtml* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="f7543-120">:::no-loc(Razor)::: consente di eseguire la transizione da HTML a C# o a un :::no-loc(Razor)::: markup specifico.</span><span class="sxs-lookup"><span data-stu-id="f7543-120">:::no-loc(Razor)::: can transition from HTML into C# or into :::no-loc(Razor):::-specific markup.</span></span> <span data-ttu-id="f7543-121">Quando un `@` simbolo è seguito da una [ :::no-loc(Razor)::: parola chiave riservata](xref:mvc/views/razor#razor-reserved-keywords), esegue la transizione in un :::no-loc(Razor)::: markup specifico, in caso contrario passa a C#.</span><span class="sxs-lookup"><span data-stu-id="f7543-121">When an `@` symbol is followed by a [:::no-loc(Razor)::: reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into :::no-loc(Razor):::-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="f7543-122">La direttiva @page</span><span class="sxs-lookup"><span data-stu-id="f7543-122">The @page directive</span></span>

<span data-ttu-id="f7543-123">La `@page` :::no-loc(Razor)::: direttiva rende il file un'azione MVC, il che significa che può gestire le richieste.</span><span class="sxs-lookup"><span data-stu-id="f7543-123">The `@page` :::no-loc(Razor)::: directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="f7543-124">`@page` deve essere la prima :::no-loc(Razor)::: direttiva in una pagina.</span><span class="sxs-lookup"><span data-stu-id="f7543-124">`@page` must be the first :::no-loc(Razor)::: directive on a page.</span></span> <span data-ttu-id="f7543-125">`@page` è un esempio di transizione di :::no-loc(Razor)::: markup specifico a.</span><span class="sxs-lookup"><span data-stu-id="f7543-125">`@page` is an example of transitioning into :::no-loc(Razor):::-specific markup.</span></span> <span data-ttu-id="f7543-126">Per ulteriori informazioni, vedere la [ :::no-loc(Razor)::: sintassi](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="f7543-126">See [:::no-loc(Razor)::: syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="f7543-127">Esaminare l'espressione lambda usata nell'helper HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="f7543-127">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="f7543-128">L'helper HTML `DisplayNameFor` controlla la proprietà `Title` a cui fa riferimento nell'espressione lambda per determinare il nome visualizzato.</span><span class="sxs-lookup"><span data-stu-id="f7543-128">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="f7543-129">L'espressione lambda viene controllata anziché valutata.</span><span class="sxs-lookup"><span data-stu-id="f7543-129">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="f7543-130">Ciò significa che non esiste alcuna violazione di accesso quando `model` , `model.Movie` o `model.Movie[0]` è `null` o vuoto.</span><span class="sxs-lookup"><span data-stu-id="f7543-130">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="f7543-131">Quando invece l'espressione lambda viene valutata (ad esempio con `@Html.DisplayFor(modelItem => item.Title)`), vengono valutati i valori proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="f7543-131">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="f7543-132">La direttiva @model</span><span class="sxs-lookup"><span data-stu-id="f7543-132">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="f7543-133">La `@model` direttiva specifica il tipo del modello passato alla :::no-loc(Razor)::: pagina.</span><span class="sxs-lookup"><span data-stu-id="f7543-133">The `@model` directive specifies the type of the model passed to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="f7543-134">Nell'esempio precedente la `@model` riga rende `PageModel` disponibile la classe derivata da per la :::no-loc(Razor)::: pagina.</span><span class="sxs-lookup"><span data-stu-id="f7543-134">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="f7543-135">Il modello viene usato negli  [helper HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)`@Html.DisplayNameFor` e `@Html.DisplayFor` nella pagina.</span><span class="sxs-lookup"><span data-stu-id="f7543-135">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="f7543-136">Pagina di layout</span><span class="sxs-lookup"><span data-stu-id="f7543-136">The layout page</span></span>

<span data-ttu-id="f7543-137">Selezionare i collegamenti ai menu ( **:::no-loc(Razor)::: PagesMovie** , **Home** e **privacy** ).</span><span class="sxs-lookup"><span data-stu-id="f7543-137">Select the menu links ( **:::no-loc(Razor):::PagesMovie** , **Home** , and **Privacy** ).</span></span> <span data-ttu-id="f7543-138">Ogni pagina mostra lo stesso layout di menu.</span><span class="sxs-lookup"><span data-stu-id="f7543-138">Each page shows the same menu layout.</span></span> <span data-ttu-id="f7543-139">Il layout di menu viene implementato nel file *Pages/Shared/_Layout.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f7543-139">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="f7543-140">Aprire il file *Pages/Shared/_Layout.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f7543-140">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="f7543-141">I modelli [Layout](xref:mvc/views/layout) consentono di:</span><span class="sxs-lookup"><span data-stu-id="f7543-141">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="f7543-142">Specificare il layout del contenitore HTML in un'unica posizione.</span><span class="sxs-lookup"><span data-stu-id="f7543-142">Specified in one place.</span></span>
* <span data-ttu-id="f7543-143">Applicare il layout in più pagine del sito.</span><span class="sxs-lookup"><span data-stu-id="f7543-143">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="f7543-144">Trovare la riga `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="f7543-144">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="f7543-145">`RenderBody` è un segnaposto dove tutte le visualizzazioni specifiche della pagina vengono presentate, *incapsulate* nella pagina di layout.</span><span class="sxs-lookup"><span data-stu-id="f7543-145">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="f7543-146">Ad esempio, selezionare il collegamento **Privacy** per eseguire il rendering della visualizzazione *Pages/Privacy.cshtml* all'interno del metodo `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="f7543-146">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="f7543-147">ViewData e layout</span><span class="sxs-lookup"><span data-stu-id="f7543-147">ViewData and layout</span></span>

<span data-ttu-id="f7543-148">Si consideri il markup seguente dal file *Pages/Movies/Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f7543-148">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="f7543-149">Il markup evidenziato sopra è un esempio di :::no-loc(Razor)::: transizione in C#.</span><span class="sxs-lookup"><span data-stu-id="f7543-149">The preceding highlighted markup is an example of :::no-loc(Razor)::: transitioning into C#.</span></span> <span data-ttu-id="f7543-150">Tra i caratteri `{` e `}` è racchiuso un blocco di codice C#.</span><span class="sxs-lookup"><span data-stu-id="f7543-150">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="f7543-151">La `PageModel` classe base contiene una `ViewData` Proprietà Dictionary che può essere utilizzata per passare dati a una visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="f7543-151">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="f7543-152">Gli oggetti vengono aggiunti al dizionario `ViewData` usando uno schema chiave/valore.</span><span class="sxs-lookup"><span data-stu-id="f7543-152">Objects are added to the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="f7543-153">Nell'esempio precedente la proprietà `"Title"` viene aggiunta al dizionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="f7543-153">In the preceding sample, the `"Title"` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="f7543-154">La proprietà `"Title"` viene usata nel file *Pages/Shared/_Layout.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f7543-154">The `"Title"` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="f7543-155">Il markup seguente illustra le prime righe del file *_Layout.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f7543-155">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="f7543-156">La riga `@*Markup removed for brevity.*@` è un :::no-loc(Razor)::: commento.</span><span class="sxs-lookup"><span data-stu-id="f7543-156">The line `@*Markup removed for brevity.*@` is a :::no-loc(Razor)::: comment.</span></span> <span data-ttu-id="f7543-157">Diversamente dai commenti HTML ( `<!-- -->` ), :::no-loc(Razor)::: i commenti non vengono inviati al client.</span><span class="sxs-lookup"><span data-stu-id="f7543-157">Unlike HTML comments (`<!-- -->`), :::no-loc(Razor)::: comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="f7543-158">Aggiornare il layout</span><span class="sxs-lookup"><span data-stu-id="f7543-158">Update the layout</span></span>

<span data-ttu-id="f7543-159">Modificare l' `<title>` elemento nel file *pages/Shared/_Layout. cshtml* per visualizzare il **film** anziché **:::no-loc(Razor)::: PagesMovie** .</span><span class="sxs-lookup"><span data-stu-id="f7543-159">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **:::no-loc(Razor):::PagesMovie** .</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="f7543-160">Trovare l'elemento di ancoraggio seguente nel file *Pages/Shared/_Layout.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f7543-160">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">:::no-loc(Razor):::PagesMovie</a>
```

<span data-ttu-id="f7543-161">Sostituire l'elemento precedente con il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="f7543-161">Replace the preceding element with the following markup:</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="f7543-162">L'elemento di ancoraggio precedente è un [helper tag](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="f7543-162">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="f7543-163">In questo caso, si tratta dell'[helper tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="f7543-163">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="f7543-164">L' `asp-page="/Movies/Index"` attributo e il valore dell'helper Tag creano un collegamento alla `/Movies/Index` :::no-loc(Razor)::: pagina.</span><span class="sxs-lookup"><span data-stu-id="f7543-164">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="f7543-165">Poiché il valore dell'attributo `asp-area` è vuoto, l'area non viene usata nel collegamento.</span><span class="sxs-lookup"><span data-stu-id="f7543-165">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="f7543-166">Per altre informazioni, vedere [Aree](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="f7543-166">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="f7543-167">Salvare le modifiche e testare l'app selezionando il collegamento **RpMovie** .</span><span class="sxs-lookup"><span data-stu-id="f7543-167">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="f7543-168">In caso di problemi, vedere il file [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Shared/_Layout.cshtml) in GitHub.</span><span class="sxs-lookup"><span data-stu-id="f7543-168">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="f7543-169">Testare gli altri collegamenti ( **Home** , **RpMovie** , **Create** , **Edit** e **Delete** ).</span><span class="sxs-lookup"><span data-stu-id="f7543-169">Test the other links ( **Home** , **RpMovie** , **Create** , **Edit** , and **Delete** ).</span></span> <span data-ttu-id="f7543-170">Ogni pagina imposta il titolo, che è possibile visualizzare nella scheda del browser. Quando si aggiunge un segnalibro a una pagina, il titolo viene usato per il segnalibro.</span><span class="sxs-lookup"><span data-stu-id="f7543-170">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="f7543-171">Potrebbe non essere possibile immettere virgole decimali nel campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="f7543-171">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="f7543-172">Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese degli Stati Uniti, è necessario eseguire alcuni passaggi per globalizzare l'app.</span><span class="sxs-lookup"><span data-stu-id="f7543-172">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="f7543-173">Vedere questo [problema 4076 su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) per istruzioni sull'aggiunta della virgola decimale.</span><span class="sxs-lookup"><span data-stu-id="f7543-173">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="f7543-174">La proprietà `Layout` viene impostata nel file *Pages/_ViewStart.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f7543-174">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="f7543-175">Il markup precedente imposta il file di layout su *pages/Shared/_Layout. cshtml* per tutti :::no-loc(Razor)::: i file nella cartella *pages* .</span><span class="sxs-lookup"><span data-stu-id="f7543-175">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all :::no-loc(Razor)::: files under the *Pages* folder.</span></span> <span data-ttu-id="f7543-176">Vedere [Layout](xref:razor-pages/index#layout) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="f7543-176">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="f7543-177">Modello di pagina di creazione</span><span class="sxs-lookup"><span data-stu-id="f7543-177">The Create page model</span></span>

<span data-ttu-id="f7543-178">Esaminare il modello di pagina *Pages/Movies/Create.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="f7543-178">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="f7543-179">Il metodo `OnGet` inizializza lo stato necessario per la pagina.</span><span class="sxs-lookup"><span data-stu-id="f7543-179">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="f7543-180">La pagina di creazione non possiede uno stato da inizializzare. Viene restituito `Page`.</span><span class="sxs-lookup"><span data-stu-id="f7543-180">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="f7543-181">Più avanti nell'esercitazione viene visualizzato un esempio di inizializzazione dello stato con `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="f7543-181">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="f7543-182">Il metodo `Page` crea un oggetto `PageResult` che esegue il rendering della pagina *Create.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f7543-182">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="f7543-183">La proprietà `Movie` usa l'attributo `[BindProperty]` per consentire l'[associazione di modelli](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="f7543-183">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="f7543-184">Dopo che il modulo di creazione registra i valori, il runtime di ASP.NET Core associa i valori registrati al modello `Movie`.</span><span class="sxs-lookup"><span data-stu-id="f7543-184">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="f7543-185">Il metodo `OnPostAsync` viene eseguito quando la pagina inserisce i dati del modulo:</span><span class="sxs-lookup"><span data-stu-id="f7543-185">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="f7543-186">Se il modello contiene errori, il modulo viene nuovamente visualizzato insieme ai dati del modulo inviati.</span><span class="sxs-lookup"><span data-stu-id="f7543-186">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="f7543-187">La maggior parte degli errori del modello sono riscontrabili sul lato client prima che il modulo sia inviato.</span><span class="sxs-lookup"><span data-stu-id="f7543-187">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="f7543-188">La registrazione di un valore per il campo data non convertibile in una data è un esempio di errore del modello.</span><span class="sxs-lookup"><span data-stu-id="f7543-188">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="f7543-189">Durante l'esercitazione saranno poi trattate nel dettaglio la convalida lato client e la convalida del modello.</span><span class="sxs-lookup"><span data-stu-id="f7543-189">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="f7543-190">Se non sono presenti errori nel modello, i dati vengono salvati e il browser viene reindirizzato alla pagina di indice.</span><span class="sxs-lookup"><span data-stu-id="f7543-190">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="f7543-191">Pagina Crea :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="f7543-191">The Create :::no-loc(Razor)::: Page</span></span>

<span data-ttu-id="f7543-192">Esaminare il file di paging *pages/Movies/create. cshtml* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="f7543-192">Examine the *Pages/Movies/Create.cshtml* :::no-loc(Razor)::: Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="f7543-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7543-193">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f7543-194">Visual Studio visualizza i tag seguenti con un tipo di carattere in grassetto distintivo specifico per gli helper tag:</span><span class="sxs-lookup"><span data-stu-id="f7543-194">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Visualizzazione di VS17 della pagina Create.cshtml](page/_static/th3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f7543-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f7543-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f7543-197">Nel markup precedente sono visualizzati gli helper tag seguenti:</span><span class="sxs-lookup"><span data-stu-id="f7543-197">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f7543-198">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="f7543-198">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f7543-199">Visual Studio visualizza i tag seguenti con un tipo di carattere in grassetto distintivo specifico per gli helper tag:</span><span class="sxs-lookup"><span data-stu-id="f7543-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="f7543-200">L'elemento `<form method="post">` è un [helper tag del modulo](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="f7543-200">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="f7543-201">L'helper tag del modulo include automaticamente un [token antifalsificazione](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="f7543-201">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="f7543-202">Il motore di ponteggi crea :::no-loc(Razor)::: markup per ogni campo nel modello (ad eccezione dell'ID) simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="f7543-202">The scaffolding engine creates :::no-loc(Razor)::: markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="f7543-203">Gli [Helper tag di convalida](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` e `<span asp-validation-for` ) visualizzano gli errori di convalida.</span><span class="sxs-lookup"><span data-stu-id="f7543-203">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="f7543-204">Il tema della convalida viene trattato in modo più dettagliato successivamente in questa serie.</span><span class="sxs-lookup"><span data-stu-id="f7543-204">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="f7543-205">L' [Helper tag di etichetta](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) genera la didascalia dell'etichetta e l' `for` attributo per la `Title` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="f7543-205">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="f7543-206">L' [Helper tag di input](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) USA gli attributi [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produce gli attributi HTML necessari per la convalida jQuery sul lato client.</span><span class="sxs-lookup"><span data-stu-id="f7543-206">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="f7543-207">Per altre informazioni sugli helper tag, ad esempio `<form method="post">`, vedere [Helper tag in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="f7543-207">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f7543-208">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="f7543-208">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="f7543-209">[Precedente: aggiunta di un modello](xref:tutorials/razor-pages/model) 
>  Passaggio [successivo: database](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="f7543-209">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f7543-210">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f7543-210">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f7543-211">Questa esercitazione esamina le :::no-loc(Razor)::: pagine create dall'impalcatura nell' [esercitazione precedente](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="f7543-211">This tutorial examines the :::no-loc(Razor)::: Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="f7543-212">[Visualizzare o scaricare il campione ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22).</span><span class="sxs-lookup"><span data-stu-id="f7543-212">[View or download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="f7543-213">Pagine di creazione, eliminazione, dettagli e modifica</span><span class="sxs-lookup"><span data-stu-id="f7543-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="f7543-214">Esaminare il modello di pagina *Pages/Movies/Index.cshtml.cs* : </span><span class="sxs-lookup"><span data-stu-id="f7543-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="f7543-215">:::no-loc(Razor)::: Le pagine sono derivate da `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="f7543-215">:::no-loc(Razor)::: Pages are derived from `PageModel`.</span></span> <span data-ttu-id="f7543-216">Per convenzione, la classe derivata `PageModel` viene denominata `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="f7543-216">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="f7543-217">Il costruttore usa l'[inserimento delle dipendenze](xref:fundamentals/dependency-injection) per aggiungere `:::no-loc(Razor):::PagesMovieContext` alla pagina.</span><span class="sxs-lookup"><span data-stu-id="f7543-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `:::no-loc(Razor):::PagesMovieContext` to the page.</span></span> <span data-ttu-id="f7543-218">Tutte le pagine create tramite scaffolding seguono questo schema.</span><span class="sxs-lookup"><span data-stu-id="f7543-218">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="f7543-219">Vedere [Codice asincrono](xref:data/ef-rp/intro#asynchronous-code) per altre informazioni sulla programmazione asincrona con Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="f7543-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="f7543-220">Quando viene effettuata una richiesta per la pagina, il `OnGetAsync` metodo restituisce un elenco di filmati alla :::no-loc(Razor)::: pagina.</span><span class="sxs-lookup"><span data-stu-id="f7543-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="f7543-221">`OnGetAsync` o `OnGet` viene chiamato su una :::no-loc(Razor)::: pagina per inizializzare lo stato della pagina.</span><span class="sxs-lookup"><span data-stu-id="f7543-221">`OnGetAsync` or `OnGet` is called on a :::no-loc(Razor)::: Page to initialize the state for the page.</span></span> <span data-ttu-id="f7543-222">In questo caso, `OnGetAsync` ottiene un elenco di filmati e li visualizza.</span><span class="sxs-lookup"><span data-stu-id="f7543-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="f7543-223">Quando `OnGet` restituisce `void` o `OnGetAsync` restituisce`Task`, non viene usato alcun metodo restituito.</span><span class="sxs-lookup"><span data-stu-id="f7543-223">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="f7543-224">Quando il tipo restituito è `IActionResult` o `Task<IActionResult>`, è necessario specificare un'istruzione return.</span><span class="sxs-lookup"><span data-stu-id="f7543-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="f7543-225">Ad esempio, il metodo *Pages/Movies/Create.cshtml.cs* `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="f7543-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="f7543-226">Esaminare la pagina *pages/movies/index. cshtml* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="f7543-226">Examine the *Pages/Movies/Index.cshtml* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="f7543-227">:::no-loc(Razor)::: consente di eseguire la transizione da HTML a C# o a un :::no-loc(Razor)::: markup specifico.</span><span class="sxs-lookup"><span data-stu-id="f7543-227">:::no-loc(Razor)::: can transition from HTML into C# or into :::no-loc(Razor):::-specific markup.</span></span> <span data-ttu-id="f7543-228">Quando un `@` simbolo è seguito da una [ :::no-loc(Razor)::: parola chiave riservata](xref:mvc/views/razor#razor-reserved-keywords), esegue la transizione in un :::no-loc(Razor)::: markup specifico, in caso contrario passa a C#.</span><span class="sxs-lookup"><span data-stu-id="f7543-228">When an `@` symbol is followed by a [:::no-loc(Razor)::: reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into :::no-loc(Razor):::-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="f7543-229">La `@page` :::no-loc(Razor)::: direttiva trasforma il file in un'azione MVC, il che significa che può gestire le richieste.</span><span class="sxs-lookup"><span data-stu-id="f7543-229">The `@page` :::no-loc(Razor)::: directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="f7543-230">`@page` deve essere la prima :::no-loc(Razor)::: direttiva in una pagina.</span><span class="sxs-lookup"><span data-stu-id="f7543-230">`@page` must be the first :::no-loc(Razor)::: directive on a page.</span></span> <span data-ttu-id="f7543-231">`@page` è un esempio di transizione di :::no-loc(Razor)::: markup specifico a.</span><span class="sxs-lookup"><span data-stu-id="f7543-231">`@page` is an example of transitioning into :::no-loc(Razor):::-specific markup.</span></span> <span data-ttu-id="f7543-232">Per ulteriori informazioni, vedere la [ :::no-loc(Razor)::: sintassi](xref:mvc/views/razor#razor-syntax) .</span><span class="sxs-lookup"><span data-stu-id="f7543-232">See [:::no-loc(Razor)::: syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="f7543-233">Esaminare l'espressione lambda usata nell'helper HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="f7543-233">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="f7543-234">L'helper HTML `DisplayNameFor` controlla la proprietà `Title` a cui fa riferimento nell'espressione lambda per determinare il nome visualizzato.</span><span class="sxs-lookup"><span data-stu-id="f7543-234">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="f7543-235">L'espressione lambda viene controllata anziché valutata.</span><span class="sxs-lookup"><span data-stu-id="f7543-235">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="f7543-236">Non sussiste pertanto violazione di accesso quando `model`, `model.Movie`, o `model.Movie[0]` sono `null` o vuoti.</span><span class="sxs-lookup"><span data-stu-id="f7543-236">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="f7543-237">Quando invece l'espressione lambda viene valutata (ad esempio con `@Html.DisplayFor(modelItem => item.Title)`), vengono valutati i valori proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="f7543-237">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="f7543-238">La direttiva @model</span><span class="sxs-lookup"><span data-stu-id="f7543-238">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="f7543-239">La `@model` direttiva specifica il tipo del modello passato alla :::no-loc(Razor)::: pagina.</span><span class="sxs-lookup"><span data-stu-id="f7543-239">The `@model` directive specifies the type of the model passed to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="f7543-240">Nell'esempio precedente la `@model` riga rende `PageModel` disponibile la classe derivata da per la :::no-loc(Razor)::: pagina.</span><span class="sxs-lookup"><span data-stu-id="f7543-240">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="f7543-241">Il modello viene usato negli  [helper HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)`@Html.DisplayNameFor` e `@Html.DisplayFor` nella pagina.</span><span class="sxs-lookup"><span data-stu-id="f7543-241">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="f7543-242">Pagina di layout</span><span class="sxs-lookup"><span data-stu-id="f7543-242">The layout page</span></span>

<span data-ttu-id="f7543-243">Selezionare i collegamenti ai menu ( **:::no-loc(Razor)::: PagesMovie** , **Home** e **privacy** ).</span><span class="sxs-lookup"><span data-stu-id="f7543-243">Select the menu links ( **:::no-loc(Razor):::PagesMovie** , **Home** , and **Privacy** ).</span></span> <span data-ttu-id="f7543-244">Ogni pagina mostra lo stesso layout di menu.</span><span class="sxs-lookup"><span data-stu-id="f7543-244">Each page shows the same menu layout.</span></span> <span data-ttu-id="f7543-245">Il layout di menu viene implementato nel file *Pages/Shared/_Layout.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f7543-245">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="f7543-246">Aprire il file *Pages/Shared/_Layout.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f7543-246">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="f7543-247">I modelli di [layout](xref:mvc/views/layout) consentono di specificare il layout del contenitore HTML del sito in un'unica posizione e quindi di applicarlo in più pagine del sito.</span><span class="sxs-lookup"><span data-stu-id="f7543-247">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="f7543-248">Trovare la riga `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="f7543-248">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="f7543-249">`RenderBody` è un segnaposto dove tutte le visualizzazioni specifiche della pagina vengono presentate, *incapsulate* nella pagina di layout.</span><span class="sxs-lookup"><span data-stu-id="f7543-249">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="f7543-250">Ad esempio, se si seleziona il collegamento **Privacy** , il rendering della visualizzazione **Pages/Privacy.cshtml** viene eseguito all'interno del metodo `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="f7543-250">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="f7543-251">ViewData e layout</span><span class="sxs-lookup"><span data-stu-id="f7543-251">ViewData and layout</span></span>

<span data-ttu-id="f7543-252">Si consideri il seguente codice del file *Pages/Movies/Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f7543-252">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="f7543-253">Il codice evidenziato precedente è un esempio di :::no-loc(Razor)::: transizione in C#.</span><span class="sxs-lookup"><span data-stu-id="f7543-253">The preceding highlighted code is an example of :::no-loc(Razor)::: transitioning into C#.</span></span> <span data-ttu-id="f7543-254">Tra i caratteri `{` e `}` è racchiuso un blocco di codice C#.</span><span class="sxs-lookup"><span data-stu-id="f7543-254">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="f7543-255">La classe di base `PageModel` ha una proprietà del dizionario `ViewData` che può essere usata per aggiungere i dati da passare a una visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="f7543-255">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="f7543-256">Gli oggetti vengono aggiunti al dizionario `ViewData` usando uno schema chiave/valore.</span><span class="sxs-lookup"><span data-stu-id="f7543-256">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="f7543-257">Nell'esempio precedente la proprietà "Title" viene aggiunta al dizionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="f7543-257">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="f7543-258">La proprietà "Title" viene usata nel file *Pages/Shared/_Layout.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f7543-258">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="f7543-259">Il markup seguente illustra le prime righe del file *_Layout.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f7543-259">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="f7543-260">La riga `@*Markup removed for brevity.*@` è un :::no-loc(Razor)::: commento che non viene visualizzato nel file di layout.</span><span class="sxs-lookup"><span data-stu-id="f7543-260">The line `@*Markup removed for brevity.*@` is a :::no-loc(Razor)::: comment which doesn't appear in your layout file.</span></span> <span data-ttu-id="f7543-261">Diversamente dai commenti HTML ( `<!-- -->` ), :::no-loc(Razor)::: i commenti non vengono inviati al client.</span><span class="sxs-lookup"><span data-stu-id="f7543-261">Unlike HTML comments (`<!-- -->`), :::no-loc(Razor)::: comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="f7543-262">Aggiornare il layout</span><span class="sxs-lookup"><span data-stu-id="f7543-262">Update the layout</span></span>

<span data-ttu-id="f7543-263">Modificare l' `<title>` elemento nel file *pages/Shared/_Layout. cshtml* per visualizzare il **film** anziché **:::no-loc(Razor)::: PagesMovie** .</span><span class="sxs-lookup"><span data-stu-id="f7543-263">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **:::no-loc(Razor):::PagesMovie** .</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="f7543-264">Trovare l'elemento di ancoraggio seguente nel file *Pages/Shared/_Layout.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f7543-264">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">:::no-loc(Razor):::PagesMovie</a>
```

<span data-ttu-id="f7543-265">Sostituire l'elemento precedente con il markup seguente.</span><span class="sxs-lookup"><span data-stu-id="f7543-265">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="f7543-266">L'elemento di ancoraggio precedente è un [helper tag](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="f7543-266">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="f7543-267">In questo caso, si tratta dell'[helper tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="f7543-267">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="f7543-268">L' `asp-page="/Movies/Index"` attributo e il valore dell'helper Tag creano un collegamento alla `/Movies/Index` :::no-loc(Razor)::: pagina.</span><span class="sxs-lookup"><span data-stu-id="f7543-268">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="f7543-269">Poiché il valore dell'attributo `asp-area` è vuoto, l'area non viene usata nel collegamento.</span><span class="sxs-lookup"><span data-stu-id="f7543-269">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="f7543-270">Per altre informazioni, vedere [Aree](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="f7543-270">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="f7543-271">Salvare le modifiche e testare l'app selezionando il collegamento **RpMovie** .</span><span class="sxs-lookup"><span data-stu-id="f7543-271">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="f7543-272">In caso di problemi, vedere il file [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Shared/_Layout.cshtml) in GitHub.</span><span class="sxs-lookup"><span data-stu-id="f7543-272">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="f7543-273">Testare gli altri collegamenti ( **Home** , **RpMovie** , **Create** , **Edit** e **Delete** ).</span><span class="sxs-lookup"><span data-stu-id="f7543-273">Test the other links ( **Home** , **RpMovie** , **Create** , **Edit** , and **Delete** ).</span></span> <span data-ttu-id="f7543-274">Ogni pagina imposta il titolo, che è possibile visualizzare nella scheda del browser. Quando si aggiunge un segnalibro a una pagina, il titolo viene usato per il segnalibro.</span><span class="sxs-lookup"><span data-stu-id="f7543-274">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="f7543-275">Potrebbe non essere possibile immettere virgole decimali nel campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="f7543-275">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="f7543-276">Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese degli Stati Uniti, è necessario eseguire alcuni passaggi per globalizzare l'app.</span><span class="sxs-lookup"><span data-stu-id="f7543-276">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="f7543-277">[Problema 4076 su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) per istruzioni sull'aggiunta della virgola decimale.</span><span class="sxs-lookup"><span data-stu-id="f7543-277">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="f7543-278">La proprietà `Layout` viene impostata nel file *Pages/_ViewStart.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f7543-278">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="f7543-279">Il markup precedente imposta il file di layout su *pages/Shared/_Layout. cshtml* per tutti :::no-loc(Razor)::: i file nella cartella *pages* .</span><span class="sxs-lookup"><span data-stu-id="f7543-279">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all :::no-loc(Razor)::: files under the *Pages* folder.</span></span> <span data-ttu-id="f7543-280">Vedere [Layout](xref:razor-pages/index#layout) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="f7543-280">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="f7543-281">Modello di pagina di creazione</span><span class="sxs-lookup"><span data-stu-id="f7543-281">The Create page model</span></span>

<span data-ttu-id="f7543-282">Esaminare il modello di pagina *Pages/Movies/Create.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="f7543-282">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="f7543-283">Il metodo `OnGet` inizializza lo stato necessario per la pagina.</span><span class="sxs-lookup"><span data-stu-id="f7543-283">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="f7543-284">La pagina di creazione non possiede uno stato da inizializzare. Viene restituito `Page`.</span><span class="sxs-lookup"><span data-stu-id="f7543-284">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="f7543-285">Più avanti nell'esercitazione viene illustrato lo stato di inizializzazione del metodo `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="f7543-285">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="f7543-286">Il metodo `Page` crea un oggetto `PageResult` che esegue il rendering della pagina *Create.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f7543-286">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="f7543-287">La proprietà `Movie` usa l'attributo `[BindProperty]` per consentire l'[associazione di modelli](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="f7543-287">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="f7543-288">Dopo che il modulo di creazione registra i valori, il runtime di ASP.NET Core associa i valori registrati al modello `Movie`.</span><span class="sxs-lookup"><span data-stu-id="f7543-288">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="f7543-289">Il metodo `OnPostAsync` viene eseguito quando la pagina inserisce i dati del modulo:</span><span class="sxs-lookup"><span data-stu-id="f7543-289">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="f7543-290">Se il modello contiene errori, il modulo viene nuovamente visualizzato insieme ai dati del modulo inviati.</span><span class="sxs-lookup"><span data-stu-id="f7543-290">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="f7543-291">La maggior parte degli errori del modello sono riscontrabili sul lato client prima che il modulo sia inviato.</span><span class="sxs-lookup"><span data-stu-id="f7543-291">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="f7543-292">La registrazione di un valore per il campo data non convertibile in una data è un esempio di errore del modello.</span><span class="sxs-lookup"><span data-stu-id="f7543-292">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="f7543-293">Durante l'esercitazione saranno poi trattate nel dettaglio la convalida lato client e la convalida del modello.</span><span class="sxs-lookup"><span data-stu-id="f7543-293">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="f7543-294">Se non sono presenti errori nel modello, i dati vengono salvati e il browser viene reindirizzato alla pagina di indice.</span><span class="sxs-lookup"><span data-stu-id="f7543-294">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="f7543-295">Pagina Crea :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="f7543-295">The Create :::no-loc(Razor)::: Page</span></span>

<span data-ttu-id="f7543-296">Esaminare il file di paging *pages/Movies/create. cshtml* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="f7543-296">Examine the *Pages/Movies/Create.cshtml* :::no-loc(Razor)::: Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="f7543-297">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7543-297">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f7543-298">Per visualizzare il tag `<form method="post">`, Visual Studio usa un tipo di carattere in grassetto distintivo specifico per gli helper tag:</span><span class="sxs-lookup"><span data-stu-id="f7543-298">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Visualizzazione di VS17 della pagina Create.cshtml](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f7543-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f7543-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f7543-301">Per altre informazioni sugli helper tag, ad esempio `<form method="post">`, vedere [Helper tag in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="f7543-301">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f7543-302">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="f7543-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f7543-303">Per visualizzare il tag `<form method="post">`, Visual Studio per Mac usa un tipo di carattere in grassetto distintivo specifico per gli helper tag.</span><span class="sxs-lookup"><span data-stu-id="f7543-303">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="f7543-304">L'elemento `<form method="post">` è un [helper tag del modulo](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="f7543-304">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="f7543-305">L'helper tag del modulo include automaticamente un [token antifalsificazione](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="f7543-305">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="f7543-306">Il motore di ponteggi crea :::no-loc(Razor)::: markup per ogni campo nel modello (ad eccezione dell'ID) simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="f7543-306">The scaffolding engine creates :::no-loc(Razor)::: markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="f7543-307">Gli [Helper tag di convalida](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` e `<span asp-validation-for` ) visualizzano gli errori di convalida.</span><span class="sxs-lookup"><span data-stu-id="f7543-307">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="f7543-308">Il tema della convalida viene trattato in modo più dettagliato successivamente in questa serie.</span><span class="sxs-lookup"><span data-stu-id="f7543-308">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="f7543-309">L' [Helper tag di etichetta](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) genera la didascalia dell'etichetta e l' `for` attributo per la `Title` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="f7543-309">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="f7543-310">L' [Helper tag di input](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) USA gli attributi [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produce gli attributi HTML necessari per la convalida jQuery sul lato client.</span><span class="sxs-lookup"><span data-stu-id="f7543-310">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f7543-311">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="f7543-311">Additional resources</span></span>

* [<span data-ttu-id="f7543-312">Versione YouTube dell'esercitazione</span><span class="sxs-lookup"><span data-stu-id="f7543-312">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="f7543-313">[Precedente: aggiunta di un modello](xref:tutorials/razor-pages/model) 
>  Passaggio [successivo: database](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="f7543-313">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
