---
title: "Parte 5: aggiornare le pagine generate in un'app ASP.NET Core"
author: rick-anderson
description: Parte 5 della serie di esercitazioni sulle Razor pagine.
ms.author: riande
ms.date: 12/20/2018
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
uid: tutorials/razor-pages/da1
ms.openlocfilehash: d8f1a256bfa3491ff1cc7f38a23c61beb719bd49
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022095"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="79a72-103">Parte 5: aggiornare le pagine generate in un'app ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="79a72-103">Part 5, update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="79a72-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="79a72-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="79a72-105">Le operazioni iniziali con l'app per i film creata con scaffolding sono state efficaci, ma la presentazione non è ottimale.</span><span class="sxs-lookup"><span data-stu-id="79a72-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="79a72-106">**ReleaseDate** deve essere **Release Date** (due parole).</span><span class="sxs-lookup"><span data-stu-id="79a72-106">**ReleaseDate** should be **Release Date** (two words).</span></span>

![App per i film aperta in Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="79a72-108">Aggiornare il codice generato</span><span class="sxs-lookup"><span data-stu-id="79a72-108">Update the generated code</span></span>

<span data-ttu-id="79a72-109">Aprire il file *Models/Movie.cs* e aggiungere le righe evidenziate illustrate nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="79a72-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="79a72-110">L'annotazione dei dati `[Column(TypeName = "decimal(18, 2)")]` consente a Entity Framework Core di eseguire correttamente il mapping di `Price` nella valuta del database.</span><span class="sxs-lookup"><span data-stu-id="79a72-110">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="79a72-111">Per altre informazioni, vedere [Tipi di dati](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="79a72-111">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="79a72-112">L'attributo [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) viene esaminato nell'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="79a72-112">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="79a72-113">L'attributo [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) specifica il testo da visualizzare per il nome di un campo, in questo caso "Release Date" anziché "ReleaseDate".</span><span class="sxs-lookup"><span data-stu-id="79a72-113">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="79a72-114">L'attributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) specifica il tipo di dati (Date) e quindi non vengono visualizzate le informazioni sull'ora archiviate nel campo.</span><span class="sxs-lookup"><span data-stu-id="79a72-114">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="79a72-115">Accedere a Pages/Movies e passare il mouse su un collegamento **Edit** (Modifica) per visualizzare l'URL di destinazione.</span><span class="sxs-lookup"><span data-stu-id="79a72-115">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Finestra del browser con il passaggio del mouse sul collegamento Edit (Modifica) e un URL di collegamento di http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="79a72-117">I collegamenti **Edit** (Modifica), **Details** (Dettagli) e **Delete** (Elimina) vengono generati dall'[helper tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) nel file *Pages/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79a72-117">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="79a72-118">Gli [Helper Tag](xref:mvc/views/tag-helpers/intro) consentono al codice lato server di partecipare alla creazione e al rendering di elementi HTML nei Razor file.</span><span class="sxs-lookup"><span data-stu-id="79a72-118">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="79a72-119">Nel codice precedente, genera in `AnchorTagHelper` modo dinamico il valore dell' `href` attributo HTML dalla Razor pagina (la route è relativa), `asp-page` , e l'ID di route ( `asp-route-id` ).</span><span class="sxs-lookup"><span data-stu-id="79a72-119">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="79a72-120">Per altre informazioni, vedere [Generazione di URL per le pagine](xref:razor-pages/index#url-generation-for-pages).</span><span class="sxs-lookup"><span data-stu-id="79a72-120">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="79a72-121">Usare **Visualizza origine** dal browser preferito per esaminare il codice generato.</span><span class="sxs-lookup"><span data-stu-id="79a72-121">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="79a72-122">Di seguito è riportata una parte del codice HTML generato:</span><span class="sxs-lookup"><span data-stu-id="79a72-122">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="79a72-123">I collegamenti generati dinamicamente passano l'ID del film con una stringa di query, ad esempio `?id=1` in `https://localhost:5001/Movies/Details?id=1`.</span><span class="sxs-lookup"><span data-stu-id="79a72-123">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

### <a name="add-route-template"></a><span data-ttu-id="79a72-124">Aggiungere un modello di route</span><span class="sxs-lookup"><span data-stu-id="79a72-124">Add route template</span></span>

<span data-ttu-id="79a72-125">Aggiornare le pagine modifica, dettagli ed Elimina Razor per usare il modello di route "{ID: int}".</span><span class="sxs-lookup"><span data-stu-id="79a72-125">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="79a72-126">Modificare la direttiva page per ognuna di queste pagine da `@page` a `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="79a72-126">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="79a72-127">Eseguire l'app e quindi visualizzare l'origine.</span><span class="sxs-lookup"><span data-stu-id="79a72-127">Run the app and then view source.</span></span> <span data-ttu-id="79a72-128">Il codice HTML generato aggiunge l'ID alla parte di percorso dell'URL:</span><span class="sxs-lookup"><span data-stu-id="79a72-128">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="79a72-129">Una richiesta alla pagina con il modello di route "{id: int}" che **non** include l'intero restituirà un errore HTTP 404 (Non trovato).</span><span class="sxs-lookup"><span data-stu-id="79a72-129">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="79a72-130">Ad esempio, `http://localhost:5000/Movies/Details` restituirà un errore 404.</span><span class="sxs-lookup"><span data-stu-id="79a72-130">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="79a72-131">Per rendere l'ID facoltativo, aggiungere `?` al vincolo di route:</span><span class="sxs-lookup"><span data-stu-id="79a72-131">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="79a72-132">Per testare il comportamento di `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="79a72-132">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="79a72-133">Impostare la direttiva page in *Pages/Movies/Details.cshtml* su `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="79a72-133">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="79a72-134">Impostare un punto di interruzione in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="79a72-134">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="79a72-135">Accedere a `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="79a72-135">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="79a72-136">Con l'istruzione `@page "{id:int}"`, il punto di interruzione non viene mai raggiunto.</span><span class="sxs-lookup"><span data-stu-id="79a72-136">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="79a72-137">Il motore di routing restituisce HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="79a72-137">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="79a72-138">Se si utilizza `@page "{id:int?}"`, il metodo `OnGetAsync` restituisce `NotFound` (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="79a72-138">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="79a72-139">Verificare la gestione delle eccezioni di concorrenza</span><span class="sxs-lookup"><span data-stu-id="79a72-139">Review concurrency exception handling</span></span>

<span data-ttu-id="79a72-140">Verificare il metodo `OnPostAsync` nel file *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="79a72-140">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="79a72-141">Il codice precedente rileva le eccezioni di concorrenza quando un client elimina il film e l'altro client invia modifiche al film.</span><span class="sxs-lookup"><span data-stu-id="79a72-141">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="79a72-142">Per testare il blocco `catch`:</span><span class="sxs-lookup"><span data-stu-id="79a72-142">To test the `catch` block:</span></span>

* <span data-ttu-id="79a72-143">Impostare un punto di interruzione su `catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="79a72-143">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="79a72-144">Selezionare **Edit** (Modifica) per un film, apportare modifiche, ma non immettere **Save** (Salva).</span><span class="sxs-lookup"><span data-stu-id="79a72-144">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="79a72-145">In un'altra finestra del browser, selezionare il collegamento **Delete** (Elimina) per lo stesso film e quindi eliminare il film.</span><span class="sxs-lookup"><span data-stu-id="79a72-145">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="79a72-146">Nella finestra del browser precedente inviare le modifiche al film.</span><span class="sxs-lookup"><span data-stu-id="79a72-146">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="79a72-147">In alcuni casi, il codice utilizzabile in ambienti di produzione potrebbe voler rilevare i conflitti di concorrenza.</span><span class="sxs-lookup"><span data-stu-id="79a72-147">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="79a72-148">Per altre informazioni, vedere [Gestire i conflitti di concorrenza](xref:data/ef-rp/concurrency).</span><span class="sxs-lookup"><span data-stu-id="79a72-148">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="79a72-149">Invio di post e analisi delle associazioni</span><span class="sxs-lookup"><span data-stu-id="79a72-149">Posting and binding review</span></span>

<span data-ttu-id="79a72-150">Esaminare il file *Pages/Movies/Edit.cshtml.cs*: </span><span class="sxs-lookup"><span data-stu-id="79a72-150">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

<span data-ttu-id="79a72-151">Quando viene eseguita una richiesta HTTP GET alla pagina Movies/Edit (Film/Modifica), ad esempio `http://localhost:5000/Movies/Edit/2`:</span><span class="sxs-lookup"><span data-stu-id="79a72-151">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="79a72-152">Il metodo `OnGetAsync` recupera il film dal database e restituisce il metodo `Page`.</span><span class="sxs-lookup"><span data-stu-id="79a72-152">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span>
* <span data-ttu-id="79a72-153">Il `Page` metodo esegue il rendering della pagina *pages/Movies/Edit. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="79a72-153">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="79a72-154">Il file *Pages/Movies/Edit.cshtml* contiene la direttiva modello (`@model RazorPagesMovie.Pages.Movies.EditModel`) che rende il modello di film disponibile nella pagina.</span><span class="sxs-lookup"><span data-stu-id="79a72-154">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="79a72-155">Il modulo Edit (Modifica) viene visualizzato con i valori dal film.</span><span class="sxs-lookup"><span data-stu-id="79a72-155">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="79a72-156">Quando viene inviata la pagina Movies/Edit (Film/Modifica):</span><span class="sxs-lookup"><span data-stu-id="79a72-156">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="79a72-157">I valori del modulo nella pagina vengono associati alla proprietà `Movie`.</span><span class="sxs-lookup"><span data-stu-id="79a72-157">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="79a72-158">L'attributo `[BindProperty]` abilita l'[associazione di modelli](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="79a72-158">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="79a72-159">Se sono presenti errori nello stato del modello, ad esempio non è possibile convertire `ReleaseDate` in una data, il modulo viene rivisualizzato con i valori inviati.</span><span class="sxs-lookup"><span data-stu-id="79a72-159">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is redisplayed with the submitted values.</span></span>
* <span data-ttu-id="79a72-160">Se non sono presenti errori del modello, il film viene salvato.</span><span class="sxs-lookup"><span data-stu-id="79a72-160">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="79a72-161">I metodi HTTP GET nelle pagine di indice, creazione ed eliminazione Razor seguono un modello simile.</span><span class="sxs-lookup"><span data-stu-id="79a72-161">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="79a72-162">Il metodo HTTP POST `OnPostAsync` nella pagina Create Razor segue un modello simile al `OnPostAsync` metodo nella pagina Edit (modifica) Razor .</span><span class="sxs-lookup"><span data-stu-id="79a72-162">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="79a72-163">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="79a72-163">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="79a72-164">[Precedente: utilizzo di un database](xref:tutorials/razor-pages/sql) 
>  Passaggio [successivo: aggiungere la ricerca](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="79a72-164">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="79a72-165">Le operazioni iniziali con l'app per i film creata con scaffolding sono state efficaci, ma la presentazione non è ottimale.</span><span class="sxs-lookup"><span data-stu-id="79a72-165">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="79a72-166">**ReleaseDate** deve essere **Release Date** (due parole).</span><span class="sxs-lookup"><span data-stu-id="79a72-166">**ReleaseDate** should be **Release Date** (two words).</span></span>

![App per i film aperta in Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="79a72-168">Aggiornare il codice generato</span><span class="sxs-lookup"><span data-stu-id="79a72-168">Update the generated code</span></span>

<span data-ttu-id="79a72-169">Aprire il file *Models/Movie.cs* e aggiungere le righe evidenziate illustrate nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="79a72-169">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="79a72-170">L'annotazione dei dati `[Column(TypeName = "decimal(18, 2)")]` consente a Entity Framework Core di eseguire correttamente il mapping di `Price` nella valuta del database.</span><span class="sxs-lookup"><span data-stu-id="79a72-170">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="79a72-171">Per altre informazioni, vedere [Tipi di dati](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="79a72-171">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="79a72-172">L'attributo [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) viene esaminato nell'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="79a72-172">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="79a72-173">L'attributo [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) specifica il testo da visualizzare per il nome di un campo, in questo caso "Release Date" anziché "ReleaseDate".</span><span class="sxs-lookup"><span data-stu-id="79a72-173">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="79a72-174">L'attributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) specifica il tipo di dati (Date) e quindi non vengono visualizzate le informazioni sull'ora archiviate nel campo.</span><span class="sxs-lookup"><span data-stu-id="79a72-174">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="79a72-175">Accedere a Pages/Movies e passare il mouse su un collegamento **Edit** (Modifica) per visualizzare l'URL di destinazione.</span><span class="sxs-lookup"><span data-stu-id="79a72-175">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Finestra del browser con il passaggio del mouse sul collegamento Edit (Modifica) e un URL di collegamento di http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="79a72-177">I collegamenti **Edit** (Modifica), **Details** (Dettagli) e **Delete** (Elimina) vengono generati dall'[helper tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) nel file *Pages/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79a72-177">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="79a72-178">Gli [Helper Tag](xref:mvc/views/tag-helpers/intro) consentono al codice lato server di partecipare alla creazione e al rendering di elementi HTML nei Razor file.</span><span class="sxs-lookup"><span data-stu-id="79a72-178">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="79a72-179">Nel codice precedente, genera in `AnchorTagHelper` modo dinamico il valore dell' `href` attributo HTML dalla Razor pagina (la route è relativa), `asp-page` , e l'ID di route ( `asp-route-id` ).</span><span class="sxs-lookup"><span data-stu-id="79a72-179">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="79a72-180">Per altre informazioni, vedere [Generazione di URL per le pagine](xref:razor-pages/index#url-generation-for-pages).</span><span class="sxs-lookup"><span data-stu-id="79a72-180">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="79a72-181">Usare **Visualizza origine** dal browser preferito per esaminare il codice generato.</span><span class="sxs-lookup"><span data-stu-id="79a72-181">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="79a72-182">Di seguito è riportata una parte del codice HTML generato:</span><span class="sxs-lookup"><span data-stu-id="79a72-182">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="79a72-183">I collegamenti generati dinamicamente passano l'ID del film con una stringa di query, ad esempio `?id=1` in `https://localhost:5001/Movies/Details?id=1`.</span><span class="sxs-lookup"><span data-stu-id="79a72-183">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

<span data-ttu-id="79a72-184">Aggiornare le pagine modifica, dettagli ed Elimina Razor per usare il modello di route "{ID: int}".</span><span class="sxs-lookup"><span data-stu-id="79a72-184">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="79a72-185">Modificare la direttiva page per ognuna di queste pagine da `@page` a `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="79a72-185">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="79a72-186">Eseguire l'app e quindi visualizzare l'origine.</span><span class="sxs-lookup"><span data-stu-id="79a72-186">Run the app and then view source.</span></span> <span data-ttu-id="79a72-187">Il codice HTML generato aggiunge l'ID alla parte di percorso dell'URL:</span><span class="sxs-lookup"><span data-stu-id="79a72-187">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="79a72-188">Una richiesta alla pagina con il modello di route "{id: int}" che **non** include l'intero restituirà un errore HTTP 404 (Non trovato).</span><span class="sxs-lookup"><span data-stu-id="79a72-188">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="79a72-189">Ad esempio, `http://localhost:5000/Movies/Details` restituirà un errore 404.</span><span class="sxs-lookup"><span data-stu-id="79a72-189">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="79a72-190">Per rendere l'ID facoltativo, aggiungere `?` al vincolo di route:</span><span class="sxs-lookup"><span data-stu-id="79a72-190">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="79a72-191">Per testare il comportamento di `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="79a72-191">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="79a72-192">Impostare la direttiva page in *Pages/Movies/Details.cshtml* su `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="79a72-192">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="79a72-193">Impostare un punto di interruzione in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="79a72-193">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="79a72-194">Accedere a `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="79a72-194">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="79a72-195">Con l'istruzione `@page "{id:int}"`, il punto di interruzione non viene mai raggiunto.</span><span class="sxs-lookup"><span data-stu-id="79a72-195">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="79a72-196">Il motore di routing restituisce HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="79a72-196">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="79a72-197">Se si utilizza `@page "{id:int?}"`, il metodo `OnGetAsync` restituisce `NotFound` (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="79a72-197">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="79a72-198">Verificare la gestione delle eccezioni di concorrenza</span><span class="sxs-lookup"><span data-stu-id="79a72-198">Review concurrency exception handling</span></span>

<span data-ttu-id="79a72-199">Verificare il metodo `OnPostAsync` nel file *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="79a72-199">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="79a72-200">Il codice precedente rileva le eccezioni di concorrenza quando un client elimina il film e l'altro client invia modifiche al film.</span><span class="sxs-lookup"><span data-stu-id="79a72-200">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="79a72-201">Per testare il blocco `catch`:</span><span class="sxs-lookup"><span data-stu-id="79a72-201">To test the `catch` block:</span></span>

* <span data-ttu-id="79a72-202">Impostare un punto di interruzione su `catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="79a72-202">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="79a72-203">Selezionare **Edit** (Modifica) per un film, apportare modifiche, ma non immettere **Save** (Salva).</span><span class="sxs-lookup"><span data-stu-id="79a72-203">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="79a72-204">In un'altra finestra del browser, selezionare il collegamento **Delete** (Elimina) per lo stesso film e quindi eliminare il film.</span><span class="sxs-lookup"><span data-stu-id="79a72-204">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="79a72-205">Nella finestra del browser precedente inviare le modifiche al film.</span><span class="sxs-lookup"><span data-stu-id="79a72-205">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="79a72-206">In alcuni casi, il codice utilizzabile in ambienti di produzione potrebbe voler rilevare i conflitti di concorrenza.</span><span class="sxs-lookup"><span data-stu-id="79a72-206">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="79a72-207">Per altre informazioni, vedere [Gestire i conflitti di concorrenza](xref:data/ef-rp/concurrency).</span><span class="sxs-lookup"><span data-stu-id="79a72-207">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="79a72-208">Invio di post e analisi delle associazioni</span><span class="sxs-lookup"><span data-stu-id="79a72-208">Posting and binding review</span></span>

<span data-ttu-id="79a72-209">Esaminare il file *Pages/Movies/Edit.cshtml.cs*: </span><span class="sxs-lookup"><span data-stu-id="79a72-209">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="79a72-210">Quando viene eseguita una richiesta HTTP GET alla pagina Movies/Edit (Film/Modifica), ad esempio `http://localhost:5000/Movies/Edit/2`:</span><span class="sxs-lookup"><span data-stu-id="79a72-210">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="79a72-211">Il metodo `OnGetAsync` recupera il film dal database e restituisce il metodo `Page`.</span><span class="sxs-lookup"><span data-stu-id="79a72-211">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="79a72-212">Il `Page` metodo esegue il rendering della pagina *pages/Movies/Edit. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="79a72-212">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="79a72-213">Il file *Pages/Movies/Edit.cshtml* contiene la direttiva modello (`@model RazorPagesMovie.Pages.Movies.EditModel`) che rende il modello di film disponibile nella pagina.</span><span class="sxs-lookup"><span data-stu-id="79a72-213">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="79a72-214">Il modulo Edit (Modifica) viene visualizzato con i valori dal film.</span><span class="sxs-lookup"><span data-stu-id="79a72-214">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="79a72-215">Quando viene inviata la pagina Movies/Edit (Film/Modifica):</span><span class="sxs-lookup"><span data-stu-id="79a72-215">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="79a72-216">I valori del modulo nella pagina vengono associati alla proprietà `Movie`.</span><span class="sxs-lookup"><span data-stu-id="79a72-216">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="79a72-217">L'attributo `[BindProperty]` abilita l'[associazione di modelli](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="79a72-217">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="79a72-218">Se sono presenti errori nello stato del modello, ad esempio non è possibile convertire `ReleaseDate` in una data, il modulo viene visualizzato con i valori inviati.</span><span class="sxs-lookup"><span data-stu-id="79a72-218">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="79a72-219">Se non sono presenti errori del modello, il film viene salvato.</span><span class="sxs-lookup"><span data-stu-id="79a72-219">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="79a72-220">I metodi HTTP GET nelle pagine di indice, creazione ed eliminazione Razor seguono un modello simile.</span><span class="sxs-lookup"><span data-stu-id="79a72-220">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="79a72-221">Il metodo HTTP POST `OnPostAsync` nella pagina Create Razor segue un modello simile al `OnPostAsync` metodo nella pagina Edit (modifica) Razor .</span><span class="sxs-lookup"><span data-stu-id="79a72-221">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="79a72-222">La funzionalità di ricerca viene aggiunta nell'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="79a72-222">Search is added in the next tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="79a72-223">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="79a72-223">Additional resources</span></span>

* [<span data-ttu-id="79a72-224">Versione YouTube dell'esercitazione</span><span class="sxs-lookup"><span data-stu-id="79a72-224">YouTube version of this tutorial</span></span>](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> <span data-ttu-id="79a72-225">[Precedente: utilizzo di un database](xref:tutorials/razor-pages/sql) 
>  Passaggio [successivo: aggiungere la ricerca](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="79a72-225">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end
