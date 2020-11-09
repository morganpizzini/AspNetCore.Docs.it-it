---
title: Parte 6, metodi e viste del controller in ASP.NET Core
author: rick-anderson
description: "Parte 6: aggiungere un modello a un'app MVC ASP.NET Core"
ms.author: riande
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/controller-methods-views
ms.openlocfilehash: b4850821317b6907452793ef09194844c90c0137
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050771"
---
# <a name="part-6-controller-methods-and-views-in-aspnet-core"></a><span data-ttu-id="35d3c-103">Parte 6, metodi e viste del controller in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="35d3c-103">Part 6, controller methods and views in ASP.NET Core</span></span>

<span data-ttu-id="35d3c-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="35d3c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="35d3c-105">Le operazioni iniziali con l'app per i film sono state efficaci, ma la presentazione non è ottimale, ad esempio **ReleaseDate** dovrebbe essere scritto come due parole.</span><span class="sxs-lookup"><span data-stu-id="35d3c-105">We have a good start to the movie app, but the presentation isn't ideal, for example, **ReleaseDate** should be two words.</span></span>

![Vista Index: Release Date è un'unica parola (senza spazi) e la data di rilascio di ogni film indica l'ora nel formato 12 AM](working-with-sql/_static/m55.png)

<span data-ttu-id="35d3c-107">Aprire il file *Models/Movie.cs* e aggiungere le righe evidenziate illustrate di seguito:</span><span class="sxs-lookup"><span data-stu-id="35d3c-107">Open the *Models/Movie.cs* file and add the highlighted lines shown below:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=2,3,12-13,17)]

<span data-ttu-id="35d3c-108">L'attributo [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) viene esaminato nell'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="35d3c-108">We cover [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) in the next tutorial.</span></span> <span data-ttu-id="35d3c-109">L'attributo [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) specifica il testo da visualizzare per il nome di un campo, in questo caso "Release Date" anziché "ReleaseDate".</span><span class="sxs-lookup"><span data-stu-id="35d3c-109">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="35d3c-110">L'attributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) specifica il tipo di dati (Date) e quindi non vengono visualizzate le informazioni sull'ora archiviate nel campo.</span><span class="sxs-lookup"><span data-stu-id="35d3c-110">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="35d3c-111">L'annotazione dei dati `[Column(TypeName = "decimal(18, 2)")]` è necessaria per consentire a Entity Framework Core di eseguire correttamente il mapping di `Price` nella valuta del database.</span><span class="sxs-lookup"><span data-stu-id="35d3c-111">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="35d3c-112">Per altre informazioni, vedere [Tipi di dati](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="35d3c-112">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="35d3c-113">Passare al controller `Movies` e posizionare il puntatore del mouse su un collegamento **Edit** (Modifica) per visualizzare l'URL di destinazione.</span><span class="sxs-lookup"><span data-stu-id="35d3c-113">Browse to the `Movies` controller and hold the mouse pointer over an **Edit** link to see the target URL.</span></span>

![Finestra del browser con il passaggio del mouse sul collegamento Edit (Modifica) e un URL di collegamento di https://localhost:5001/Movies/Edit/5](~/tutorials/first-mvc-app/controller-methods-views/_static/edit7.png)

<span data-ttu-id="35d3c-115">I collegamenti **Edit** (Modifica), **Details** (Dettagli) e **Delete** (Elimina) vengono generati dall'helper per i tag di ancoraggio di Core MVC nel file *Views/Movies/Index.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="35d3c-115">The **Edit** , **Details** , and **Delete** links are generated by the Core MVC Anchor Tag Helper in the *Views/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1-3&range=46-50)]

<span data-ttu-id="35d3c-116">Gli [Helper Tag](xref:mvc/views/tag-helpers/intro) consentono al codice lato server di partecipare alla creazione e al rendering di elementi HTML nei Razor file.</span><span class="sxs-lookup"><span data-stu-id="35d3c-116">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="35d3c-117">Nel codice precedente, genera in `AnchorTagHelper` modo dinamico il valore dell' `href` attributo HTML dal metodo di azione del controller e dall'ID della route. Usare **Visualizza origine** dal browser preferito oppure usare gli strumenti di sviluppo per esaminare il markup generato.</span><span class="sxs-lookup"><span data-stu-id="35d3c-117">In the code above, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the controller action method and route id. You use **View Source** from your favorite browser or use the developer tools to examine the generated markup.</span></span> <span data-ttu-id="35d3c-118">Di seguito è riportata una parte del codice HTML generato:</span><span class="sxs-lookup"><span data-stu-id="35d3c-118">A portion of the generated HTML is shown below:</span></span>

```html
 <td>
    <a href="/Movies/Edit/4"> Edit </a> |
    <a href="/Movies/Details/4"> Details </a> |
    <a href="/Movies/Delete/4"> Delete </a>
</td>
```

<span data-ttu-id="35d3c-119">Tenere presente il formato per il [routing](xref:mvc/controllers/routing) impostato nel file *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="35d3c-119">Recall the format for [routing](xref:mvc/controllers/routing) set in the *Startup.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="35d3c-120">ASP.NET Core converte `https://localhost:5001/Movies/Edit/4` in una richiesta al metodo di azione `Edit` del controller `Movies` con il parametro `Id` impostato su 4.</span><span class="sxs-lookup"><span data-stu-id="35d3c-120">ASP.NET Core translates `https://localhost:5001/Movies/Edit/4` into a request to the `Edit` action method of the `Movies` controller with the parameter `Id` of 4.</span></span> <span data-ttu-id="35d3c-121">I metodi del controller sono noti anche come metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="35d3c-121">(Controller methods are also known as action methods.)</span></span>

<span data-ttu-id="35d3c-122">Una delle nuove funzionalità più note di ASP.NET Core è rappresentata dagli [helper tag](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="35d3c-122">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are one of the most popular new features in ASP.NET Core.</span></span> <span data-ttu-id="35d3c-123">Per altre informazioni, vedere [Risorse aggiuntive](#additional-resources).</span><span class="sxs-lookup"><span data-stu-id="35d3c-123">For more information, see [Additional resources](#additional-resources).</span></span>

<a name="get-post"></a>

<span data-ttu-id="35d3c-124">Aprire il controller `Movies` ed esaminare i due metodi di azione `Edit`.</span><span class="sxs-lookup"><span data-stu-id="35d3c-124">Open the `Movies` controller and examine the two `Edit` action methods.</span></span> <span data-ttu-id="35d3c-125">Il codice seguente illustra il `HTTP GET Edit` metodo, che recupera il film e popola il modulo di modifica generato dal file *Edit. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="35d3c-125">The following code shows the `HTTP GET Edit` method, which fetches the movie and populates the edit form generated by the *Edit.cshtml* Razor file.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

<span data-ttu-id="35d3c-126">Il codice seguente illustra il metodo `HTTP POST Edit`, che elabora i valori di film inviati:</span><span class="sxs-lookup"><span data-stu-id="35d3c-126">The following code shows the `HTTP POST Edit` method, which processes the posted movie values:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit1)]

<span data-ttu-id="35d3c-127">Il codice seguente illustra il metodo `HTTP POST Edit`, che elabora i valori di film inviati:</span><span class="sxs-lookup"><span data-stu-id="35d3c-127">The following code shows the `HTTP POST Edit` method, which processes the posted movie values:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

<span data-ttu-id="35d3c-128">L'attributo `[Bind]` è un modo per proteggersi dall'[overposting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost).</span><span class="sxs-lookup"><span data-stu-id="35d3c-128">The `[Bind]` attribute is one way to protect against [over-posting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost).</span></span> <span data-ttu-id="35d3c-129">È necessario includere solo le proprietà nell'attributo `[Bind]` che si vuole modificare.</span><span class="sxs-lookup"><span data-stu-id="35d3c-129">You should only include properties in the `[Bind]` attribute that you want to change.</span></span> <span data-ttu-id="35d3c-130">Per altre informazioni, vedere [Proteggere il controller dall'overposting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application).</span><span class="sxs-lookup"><span data-stu-id="35d3c-130">For more information, see [Protect your controller from over-posting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application).</span></span> <span data-ttu-id="35d3c-131">[ViewModel](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) offre un approccio alternativo per evitare l'overposting.</span><span class="sxs-lookup"><span data-stu-id="35d3c-131">[ViewModels](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) provide an alternative approach to prevent over-posting.</span></span>

<span data-ttu-id="35d3c-132">Si noti che il secondo metodo di azione `Edit` è preceduto dall'attributo `[HttpPost]`.</span><span class="sxs-lookup"><span data-stu-id="35d3c-132">Notice the second `Edit` action method is preceded by the `[HttpPost]` attribute.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2&highlight=1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2&highlight=4)]

::: moniker-end

<span data-ttu-id="35d3c-133">L'attributo `HttpPost` specifica che questo metodo `Edit` può essere richiamato *solo* per le richieste `POST`.</span><span class="sxs-lookup"><span data-stu-id="35d3c-133">The `HttpPost` attribute specifies that this `Edit` method can be invoked *only* for `POST` requests.</span></span> <span data-ttu-id="35d3c-134">È possibile applicare l'attributo `[HttpGet]` al primo metodo di modifica, ma non è necessario perché l'impostazione predefinita è `[HttpGet]`.</span><span class="sxs-lookup"><span data-stu-id="35d3c-134">You could apply the `[HttpGet]` attribute to the first edit method, but that's not necessary because `[HttpGet]` is the default.</span></span>

<span data-ttu-id="35d3c-135">L'attributo `ValidateAntiForgeryToken` viene usato per [impedire la falsificazione di una richiesta](xref:security/anti-request-forgery) ed è accoppiato a un token antifalsificazione generato nel file di vista di modifica ( *Views/Movies/Edit.cshtml* ).</span><span class="sxs-lookup"><span data-stu-id="35d3c-135">The `ValidateAntiForgeryToken` attribute is used to [prevent forgery of a request](xref:security/anti-request-forgery) and is paired up with an anti-forgery token generated in the edit view file ( *Views/Movies/Edit.cshtml* ).</span></span> <span data-ttu-id="35d3c-136">Il file di vista di modifica genera il token antifalsificazione con l'[helper tag di modulo](xref:mvc/views/working-with-forms).</span><span class="sxs-lookup"><span data-stu-id="35d3c-136">The edit view file generates the anti-forgery token with the [Form Tag Helper](xref:mvc/views/working-with-forms).</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/Edit.cshtml?range=9)]

<span data-ttu-id="35d3c-137">L'[helper tag](xref:mvc/views/working-with-forms) genera un token antifalsificazione nascosto che deve corrispondere al token antifalsificazione generato `[ValidateAntiForgeryToken]` nel metodo `Edit` del controller di film.</span><span class="sxs-lookup"><span data-stu-id="35d3c-137">The [Form Tag Helper](xref:mvc/views/working-with-forms) generates a hidden anti-forgery token that must match the `[ValidateAntiForgeryToken]` generated anti-forgery token in the `Edit` method of the Movies controller.</span></span> <span data-ttu-id="35d3c-138">Per altre informazioni, vedere <xref:security/anti-request-forgery>.</span><span class="sxs-lookup"><span data-stu-id="35d3c-138">For more information, see <xref:security/anti-request-forgery>.</span></span>

<span data-ttu-id="35d3c-139">Il metodo `HttpGet Edit` accetta il parametro `ID`, cerca il film tramite il metodo `FindAsync` Entity Framework e restituisce il film selezionato nella vista Edit.</span><span class="sxs-lookup"><span data-stu-id="35d3c-139">The `HttpGet Edit` method takes the movie `ID` parameter, looks up the movie using the Entity Framework `FindAsync` method, and returns the selected movie to the Edit view.</span></span> <span data-ttu-id="35d3c-140">Se non vengono trovati film, viene restituito l'errore HTTP 404 `NotFound`.</span><span class="sxs-lookup"><span data-stu-id="35d3c-140">If a movie cannot be found, `NotFound` (HTTP 404) is returned.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

<span data-ttu-id="35d3c-141">Quando il sistema di scaffolding ha creato la vista Edit, ha esaminato la classe `Movie` e il codice creato per eseguire il rendering degli elementi `<label>` e `<input>` per ogni proprietà della classe.</span><span class="sxs-lookup"><span data-stu-id="35d3c-141">When the scaffolding system created the Edit view, it examined the `Movie` class and created code to render `<label>` and `<input>` elements for each property of the class.</span></span> <span data-ttu-id="35d3c-142">L'esempio seguente illustra la vista Edit (Modifica) generata dal sistema di scaffolding di Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="35d3c-142">The following example shows the Edit view that was generated by the Visual Studio scaffolding system:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/EditOriginal.cshtml)]

<span data-ttu-id="35d3c-143">Si noti come il modello di vista contiene un'istruzione `@model MvcMovie.Models.Movie` all'inizio del file.</span><span class="sxs-lookup"><span data-stu-id="35d3c-143">Notice how the view template has a `@model MvcMovie.Models.Movie` statement at the top of the file.</span></span> <span data-ttu-id="35d3c-144">`@model MvcMovie.Models.Movie` specifica che il modelli previsto dalla vista per il modello di vista sia di tipo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="35d3c-144">`@model MvcMovie.Models.Movie` specifies that the view expects the model for the view template to be of type `Movie`.</span></span>

<span data-ttu-id="35d3c-145">Il codice di scaffolding usa diversi metodi helper tag per semplificare il markup HTML.</span><span class="sxs-lookup"><span data-stu-id="35d3c-145">The scaffolded code uses several Tag Helper methods to streamline the HTML markup.</span></span> <span data-ttu-id="35d3c-146">L' [Helper Tag Label](xref:mvc/views/working-with-forms) Visualizza il nome del campo ("title", "ReleaseDate", "genre" o "price").</span><span class="sxs-lookup"><span data-stu-id="35d3c-146">The [Label Tag Helper](xref:mvc/views/working-with-forms) displays the name of the field ("Title", "ReleaseDate", "Genre", or "Price").</span></span> <span data-ttu-id="35d3c-147">L'[helper tag di input](xref:mvc/views/working-with-forms) esegue il rendering di un elemento `<input>` HTML.</span><span class="sxs-lookup"><span data-stu-id="35d3c-147">The [Input Tag Helper](xref:mvc/views/working-with-forms) renders an HTML `<input>` element.</span></span> <span data-ttu-id="35d3c-148">L'[helper tag di convalida](xref:mvc/views/working-with-forms) visualizza eventuali messaggi di convalida associati a questa proprietà.</span><span class="sxs-lookup"><span data-stu-id="35d3c-148">The [Validation Tag Helper](xref:mvc/views/working-with-forms) displays any validation messages associated with that property.</span></span>

<span data-ttu-id="35d3c-149">Eseguire l'applicazione e passare all'URL `/Movies`.</span><span class="sxs-lookup"><span data-stu-id="35d3c-149">Run the application and navigate to the `/Movies` URL.</span></span> <span data-ttu-id="35d3c-150">Fare clic su un collegamento di **modifica** .</span><span class="sxs-lookup"><span data-stu-id="35d3c-150">Click an **Edit** link.</span></span> <span data-ttu-id="35d3c-151">Nel browser visualizzare l'origine per la pagina.</span><span class="sxs-lookup"><span data-stu-id="35d3c-151">In the browser, view the source for the page.</span></span> <span data-ttu-id="35d3c-152">Il codice HTML generato per l'elemento `<form>` è riportato di seguito.</span><span class="sxs-lookup"><span data-stu-id="35d3c-152">The generated HTML for the `<form>` element is shown below.</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/edit_view_source.html?highlight=1,6,10,17,24,28)]

<span data-ttu-id="35d3c-153">Gli elementi `<input>` si trovano in un elemento `HTML <form>` il cui attributo `action` è impostato per inviare all'URL `/Movies/Edit/id`.</span><span class="sxs-lookup"><span data-stu-id="35d3c-153">The `<input>` elements are in an `HTML <form>` element whose `action` attribute is set to post to the `/Movies/Edit/id` URL.</span></span> <span data-ttu-id="35d3c-154">I dati del modulo verranno inviati al server quando si fa clic sul pulsante `Save`.</span><span class="sxs-lookup"><span data-stu-id="35d3c-154">The form data will be posted to the server when the `Save` button is clicked.</span></span> <span data-ttu-id="35d3c-155">L'ultima riga prima dell'elemento `</form>` di chiusura mostra il token [XSRF](xref:security/anti-request-forgery) nascosto generato dall'[helper tag del modulo](xref:mvc/views/working-with-forms).</span><span class="sxs-lookup"><span data-stu-id="35d3c-155">The last line before the closing `</form>` element shows the hidden [XSRF](xref:security/anti-request-forgery) token generated by the [Form Tag Helper](xref:mvc/views/working-with-forms).</span></span>

## <a name="processing-the-post-request"></a><span data-ttu-id="35d3c-156">Elaborazione della richiesta POST</span><span class="sxs-lookup"><span data-stu-id="35d3c-156">Processing the POST Request</span></span>

<span data-ttu-id="35d3c-157">Nell'elenco seguente viene indicata la versione `[HttpPost]` del metodo di azione `Edit`.</span><span class="sxs-lookup"><span data-stu-id="35d3c-157">The following listing shows the `[HttpPost]` version of the `Edit` action method.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

<span data-ttu-id="35d3c-158">L'attributo `[ValidateAntiForgeryToken]` convalida il token [XSRF](xref:security/anti-request-forgery) nascosto generato dal generatore di token antifalsificazione nell'[helper tag del modulo](xref:mvc/views/working-with-forms)</span><span class="sxs-lookup"><span data-stu-id="35d3c-158">The `[ValidateAntiForgeryToken]` attribute validates the hidden [XSRF](xref:security/anti-request-forgery) token generated by the anti-forgery token generator in the [Form Tag Helper](xref:mvc/views/working-with-forms)</span></span>

<span data-ttu-id="35d3c-159">Il sistema di [associazione di modelli](xref:mvc/models/model-binding) accetta i valori del modulo inseriti e crea un oggetto `Movie` passato come parametro `movie`.</span><span class="sxs-lookup"><span data-stu-id="35d3c-159">The [model binding](xref:mvc/models/model-binding) system takes the posted form values and creates a `Movie` object that's passed as the `movie` parameter.</span></span> <span data-ttu-id="35d3c-160">La `ModelState.IsValid` proprietà verifica che i dati inviati nel form possano essere utilizzati per modificare (modificare o aggiornare) un `Movie` oggetto.</span><span class="sxs-lookup"><span data-stu-id="35d3c-160">The `ModelState.IsValid` property verifies that the data submitted in the form can be used to modify (edit or update) a `Movie` object.</span></span> <span data-ttu-id="35d3c-161">Se sono validi, i dati vengono salvati.</span><span class="sxs-lookup"><span data-stu-id="35d3c-161">If the data is valid, it's saved.</span></span> <span data-ttu-id="35d3c-162">I dati dei film aggiornati (modificati) vengono salvati nel database chiamando il metodo `SaveChangesAsync` del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="35d3c-162">The updated (edited) movie data is saved to the database by calling the `SaveChangesAsync` method of database context.</span></span> <span data-ttu-id="35d3c-163">Dopo avere salvato i dati, il codice reindirizza l'utente al metodo di azione `Index` della classe `MoviesController`, che visualizza la raccolta di film, incluse le modifiche appena apportate.</span><span class="sxs-lookup"><span data-stu-id="35d3c-163">After saving the data, the code redirects the user to the `Index` action method of the `MoviesController` class, which displays the movie collection, including the changes just made.</span></span>

<span data-ttu-id="35d3c-164">Prima di inviare il modulo al server, la convalida sul lato client controlla tutte le regole di convalida nei campi.</span><span class="sxs-lookup"><span data-stu-id="35d3c-164">Before the form is posted to the server, client-side validation checks any validation rules on the fields.</span></span> <span data-ttu-id="35d3c-165">Se sono presenti errori di convalida, viene visualizzato un messaggio di errore e il modulo non viene inviato.</span><span class="sxs-lookup"><span data-stu-id="35d3c-165">If there are any validation errors, an error message is displayed and the form isn't posted.</span></span> <span data-ttu-id="35d3c-166">Se JavaScript è disabilitato, non verrà eseguita la convalida sul lato client, ma il server rileverà i valori inviati non validi e i valori del modulo verranno visualizzati nuovamente con messaggi di errore.</span><span class="sxs-lookup"><span data-stu-id="35d3c-166">If JavaScript is disabled, you won't have client-side validation but the server will detect the posted values that are not valid, and the form values will be redisplayed with error messages.</span></span> <span data-ttu-id="35d3c-167">Più avanti in questa esercitazione la [convalida del modello](xref:mvc/models/validation) verrà esaminata in maggiore dettaglio.</span><span class="sxs-lookup"><span data-stu-id="35d3c-167">Later in the tutorial we examine [Model Validation](xref:mvc/models/validation) in more detail.</span></span> <span data-ttu-id="35d3c-168">L' [helper tag di convalida](xref:mvc/views/working-with-forms) nel modello di vista *Views/Movies/Edit.cshtml* gestisce la visualizzazione dei messaggi di errore appropriati.</span><span class="sxs-lookup"><span data-stu-id="35d3c-168">The [Validation Tag Helper](xref:mvc/views/working-with-forms) in the *Views/Movies/Edit.cshtml* view template takes care of displaying appropriate error messages.</span></span>

![Vista Edit (Modifica): un'eccezione per un valore di prezzo non corretto di abc indica che il campo del prezzo deve essere un numero.](~/tutorials/first-mvc-app/controller-methods-views/_static/val.png)

<span data-ttu-id="35d3c-171">Tutti i metodi `HttpGet` nel controller di film seguono un pattern simile.</span><span class="sxs-lookup"><span data-stu-id="35d3c-171">All the `HttpGet` methods in the movie controller follow a similar pattern.</span></span> <span data-ttu-id="35d3c-172">Ricevono un oggetto film (o un elenco di oggetti nel caso di `Index`) e passano l'oggetto (modello) alla vista.</span><span class="sxs-lookup"><span data-stu-id="35d3c-172">They get a movie object (or list of objects, in the case of `Index`), and pass the object (model) to the view.</span></span> <span data-ttu-id="35d3c-173">Il metodo `Create` passa un oggetto vuoto alla vista `Create`.</span><span class="sxs-lookup"><span data-stu-id="35d3c-173">The `Create` method passes an empty movie object to the `Create` view.</span></span> <span data-ttu-id="35d3c-174">Tutti i metodi che creano, modificano, eliminano o cambiano in altro modo i dati, eseguono questa operazione nell'overload `[HttpPost]` del metodo.</span><span class="sxs-lookup"><span data-stu-id="35d3c-174">All the methods that create, edit, delete, or otherwise modify data do so in the `[HttpPost]` overload of the method.</span></span> <span data-ttu-id="35d3c-175">La modifica dei dati in un metodo `HTTP GET` è un rischio per la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="35d3c-175">Modifying data in an `HTTP GET` method is a security risk.</span></span> <span data-ttu-id="35d3c-176">La modifica dei dati in un metodo `HTTP GET` viola anche le procedure consigliate HTTP e il pattern [REST](http://rest.elkstein.org/) architetturale, che specifica che le richieste GET non devono modificare lo stato dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="35d3c-176">Modifying data in an `HTTP GET` method also violates HTTP best practices and the architectural [REST](http://rest.elkstein.org/) pattern, which specifies that GET requests shouldn't change the state of your application.</span></span> <span data-ttu-id="35d3c-177">In altre parole, l'esecuzione di un'operazione GET deve essere sicura, senza effetti collaterali e non modificare dati persistenti.</span><span class="sxs-lookup"><span data-stu-id="35d3c-177">In other words, performing a GET operation should be a safe operation that has no side effects and doesn't modify your persisted data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="35d3c-178">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="35d3c-178">Additional resources</span></span>

* [<span data-ttu-id="35d3c-179">Globalizzazione e localizzazione</span><span class="sxs-lookup"><span data-stu-id="35d3c-179">Globalization and localization</span></span>](xref:fundamentals/localization)
* [<span data-ttu-id="35d3c-180">Introduzione agli helper tag</span><span class="sxs-lookup"><span data-stu-id="35d3c-180">Introduction to Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="35d3c-181">Creare helper tag</span><span class="sxs-lookup"><span data-stu-id="35d3c-181">Author Tag Helpers</span></span>](xref:mvc/views/tag-helpers/authoring)
* <xref:security/anti-request-forgery>
* <span data-ttu-id="35d3c-182">Proteggere il controller dall'[dall'overposting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application).</span><span class="sxs-lookup"><span data-stu-id="35d3c-182">Protect your controller from [over-posting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)</span></span>
* [<span data-ttu-id="35d3c-183">ViewModel</span><span class="sxs-lookup"><span data-stu-id="35d3c-183">ViewModels</span></span>](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/)
* [<span data-ttu-id="35d3c-184">Helper tag di modulo</span><span class="sxs-lookup"><span data-stu-id="35d3c-184">Form Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="35d3c-185">Helper tag di input</span><span class="sxs-lookup"><span data-stu-id="35d3c-185">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="35d3c-186">Helper tag di etichetta</span><span class="sxs-lookup"><span data-stu-id="35d3c-186">Label Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="35d3c-187">Helper tag di selezione</span><span class="sxs-lookup"><span data-stu-id="35d3c-187">Select Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="35d3c-188">Helper tag di convalida</span><span class="sxs-lookup"><span data-stu-id="35d3c-188">Validation Tag Helper</span></span>](xref:mvc/views/working-with-forms)

> [!div class="step-by-step"]
> <span data-ttu-id="35d3c-189">[Precedente](working-with-sql.md) 
>  [Avanti](search.md)</span><span class="sxs-lookup"><span data-stu-id="35d3c-189">[Previous](working-with-sql.md)
[Next](search.md)</span></span>  
