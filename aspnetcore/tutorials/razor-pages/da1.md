---
title: 'Parte 5: aggiornare le pagine generate'
author: rick-anderson
description: Parte 5 della serie di esercitazioni sulle Razor pagine.
ms.author: riande
ms.date: 09/20/2020
no-loc:
- Index
- appsettings.json
- ASP.NET Core Identity
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
ms.openlocfilehash: 460950413d1dd2d3539c1d62b0eb11f6bb5144a9
ms.sourcegitcommit: db0a6eb0be7bd7f22810a71fe9bf30e957fd116a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96419967"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="762e8-103">Parte 5: aggiornare le pagine generate in un'app ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="762e8-103">Part 5, update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="762e8-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="762e8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="762e8-105">Le operazioni iniziali con l'app per i film creata con scaffolding sono state efficaci, ma la presentazione non è ottimale.</span><span class="sxs-lookup"><span data-stu-id="762e8-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="762e8-106">Il rilascio **deve essere** costituito da due parole, **Data di rilascio**.</span><span class="sxs-lookup"><span data-stu-id="762e8-106">**ReleaseDate** should be two words, **Release Date**.</span></span>

![App per i film aperta in Chrome](sql/_static/5/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="762e8-108">Aggiornare il codice generato</span><span class="sxs-lookup"><span data-stu-id="762e8-108">Update the generated code</span></span>

<span data-ttu-id="762e8-109">Aprire il file *Models/Movie.cs* e aggiungere le righe evidenziate illustrate nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="762e8-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="762e8-110">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="762e8-110">In the previous code:</span></span>

* <span data-ttu-id="762e8-111">L'annotazione dei dati `[Column(TypeName = "decimal(18, 2)")]` consente a Entity Framework Core di eseguire correttamente il mapping di `Price` nella valuta del database.</span><span class="sxs-lookup"><span data-stu-id="762e8-111">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="762e8-112">Per altre informazioni, vedere [Tipi di dati](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="762e8-112">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>
* <span data-ttu-id="762e8-113">L'attributo [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) specifica il nome visualizzato di un campo.</span><span class="sxs-lookup"><span data-stu-id="762e8-113">The [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) attribute specifies the display name of a field.</span></span> <span data-ttu-id="762e8-114">Nel codice precedente, "release date" invece di "ReleaseDate".</span><span class="sxs-lookup"><span data-stu-id="762e8-114">In the preceding code, "Release Date" instead of "ReleaseDate".</span></span>
* <span data-ttu-id="762e8-115">L'attributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="762e8-115">The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="762e8-116">Le informazioni sull'ora archiviate nel campo non vengono visualizzate.</span><span class="sxs-lookup"><span data-stu-id="762e8-116">The time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="762e8-117">L'attributo [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) viene esaminato nell'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="762e8-117">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span>

<span data-ttu-id="762e8-118">Passare a *pagine/filmati* e passare il puntatore del mouse su un collegamento **modifica** per visualizzare l'URL di destinazione.</span><span class="sxs-lookup"><span data-stu-id="762e8-118">Browse to *Pages/Movies* and hover over an **Edit** link to see the target URL.</span></span>

![Finestra del browser con il passaggio del mouse sul collegamento Edit (Modifica) e un URL di collegamento di https://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="762e8-120">I collegamenti **modifica**, **Dettagli** ed **Elimina** vengono generati dall' [Helper tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) nel file *pages/Movies/ Index . cshtml* .</span><span class="sxs-lookup"><span data-stu-id="762e8-120">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="762e8-121">Gli [Helper Tag](xref:mvc/views/tag-helpers/intro) consentono al codice lato server di partecipare alla creazione e al rendering di elementi HTML nei Razor file.</span><span class="sxs-lookup"><span data-stu-id="762e8-121">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="762e8-122">Nel codice precedente, l' [Helper tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) genera in modo dinamico il `href` valore dell'attributo HTML dalla Razor pagina (la route è relativa), `asp-page` e l'identificatore della route ( `asp-route-id` ).</span><span class="sxs-lookup"><span data-stu-id="762e8-122">In the preceding code, the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`, and the route identifier (`asp-route-id`).</span></span> <span data-ttu-id="762e8-123">Per ulteriori informazioni, vedere [generazione di URL per le pagine](xref:razor-pages/index#url-generation-for-pages).</span><span class="sxs-lookup"><span data-stu-id="762e8-123">For more information, see [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages).</span></span>

<span data-ttu-id="762e8-124">Usare **Visualizza origine** da un browser per esaminare il markup generato.</span><span class="sxs-lookup"><span data-stu-id="762e8-124">Use **View Source** from a browser to examine the generated markup.</span></span> <span data-ttu-id="762e8-125">Di seguito è riportata una parte del codice HTML generato:</span><span class="sxs-lookup"><span data-stu-id="762e8-125">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

   <span data-ttu-id="762e8-126">I collegamenti generati dinamicamente passano l'ID del film con una stringa di query.</span><span class="sxs-lookup"><span data-stu-id="762e8-126">The dynamically generated links pass the movie ID with a query string.</span></span> <span data-ttu-id="762e8-127">Ad esempio, `?id=1` in `https://localhost:5001/Movies/Details?id=1` .</span><span class="sxs-lookup"><span data-stu-id="762e8-127">For example, the `?id=1` in `https://localhost:5001/Movies/Details?id=1`.</span></span>

### <a name="add-route-template"></a><span data-ttu-id="762e8-128">Aggiungere un modello di route</span><span class="sxs-lookup"><span data-stu-id="762e8-128">Add route template</span></span>

<span data-ttu-id="762e8-129">Aggiornare le pagine modifica, dettagli ed Elimina Razor per usare il `{id:int}` modello di route.</span><span class="sxs-lookup"><span data-stu-id="762e8-129">Update the Edit, Details, and Delete Razor Pages to use the `{id:int}` route template.</span></span> <span data-ttu-id="762e8-130">Modificare la direttiva page per ognuna di queste pagine da `@page` a `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="762e8-130">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="762e8-131">Eseguire l'app e quindi visualizzare l'origine.</span><span class="sxs-lookup"><span data-stu-id="762e8-131">Run the app and then view source.</span></span>

<span data-ttu-id="762e8-132">Il codice HTML generato aggiunge l'ID alla parte di percorso dell'URL:</span><span class="sxs-lookup"><span data-stu-id="762e8-132">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="762e8-133">Una richiesta alla pagina con il `{id:int}` modello di route che **non** include il valore integer restituirà un errore HTTP 404 (non trovato).</span><span class="sxs-lookup"><span data-stu-id="762e8-133">A request to the page with the `{id:int}` route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="762e8-134">Ad esempio, `https://localhost:5001/Movies/Details` restituirà un errore 404.</span><span class="sxs-lookup"><span data-stu-id="762e8-134">For example, `https://localhost:5001/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="762e8-135">Per rendere l'ID facoltativo, aggiungere `?` al vincolo di route:</span><span class="sxs-lookup"><span data-stu-id="762e8-135">To make the ID optional, append `?` to the route constraint:</span></span>

```cshtml
@page "{id:int?}"
```

<span data-ttu-id="762e8-136">Testare il comportamento di `@page "{id:int?}"` :</span><span class="sxs-lookup"><span data-stu-id="762e8-136">Test the behavior of `@page "{id:int?}"`:</span></span>

1. <span data-ttu-id="762e8-137">Impostare la direttiva page in *Pages/Movies/Details.cshtml* su `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="762e8-137">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
1. <span data-ttu-id="762e8-138">Impostare un punto di rottura in `public async Task<IActionResult> OnGetAsync(int? id)` , in *pages/Movies/details. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="762e8-138">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)`, in *Pages/Movies/Details.cshtml.cs*.</span></span>
1. <span data-ttu-id="762e8-139">Accedere a `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="762e8-139">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="762e8-140">Con l'istruzione `@page "{id:int}"`, il punto di interruzione non viene mai raggiunto.</span><span class="sxs-lookup"><span data-stu-id="762e8-140">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="762e8-141">Il motore di routing restituisce HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="762e8-141">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="762e8-142">Utilizzando `@page "{id:int?}"` , il `OnGetAsync` metodo restituisce `NotFound` (http 404):</span><span class="sxs-lookup"><span data-stu-id="762e8-142">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404):</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="762e8-143">Verificare la gestione delle eccezioni di concorrenza</span><span class="sxs-lookup"><span data-stu-id="762e8-143">Review concurrency exception handling</span></span>

<span data-ttu-id="762e8-144">Verificare il metodo `OnPostAsync` nel file *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="762e8-144">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="762e8-145">Il codice precedente rileva le eccezioni di concorrenza quando un client elimina il film e l'altro client invia le modifiche al film.</span><span class="sxs-lookup"><span data-stu-id="762e8-145">The previous code detects concurrency exceptions when one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="762e8-146">Per testare il blocco `catch`:</span><span class="sxs-lookup"><span data-stu-id="762e8-146">To test the `catch` block:</span></span>

1. <span data-ttu-id="762e8-147">Impostare un punto di interruzione su `catch (DbUpdateConcurrencyException)` .</span><span class="sxs-lookup"><span data-stu-id="762e8-147">Set a breakpoint on `catch (DbUpdateConcurrencyException)`.</span></span>
1. <span data-ttu-id="762e8-148">Selezionare **Edit** (Modifica) per un film, apportare modifiche, ma non immettere **Save** (Salva).</span><span class="sxs-lookup"><span data-stu-id="762e8-148">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
1. <span data-ttu-id="762e8-149">In un'altra finestra del browser, selezionare il collegamento **Delete** (Elimina) per lo stesso film e quindi eliminare il film.</span><span class="sxs-lookup"><span data-stu-id="762e8-149">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
1. <span data-ttu-id="762e8-150">Nella finestra del browser precedente inviare le modifiche al film.</span><span class="sxs-lookup"><span data-stu-id="762e8-150">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="762e8-151">In alcuni casi, il codice utilizzabile in ambienti di produzione potrebbe voler rilevare i conflitti di concorrenza.</span><span class="sxs-lookup"><span data-stu-id="762e8-151">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="762e8-152">Per altre informazioni, vedere [Gestire i conflitti di concorrenza](xref:data/ef-rp/concurrency).</span><span class="sxs-lookup"><span data-stu-id="762e8-152">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="762e8-153">Invio di post e analisi delle associazioni</span><span class="sxs-lookup"><span data-stu-id="762e8-153">Posting and binding review</span></span>

<span data-ttu-id="762e8-154">Esaminare il file *Pages/Movies/Edit.cshtml.cs*: </span><span class="sxs-lookup"><span data-stu-id="762e8-154">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

<span data-ttu-id="762e8-155">Quando viene effettuata una richiesta HTTP GET alla pagina Movies/Edit, ad esempio `https://localhost:5001/Movies/Edit/3` :</span><span class="sxs-lookup"><span data-stu-id="762e8-155">When an HTTP GET request is made to the Movies/Edit page, for example, `https://localhost:5001/Movies/Edit/3`:</span></span>

* <span data-ttu-id="762e8-156">Il metodo `OnGetAsync` recupera il film dal database e restituisce il metodo `Page`.</span><span class="sxs-lookup"><span data-stu-id="762e8-156">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span>
* <span data-ttu-id="762e8-157">Il `Page` metodo esegue il rendering della pagina *pages/Movies/Edit. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="762e8-157">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="762e8-158">Il file *pages/Movies/Edit. cshtml* contiene la direttiva Model `@model RazorPagesMovie.Pages.Movies.EditModel` che rende il modello di film disponibile nella pagina.</span><span class="sxs-lookup"><span data-stu-id="762e8-158">The *Pages/Movies/Edit.cshtml* file contains the model directive `@model RazorPagesMovie.Pages.Movies.EditModel`, which makes the movie model available on the page.</span></span>
* <span data-ttu-id="762e8-159">Il modulo Edit (Modifica) viene visualizzato con i valori dal film.</span><span class="sxs-lookup"><span data-stu-id="762e8-159">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="762e8-160">Quando viene inviata la pagina Movies/Edit (Film/Modifica):</span><span class="sxs-lookup"><span data-stu-id="762e8-160">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="762e8-161">I valori del modulo nella pagina vengono associati alla proprietà `Movie`.</span><span class="sxs-lookup"><span data-stu-id="762e8-161">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="762e8-162">L'attributo `[BindProperty]` abilita l'[associazione di modelli](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="762e8-162">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="762e8-163">Se si verificano errori nello stato del modello, ad esempio, `ReleaseDate` non può essere convertito in una data, il form viene visualizzato nuovamente con i valori inviati.</span><span class="sxs-lookup"><span data-stu-id="762e8-163">If there are errors in the model state, for example, `ReleaseDate` cannot be converted to a date, the form is redisplayed with the submitted values.</span></span>
* <span data-ttu-id="762e8-164">Se non sono presenti errori del modello, il film viene salvato.</span><span class="sxs-lookup"><span data-stu-id="762e8-164">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="762e8-165">I metodi HTTP GET nelle Index pagine, create ed Delete Razor seguono un modello simile.</span><span class="sxs-lookup"><span data-stu-id="762e8-165">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="762e8-166">Il metodo HTTP POST `OnPostAsync` nella pagina Create Razor segue un modello simile al `OnPostAsync` metodo nella pagina Edit (modifica) Razor .</span><span class="sxs-lookup"><span data-stu-id="762e8-166">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="762e8-167">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="762e8-167">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="762e8-168">[Precedente: utilizzo di un database](xref:tutorials/razor-pages/sql) 
>  Passaggio [successivo: aggiungere la ricerca](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="762e8-168">[Previous: Work with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="762e8-169">Le operazioni iniziali con l'app per i film creata con scaffolding sono state efficaci, ma la presentazione non è ottimale.</span><span class="sxs-lookup"><span data-stu-id="762e8-169">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="762e8-170">Il rilascio **deve essere** costituito da due parole, **Data di rilascio**.</span><span class="sxs-lookup"><span data-stu-id="762e8-170">**ReleaseDate** should be two words, **Release Date**.</span></span>

![App per i film aperta in Chrome](sql/_static/m55https.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="762e8-172">Aggiornare il codice generato</span><span class="sxs-lookup"><span data-stu-id="762e8-172">Update the generated code</span></span>

<span data-ttu-id="762e8-173">Aprire il file *Models/Movie.cs* e aggiungere le righe evidenziate illustrate nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="762e8-173">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="762e8-174">L'annotazione dei dati `[Column(TypeName = "decimal(18, 2)")]` consente a Entity Framework Core di eseguire correttamente il mapping di `Price` nella valuta del database.</span><span class="sxs-lookup"><span data-stu-id="762e8-174">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="762e8-175">Per altre informazioni, vedere [Tipi di dati](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="762e8-175">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="762e8-176">L'attributo [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) viene esaminato nell'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="762e8-176">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="762e8-177">L'attributo [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) specifica gli elementi da visualizzare per il nome di un campo, in questo caso "release date" invece di "ReleaseDate".</span><span class="sxs-lookup"><span data-stu-id="762e8-177">The [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) attribute specifies what to display for the name of a field, in this case "Release Date" instead of "ReleaseDate".</span></span> <span data-ttu-id="762e8-178">L'attributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ), quindi le informazioni sull'ora archiviate nel campo non vengono visualizzate.</span><span class="sxs-lookup"><span data-stu-id="762e8-178">The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="762e8-179">Accedere a Pages/Movies e passare il mouse su un collegamento **Edit** (Modifica) per visualizzare l'URL di destinazione.</span><span class="sxs-lookup"><span data-stu-id="762e8-179">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Finestra del browser con il passaggio del mouse sul collegamento Edit (Modifica) e un URL di collegamento di http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="762e8-181">I collegamenti **modifica**, **Dettagli** ed **Elimina** vengono generati dall' [Helper tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) nel file *pages/Movies/ Index . cshtml* .</span><span class="sxs-lookup"><span data-stu-id="762e8-181">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="762e8-182">Gli [Helper Tag](xref:mvc/views/tag-helpers/intro) consentono al codice lato server di partecipare alla creazione e al rendering di elementi HTML nei Razor file.</span><span class="sxs-lookup"><span data-stu-id="762e8-182">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="762e8-183">Nel codice precedente, genera in `AnchorTagHelper` modo dinamico il valore dell' `href` attributo HTML dalla Razor pagina (la route è relativa), `asp-page` , e l'ID di route ( `asp-route-id` ).</span><span class="sxs-lookup"><span data-stu-id="762e8-183">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`, and the route id (`asp-route-id`).</span></span> <span data-ttu-id="762e8-184">Per altre informazioni, vedere [Generazione di URL per le pagine](xref:razor-pages/index#url-generation-for-pages).</span><span class="sxs-lookup"><span data-stu-id="762e8-184">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="762e8-185">Usare **Visualizza origine** da un browser per esaminare il markup generato.</span><span class="sxs-lookup"><span data-stu-id="762e8-185">Use **View Source** from a browser to examine the generated markup.</span></span> <span data-ttu-id="762e8-186">Di seguito è riportata una parte del codice HTML generato:</span><span class="sxs-lookup"><span data-stu-id="762e8-186">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="762e8-187">I collegamenti generati dinamicamente passano l'ID del film con una stringa di query.</span><span class="sxs-lookup"><span data-stu-id="762e8-187">The dynamically generated links pass the movie ID with a query string.</span></span> <span data-ttu-id="762e8-188">Ad esempio, `?id=1` in  `https://localhost:5001/Movies/Details?id=1` .</span><span class="sxs-lookup"><span data-stu-id="762e8-188">For example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`.</span></span>

<span data-ttu-id="762e8-189">Aggiornare le pagine modifica, dettagli ed Elimina Razor per usare il modello di route "{ID: int}".</span><span class="sxs-lookup"><span data-stu-id="762e8-189">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="762e8-190">Modificare la direttiva page per ognuna di queste pagine da `@page` a `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="762e8-190">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="762e8-191">Eseguire l'app e quindi visualizzare l'origine.</span><span class="sxs-lookup"><span data-stu-id="762e8-191">Run the app and then view source.</span></span> <span data-ttu-id="762e8-192">Il codice HTML generato aggiunge l'ID alla parte di percorso dell'URL:</span><span class="sxs-lookup"><span data-stu-id="762e8-192">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="762e8-193">Una richiesta alla pagina con il modello di route "{id: int}" che **non** include l'intero restituirà un errore HTTP 404 (Non trovato).</span><span class="sxs-lookup"><span data-stu-id="762e8-193">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="762e8-194">Ad esempio, `https://localhost:5001/Movies/Details` restituirà un errore 404.</span><span class="sxs-lookup"><span data-stu-id="762e8-194">For example, `https://localhost:5001/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="762e8-195">Per rendere l'ID facoltativo, aggiungere `?` al vincolo di route:</span><span class="sxs-lookup"><span data-stu-id="762e8-195">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="762e8-196">Per testare il comportamento di `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="762e8-196">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="762e8-197">Impostare la direttiva page in *Pages/Movies/Details.cshtml* su `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="762e8-197">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="762e8-198">Impostare un punto di rottura in `public async Task<IActionResult> OnGetAsync(int? id)` , in *pages/Movies/details. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="762e8-198">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)`, in *Pages/Movies/Details.cshtml.cs*.</span></span>
* <span data-ttu-id="762e8-199">Accedere a `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="762e8-199">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="762e8-200">Con l'istruzione `@page "{id:int}"`, il punto di interruzione non viene mai raggiunto.</span><span class="sxs-lookup"><span data-stu-id="762e8-200">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="762e8-201">Il motore di routing restituisce HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="762e8-201">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="762e8-202">Utilizzando `@page "{id:int?}"` , il `OnGetAsync` metodo restituisce `NotFound` (http 404):</span><span class="sxs-lookup"><span data-stu-id="762e8-202">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404):</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="762e8-203">Verificare la gestione delle eccezioni di concorrenza</span><span class="sxs-lookup"><span data-stu-id="762e8-203">Review concurrency exception handling</span></span>

<span data-ttu-id="762e8-204">Verificare il metodo `OnPostAsync` nel file *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="762e8-204">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="762e8-205">Il codice precedente rileva le eccezioni di concorrenza quando un client elimina il film e l'altro client invia modifiche al film.</span><span class="sxs-lookup"><span data-stu-id="762e8-205">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="762e8-206">Per testare il blocco `catch`:</span><span class="sxs-lookup"><span data-stu-id="762e8-206">To test the `catch` block:</span></span>

* <span data-ttu-id="762e8-207">Impostare un punto di interruzione su `catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="762e8-207">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="762e8-208">Selezionare **Edit** (Modifica) per un film, apportare modifiche, ma non immettere **Save** (Salva).</span><span class="sxs-lookup"><span data-stu-id="762e8-208">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="762e8-209">In un'altra finestra del browser, selezionare il collegamento **Delete** (Elimina) per lo stesso film e quindi eliminare il film.</span><span class="sxs-lookup"><span data-stu-id="762e8-209">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="762e8-210">Nella finestra del browser precedente inviare le modifiche al film.</span><span class="sxs-lookup"><span data-stu-id="762e8-210">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="762e8-211">In alcuni casi, il codice utilizzabile in ambienti di produzione potrebbe voler rilevare i conflitti di concorrenza.</span><span class="sxs-lookup"><span data-stu-id="762e8-211">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="762e8-212">Per altre informazioni, vedere [Gestire i conflitti di concorrenza](xref:data/ef-rp/concurrency).</span><span class="sxs-lookup"><span data-stu-id="762e8-212">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="762e8-213">Invio di post e analisi delle associazioni</span><span class="sxs-lookup"><span data-stu-id="762e8-213">Posting and binding review</span></span>

<span data-ttu-id="762e8-214">Esaminare il file *Pages/Movies/Edit.cshtml.cs*: </span><span class="sxs-lookup"><span data-stu-id="762e8-214">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="762e8-215">Quando viene effettuata una richiesta HTTP GET alla pagina Movies/Edit, ad esempio `https://localhost:5001/Movies/Edit/3` :</span><span class="sxs-lookup"><span data-stu-id="762e8-215">When an HTTP GET request is made to the Movies/Edit page, for example, `https://localhost:5001/Movies/Edit/3`:</span></span>

* <span data-ttu-id="762e8-216">Il metodo `OnGetAsync` recupera il film dal database e restituisce il metodo `Page`.</span><span class="sxs-lookup"><span data-stu-id="762e8-216">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="762e8-217">Il `Page` metodo esegue il rendering della pagina *pages/Movies/Edit. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="762e8-217">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="762e8-218">Il file *pages/Movies/Edit. cshtml* contiene la direttiva Model `@model RazorPagesMovie.Pages.Movies.EditModel` che rende il modello di film disponibile nella pagina.</span><span class="sxs-lookup"><span data-stu-id="762e8-218">The *Pages/Movies/Edit.cshtml* file contains the model directive `@model RazorPagesMovie.Pages.Movies.EditModel`, which makes the movie model available on the page.</span></span>
* <span data-ttu-id="762e8-219">Il modulo Edit (Modifica) viene visualizzato con i valori dal film.</span><span class="sxs-lookup"><span data-stu-id="762e8-219">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="762e8-220">Quando viene inviata la pagina Movies/Edit (Film/Modifica):</span><span class="sxs-lookup"><span data-stu-id="762e8-220">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="762e8-221">I valori del modulo nella pagina vengono associati alla proprietà `Movie`.</span><span class="sxs-lookup"><span data-stu-id="762e8-221">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="762e8-222">L'attributo `[BindProperty]` abilita l'[associazione di modelli](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="762e8-222">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="762e8-223">Se si verificano errori nello stato del modello, ad esempio, `ReleaseDate` non può essere convertito in una data, il form viene visualizzato con i valori inviati.</span><span class="sxs-lookup"><span data-stu-id="762e8-223">If there are errors in the model state, for example, `ReleaseDate` cannot be converted to a date, the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="762e8-224">Se non sono presenti errori del modello, il film viene salvato.</span><span class="sxs-lookup"><span data-stu-id="762e8-224">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="762e8-225">I metodi HTTP GET nelle Index pagine, create ed Delete Razor seguono un modello simile.</span><span class="sxs-lookup"><span data-stu-id="762e8-225">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="762e8-226">Il metodo HTTP POST `OnPostAsync` nella pagina Create Razor segue un modello simile al `OnPostAsync` metodo nella pagina Edit (modifica) Razor .</span><span class="sxs-lookup"><span data-stu-id="762e8-226">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="762e8-227">La funzionalità di ricerca viene aggiunta nell'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="762e8-227">Search is added in the next tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="762e8-228">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="762e8-228">Additional resources</span></span>

* [<span data-ttu-id="762e8-229">Versione YouTube dell'esercitazione</span><span class="sxs-lookup"><span data-stu-id="762e8-229">YouTube version of this tutorial</span></span>](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> <span data-ttu-id="762e8-230">[Precedente: utilizzo di un database](xref:tutorials/razor-pages/sql) 
>  Passaggio [successivo: aggiungere la ricerca](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="762e8-230">[Previous: Work with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end
