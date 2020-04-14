---
title: Razor Pages con EF Core in ASP.NET Core - Leggere dati correlati - 6 di 8
author: rick-anderson
description: In questa esercitazione verranno letti e visualizzati dati correlati, ovvero dati che Entity Framework carica all'interno delle proprietà di navigazione.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
uid: data/ef-rp/read-related-data
ms.openlocfilehash: 0835dbf6b5434e4224d9f56ea3335df4b5d9e119
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277327"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---read-related-data---6-of-8"></a><span data-ttu-id="5bad5-103">Razor Pages con EF Core in ASP.NET Core - Leggere dati correlati - 6 di 8</span><span class="sxs-lookup"><span data-stu-id="5bad5-103">Razor Pages with EF Core in ASP.NET Core - Read Related Data - 6 of 8</span></span>

<span data-ttu-id="5bad5-104">[Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5bad5-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5bad5-105">Questa esercitazione illustra come leggere e visualizzare dati correlati.</span><span class="sxs-lookup"><span data-stu-id="5bad5-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="5bad5-106">I dati correlati sono dati che EF Core carica all'interno delle proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="5bad5-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="5bad5-107">Le figure seguenti mostrano le pagine completate per questa esercitazione:</span><span class="sxs-lookup"><span data-stu-id="5bad5-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Pagina di indice dei corsi](read-related-data/_static/courses-index30.png)

![Pagina di indice degli insegnanti](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="5bad5-110">Caricamento eager, esplicito e lazy</span><span class="sxs-lookup"><span data-stu-id="5bad5-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="5bad5-111">Esistono diversi modi con cui EF Core può caricare i dati correlati nelle proprietà di navigazione di un'entità:</span><span class="sxs-lookup"><span data-stu-id="5bad5-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="5bad5-112">[Caricamento eager](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="5bad5-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="5bad5-113">Il caricamento eager si verifica quando una query per un solo tipo di entità carica anche entità correlate.</span><span class="sxs-lookup"><span data-stu-id="5bad5-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="5bad5-114">Quando viene letta un'entità, vengono recuperati i dati correlati corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="5bad5-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="5bad5-115">Ciò in genere ha come risultato una query join singola che recupera tutti i dati necessari.</span><span class="sxs-lookup"><span data-stu-id="5bad5-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="5bad5-116">Per alcuni tipi di caricamento eager, EF Core genera più query.</span><span class="sxs-lookup"><span data-stu-id="5bad5-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="5bad5-117">L'esecuzione di più query può essere più efficiente rispetto a una singola query gigante.</span><span class="sxs-lookup"><span data-stu-id="5bad5-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="5bad5-118">Il caricamento eager viene specificato con i metodi `Include` e `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Esempio di caricamento eager](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="5bad5-120">Il caricamento eager invia più query quando è inclusa una navigazione di raccolte:</span><span class="sxs-lookup"><span data-stu-id="5bad5-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="5bad5-121">Una query per la query principale</span><span class="sxs-lookup"><span data-stu-id="5bad5-121">One query for the main query</span></span> 
  * <span data-ttu-id="5bad5-122">Una query per ogni raccolta "perimetrale" nell'albero del caricamento.</span><span class="sxs-lookup"><span data-stu-id="5bad5-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="5bad5-123">Query separate con `Load`: i dati possono essere recuperati in query separate ed EF Core "corregge" le proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="5bad5-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="5bad5-124">"Corregge" significa che EF Core popola automaticamente le proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="5bad5-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="5bad5-125">Le query separate con `Load` sono più simili a un caricamento esplicito che a un caricamento eager.</span><span class="sxs-lookup"><span data-stu-id="5bad5-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Esempio di query separate](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="5bad5-127">Nota: EF Core corregge automaticamente le proprietà di navigazione per qualsiasi altra entità caricata in precedenza nell'istanza contesto.</span><span class="sxs-lookup"><span data-stu-id="5bad5-127">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="5bad5-128">Anche se i dati per una proprietà di navigazione *non* sono inclusi in modo esplicito, la proprietà può comunque essere popolata se alcune o tutte le entità correlate sono state caricate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="5bad5-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="5bad5-129">[Caricamento esplicito](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="5bad5-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="5bad5-130">Quando un'entità viene letta per la prima volta, i dati correlati non vengono recuperati.</span><span class="sxs-lookup"><span data-stu-id="5bad5-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="5bad5-131">Per recuperare i dati correlati quando necessario, è necessario scrivere codice.</span><span class="sxs-lookup"><span data-stu-id="5bad5-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="5bad5-132">Il caricamento esplicito con query separate ha come risultato l'invio di più query al database.</span><span class="sxs-lookup"><span data-stu-id="5bad5-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="5bad5-133">Con il caricamento esplicito, il codice specifica le proprietà di navigazione da caricare.</span><span class="sxs-lookup"><span data-stu-id="5bad5-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="5bad5-134">Per eseguire il caricamento esplicito, usare il metodo `Load`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="5bad5-135">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5bad5-135">For example:</span></span>

  ![Esempio di caricamento esplicito](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="5bad5-137">[Caricamento lazy](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="5bad5-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="5bad5-138">[Il caricamento lazy è stato aggiunto a EF Core nella versione 2.1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="5bad5-138">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="5bad5-139">Quando un'entità viene letta per la prima volta, i dati correlati non vengono recuperati.</span><span class="sxs-lookup"><span data-stu-id="5bad5-139">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="5bad5-140">La prima volta che si accede a una proprietà di navigazione, i dati necessari per quest'ultima vengono recuperati automaticamente.</span><span class="sxs-lookup"><span data-stu-id="5bad5-140">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="5bad5-141">Ogni volta che si accede a una proprietà di navigazione per la prima volta, viene inviata una query al database.</span><span class="sxs-lookup"><span data-stu-id="5bad5-141">A query is sent to the database each time a navigation property is accessed for the first time.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="5bad5-142">Creare pagine Course</span><span class="sxs-lookup"><span data-stu-id="5bad5-142">Create Course pages</span></span>

<span data-ttu-id="5bad5-143">L'entità `Course` include una proprietà di navigazione che contiene l'entità `Department` correlata.</span><span class="sxs-lookup"><span data-stu-id="5bad5-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<span data-ttu-id="5bad5-145">Per visualizzare il nome del dipartimento assegnato per un corso:</span><span class="sxs-lookup"><span data-stu-id="5bad5-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="5bad5-146">Caricare l'entità `Department` correlata nella proprietà di navigazione `Course.Department`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="5bad5-147">Ottenere il nome dalla proprietà `Name` dell'entità `Department`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="5bad5-148">Scaffolding delle pagine Course</span><span class="sxs-lookup"><span data-stu-id="5bad5-148">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5bad5-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5bad5-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5bad5-150">Seguire le istruzioni in [Scaffolding delle pagine Student](xref:data/ef-rp/intro#scaffold-student-pages) con le eccezioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bad5-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="5bad5-151">Creare una cartella *Pages/Courses*.</span><span class="sxs-lookup"><span data-stu-id="5bad5-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="5bad5-152">Usare `Course` per la classe del modello.</span><span class="sxs-lookup"><span data-stu-id="5bad5-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="5bad5-153">Usare la classe di contesto esistente anziché crearne una nuova.</span><span class="sxs-lookup"><span data-stu-id="5bad5-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5bad5-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5bad5-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5bad5-155">Creare una cartella *Pages/Courses*.</span><span class="sxs-lookup"><span data-stu-id="5bad5-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="5bad5-156">Eseguire il comando seguente per eseguire lo scaffolding delle pagine Course.</span><span class="sxs-lookup"><span data-stu-id="5bad5-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="5bad5-157">**In Windows:**</span><span class="sxs-lookup"><span data-stu-id="5bad5-157">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="5bad5-158">**Su Linux o macOS:**</span><span class="sxs-lookup"><span data-stu-id="5bad5-158">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="5bad5-159">Aprire *Pages/Courses/Index.cshtml.cs* ed esaminare il metodo `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="5bad5-160">Il motore di scaffolding ha specificato il caricamento eager per la proprietà di navigazione `Department`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="5bad5-161">Il metodo `Include` specifica il caricamento eager.</span><span class="sxs-lookup"><span data-stu-id="5bad5-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="5bad5-162">Eseguire l'app e selezionare il collegamento **Courses** (Corsi).</span><span class="sxs-lookup"><span data-stu-id="5bad5-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="5bad5-163">La colonna dei dipartimenti visualizza il `DepartmentID`, che non è utile.</span><span class="sxs-lookup"><span data-stu-id="5bad5-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="5bad5-164">Visualizzare il nome del dipartimento</span><span class="sxs-lookup"><span data-stu-id="5bad5-164">Display the department name</span></span>

<span data-ttu-id="5bad5-165">Aggiornare Pages/Courses/Index.cshtml.cs con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="5bad5-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="5bad5-166">Il codice precedente modifica la proprietà `Course` in `Courses` e aggiunge `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="5bad5-167">`AsNoTracking` migliora le prestazioni, perché le entità restituite non vengono registrate,</span><span class="sxs-lookup"><span data-stu-id="5bad5-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="5bad5-168">dato che non vengono aggiornate nel contesto corrente.</span><span class="sxs-lookup"><span data-stu-id="5bad5-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="5bad5-169">Aggiornare *Pages/Courses/Index.cshtml* con il codice seguente.</span><span class="sxs-lookup"><span data-stu-id="5bad5-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="5bad5-170">Al codice con scaffolding sono state apportate le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bad5-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="5bad5-171">Il nome della proprietà `Course` è stato modificato in `Courses`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="5bad5-172">È stata aggiunta la colonna **Number** (Numero) con il valore della proprietà `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="5bad5-173">Per impostazione predefinita, non viene eseguito lo scaffolding delle chiavi primarie, perché in genere non sono significative per gli utenti finali.</span><span class="sxs-lookup"><span data-stu-id="5bad5-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="5bad5-174">In questo caso, tuttavia, la chiave primaria è significativa.</span><span class="sxs-lookup"><span data-stu-id="5bad5-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="5bad5-175">Modificare la colonna **Department** (Dipartimento) per visualizzare il nome del dipartimento.</span><span class="sxs-lookup"><span data-stu-id="5bad5-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="5bad5-176">Il codice visualizza la proprietà `Name` dell'entità `Department` che viene caricata nella proprietà di navigazione `Department`:</span><span class="sxs-lookup"><span data-stu-id="5bad5-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="5bad5-177">Eseguire l'app e selezionare la scheda **Courses** (Corsi) per visualizzare l'elenco con i nomi dei dipartimenti.</span><span class="sxs-lookup"><span data-stu-id="5bad5-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Pagina di indice dei corsi](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="5bad5-179">Caricamento di dati correlati con Select</span><span class="sxs-lookup"><span data-stu-id="5bad5-179">Loading related data with Select</span></span>

<span data-ttu-id="5bad5-180">Il metodo `OnGetAsync` carica i dati correlati con il metodo `Include`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="5bad5-181">Il metodo `Select` è un'alternativa che carica solo i dati correlati necessari.</span><span class="sxs-lookup"><span data-stu-id="5bad5-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="5bad5-182">Per singoli elementi, ad esempio per `Department.Name` usa un INNER JOIN SQL.</span><span class="sxs-lookup"><span data-stu-id="5bad5-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="5bad5-183">Per le raccolte usa un altro accesso al database, ma anche l'operatore `Include` fa lo stesso sulle raccolte.</span><span class="sxs-lookup"><span data-stu-id="5bad5-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="5bad5-184">Il codice seguente carica dati correlati con il metodo `Select`:</span><span class="sxs-lookup"><span data-stu-id="5bad5-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="5bad5-185">Il codice precedente non restituisce alcun tipo di entità, pertanto non viene eseguito alcun rilevamento.</span><span class="sxs-lookup"><span data-stu-id="5bad5-185">The preceding code doesn't return any entity types, therefore no tracking is done.</span></span> <span data-ttu-id="5bad5-186">Per ulteriori informazioni sul rilevamento di Entity Framework, vedere [rilevamento e query senza rilevamento](/ef/core/querying/tracking).</span><span class="sxs-lookup"><span data-stu-id="5bad5-186">For more information about the EF tracking, see [Tracking vs. No-Tracking Queries](/ef/core/querying/tracking).</span></span>

<span data-ttu-id="5bad5-187">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="5bad5-187">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="5bad5-188">Per un esempio completo, vedere [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) e [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs).</span><span class="sxs-lookup"><span data-stu-id="5bad5-188">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="5bad5-189">Creare pagine Instructor</span><span class="sxs-lookup"><span data-stu-id="5bad5-189">Create Instructor pages</span></span>

<span data-ttu-id="5bad5-190">Questa sezione descrive come eseguire lo scaffolding delle pagine Instructor e come aggiungere corsi e iscrizioni correlati alla pagina di indice degli insegnanti.</span><span class="sxs-lookup"><span data-stu-id="5bad5-190">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="5bad5-191">![Pagina Indice Istruttori](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="5bad5-191">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="5bad5-192">Questa pagina legge e visualizza dati correlati nei modi seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bad5-192">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="5bad5-193">L'elenco di insegnanti visualizza dati correlati provenienti dall'entità `OfficeAssignment`, Office (Ufficio) nella figura precedente.</span><span class="sxs-lookup"><span data-stu-id="5bad5-193">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="5bad5-194">Tra le entità `Instructor` e `OfficeAssignment` c'è una relazione uno-a-zero-o-uno.</span><span class="sxs-lookup"><span data-stu-id="5bad5-194">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="5bad5-195">Per le entità `OfficeAssignment` viene usato il caricamento eager.</span><span class="sxs-lookup"><span data-stu-id="5bad5-195">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="5bad5-196">Il caricamento eager è in genere più efficiente quando i dati correlati devono essere visualizzati.</span><span class="sxs-lookup"><span data-stu-id="5bad5-196">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="5bad5-197">In questo caso, devono essere visualizzate le assegnazioni di ufficio degli insegnanti.</span><span class="sxs-lookup"><span data-stu-id="5bad5-197">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="5bad5-198">Quando l'utente seleziona un insegnante, vengono visualizzate le entità `Course` correlate.</span><span class="sxs-lookup"><span data-stu-id="5bad5-198">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="5bad5-199">Tra le entità `Instructor` e `Course` esiste una relazione molti-a-molti.</span><span class="sxs-lookup"><span data-stu-id="5bad5-199">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="5bad5-200">Il caricamento eager viene usato per le entità `Course` e le entità `Department` correlate.</span><span class="sxs-lookup"><span data-stu-id="5bad5-200">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="5bad5-201">In questo caso, query separate potrebbero essere più efficienti, perché sono necessari solo i corsi per l'insegnante selezionato.</span><span class="sxs-lookup"><span data-stu-id="5bad5-201">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="5bad5-202">Questo esempio illustra come usare il caricamento eager per le proprietà di navigazione di entità all'interno di proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="5bad5-202">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="5bad5-203">Quando l'utente seleziona un corso, vengono visualizzati i dati correlati dall'entità `Enrollments`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-203">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="5bad5-204">Nella figura precedente, vengono visualizzati il voto e il nome degli studenti.</span><span class="sxs-lookup"><span data-stu-id="5bad5-204">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="5bad5-205">Tra le entità `Course` e `Enrollment` esiste una relazione uno-a-molti.</span><span class="sxs-lookup"><span data-stu-id="5bad5-205">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="5bad5-206">Creare un modello di visualizzazione</span><span class="sxs-lookup"><span data-stu-id="5bad5-206">Create a view model</span></span>

<span data-ttu-id="5bad5-207">La pagina Instructors (Insegnanti) mostra i dati di tre tabelle diverse.</span><span class="sxs-lookup"><span data-stu-id="5bad5-207">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="5bad5-208">È necessario un modello di visualizzazione che includa tre proprietà che rappresentano le tre tabelle.</span><span class="sxs-lookup"><span data-stu-id="5bad5-208">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="5bad5-209">Creare *SchoolViewModels/InstructorIndexData.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="5bad5-209">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="5bad5-210">Scaffolding delle pagine Instructor</span><span class="sxs-lookup"><span data-stu-id="5bad5-210">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5bad5-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5bad5-211">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5bad5-212">Seguire le istruzioni in [Scaffolding delle pagine Student](xref:data/ef-rp/intro#scaffold-student-pages) con le eccezioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bad5-212">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="5bad5-213">Creare una cartella *Pages/Instructors*.</span><span class="sxs-lookup"><span data-stu-id="5bad5-213">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="5bad5-214">Usare `Instructor` per la classe del modello.</span><span class="sxs-lookup"><span data-stu-id="5bad5-214">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="5bad5-215">Usare la classe di contesto esistente anziché crearne una nuova.</span><span class="sxs-lookup"><span data-stu-id="5bad5-215">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5bad5-216">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5bad5-216">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5bad5-217">Creare una cartella *Pages/Instructors*.</span><span class="sxs-lookup"><span data-stu-id="5bad5-217">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="5bad5-218">Eseguire il comando seguente per eseguire lo scaffolding delle pagine Instructor.</span><span class="sxs-lookup"><span data-stu-id="5bad5-218">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="5bad5-219">**In Windows:**</span><span class="sxs-lookup"><span data-stu-id="5bad5-219">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="5bad5-220">**Su Linux o macOS:**</span><span class="sxs-lookup"><span data-stu-id="5bad5-220">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="5bad5-221">Per visualizzare l'aspetto della pagina con scaffolding prima di aggiornarla, eseguire l'app e passare alla pagina Instructors.</span><span class="sxs-lookup"><span data-stu-id="5bad5-221">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="5bad5-222">Aggiornare *Pages/Instructors/Index.cshtml.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="5bad5-222">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="5bad5-223">Il metodo `OnGetAsync` accetta i dati di route facoltativi per l'ID dell'insegnante selezionato.</span><span class="sxs-lookup"><span data-stu-id="5bad5-223">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="5bad5-224">Esaminare la query nel file *Pages/Instructors/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="5bad5-224">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="5bad5-225">Il codice specifica il caricamento eager delle proprietà di navigazione seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bad5-225">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="5bad5-226">Si noti la ripetizione dei metodi `Include` e `ThenInclude` per `CourseAssignments` e `Course`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-226">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="5bad5-227">Questa ripetizione è necessaria per specificare il caricamento eager per due proprietà di navigazione dell'entità `Course`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-227">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="5bad5-228">Il codice seguente viene eseguito quando viene selezionato un insegnante (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="5bad5-228">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="5bad5-229">L'insegnante selezionato viene recuperato dall'elenco di insegnanti nel modello di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="5bad5-229">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="5bad5-230">La proprietà `Courses` del modello di visualizzazione viene caricata con le entità `Course` dalla proprietà di navigazione `CourseAssignments` di tale insegnante.</span><span class="sxs-lookup"><span data-stu-id="5bad5-230">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="5bad5-231">Il metodo `Where` restituisce una raccolta.</span><span class="sxs-lookup"><span data-stu-id="5bad5-231">The `Where` method returns a collection.</span></span> <span data-ttu-id="5bad5-232">Tuttavia, in questo caso, il filtro selezionerà una singola entità.</span><span class="sxs-lookup"><span data-stu-id="5bad5-232">But in this case, the filter will select a single entity.</span></span> <span data-ttu-id="5bad5-233">Il metodo `Single` viene quindi chiamato per convertire la raccolta in una singola entità `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-233">so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="5bad5-234">L'entità `Instructor` consente l'accesso alla proprietà `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-234">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="5bad5-235">`CourseAssignments` consente l'accesso alle entità `Course` correlate.</span><span class="sxs-lookup"><span data-stu-id="5bad5-235">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Instructor-to-Courses m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="5bad5-237">Il metodo `Single` viene usato in una raccolta quando quest'ultima ha un solo elemento.</span><span class="sxs-lookup"><span data-stu-id="5bad5-237">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="5bad5-238">Il metodo `Single` genera un'eccezione se la raccolta è vuota o se contiene più elementi.</span><span class="sxs-lookup"><span data-stu-id="5bad5-238">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="5bad5-239">In alternativa, è possibile usare `SingleOrDefault`, che restituisce un valore predefinito (Null in questo caso) se la raccolta è vuota.</span><span class="sxs-lookup"><span data-stu-id="5bad5-239">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="5bad5-240">Il codice seguente popola la proprietà `Enrollments` del modello di visualizzazione quando è selezionato un corso:</span><span class="sxs-lookup"><span data-stu-id="5bad5-240">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="5bad5-241">Aggiornare la pagina di indice degli insegnanti</span><span class="sxs-lookup"><span data-stu-id="5bad5-241">Update the instructors Index page</span></span>

<span data-ttu-id="5bad5-242">Aggiornare *Pages/Instructors/Index.cshtml* con il codice seguente.</span><span class="sxs-lookup"><span data-stu-id="5bad5-242">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="5bad5-243">Il codice precedente apporta le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bad5-243">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="5bad5-244">Aggiorna la direttiva `page` da `@page` a `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-244">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="5bad5-245">`"{id:int?}"` è un modello di route.</span><span class="sxs-lookup"><span data-stu-id="5bad5-245">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="5bad5-246">Il modello di route cambia le stringhe di query di tipo integer nell'URL in dati di route.</span><span class="sxs-lookup"><span data-stu-id="5bad5-246">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="5bad5-247">Se ad esempio si fa clic su sul collegamento **Select** (Seleziona) per un insegnante con la sola direttiva `@page`, viene generato un URL come il seguente:</span><span class="sxs-lookup"><span data-stu-id="5bad5-247">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="5bad5-248">Quando la direttiva della pagina è `@page "{id:int?}"`, l'URL è:</span><span class="sxs-lookup"><span data-stu-id="5bad5-248">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="5bad5-249">Aggiunge una colonna **Office** che visualizza `item.OfficeAssignment.Location` solo se `item.OfficeAssignment` non è Null.</span><span class="sxs-lookup"><span data-stu-id="5bad5-249">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="5bad5-250">Poiché questa è una relazione uno-a-zero-o-uno, potrebbe non esserci un'entità OfficeAssignment correlata.</span><span class="sxs-lookup"><span data-stu-id="5bad5-250">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="5bad5-251">Aggiunge una colonna **Courses** che visualizza i corsi tenuti da ogni insegnante.</span><span class="sxs-lookup"><span data-stu-id="5bad5-251">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="5bad5-252">Per altre informazioni su questa sintassi del rasoio, vedere [Transizione di linea esplicita.](xref:mvc/views/razor#explicit-line-transition)</span><span class="sxs-lookup"><span data-stu-id="5bad5-252">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="5bad5-253">Aggiunge codice che aggiunge `class="success"` in modo dinamico all'elemento `tr` dell'insegnante e del corso selezionati.</span><span class="sxs-lookup"><span data-stu-id="5bad5-253">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="5bad5-254">In questo modo viene impostato un colore di sfondo per la riga selezionata tramite una classe Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="5bad5-254">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="5bad5-255">Aggiunge un nuovo collegamento ipertestuale con etichetta **Select**.</span><span class="sxs-lookup"><span data-stu-id="5bad5-255">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="5bad5-256">Questo collegamento invia l'ID dell'insegnante selezionato al metodo `Index` e imposta un colore di sfondo.</span><span class="sxs-lookup"><span data-stu-id="5bad5-256">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="5bad5-257">Aggiunge una tabella di corsi per l'insegnante selezionato.</span><span class="sxs-lookup"><span data-stu-id="5bad5-257">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="5bad5-258">Aggiunge una tabella di iscrizioni degli studenti per il corso selezionato.</span><span class="sxs-lookup"><span data-stu-id="5bad5-258">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="5bad5-259">Eseguire l'app e selezionare la scheda **Istruttori.** Nella pagina `Location` viene visualizzato l'ufficio dell'entità correlata. `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="5bad5-259">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="5bad5-260">Se `OfficeAssignment` è null, viene visualizzata una cella di tabella vuota.</span><span class="sxs-lookup"><span data-stu-id="5bad5-260">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="5bad5-261">Fare clic sul collegamento **Select** per un insegnante.</span><span class="sxs-lookup"><span data-stu-id="5bad5-261">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="5bad5-262">Verranno visualizzate le modifiche dello stile delle righe e i corsi assegnati a tale insegnante.</span><span class="sxs-lookup"><span data-stu-id="5bad5-262">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="5bad5-263">Selezionare un corso per visualizzare l'elenco degli studenti iscritti e i voti corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="5bad5-263">Select a course to see the list of enrolled students and their grades.</span></span>

![Pagina di indice degli insegnanti con un corso selezionato](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="5bad5-265">Usare Single</span><span class="sxs-lookup"><span data-stu-id="5bad5-265">Using Single</span></span>

<span data-ttu-id="5bad5-266">Il metodo `Single` può passare la condizione `Where` anziché chiamare il metodo `Where` separatamente:</span><span class="sxs-lookup"><span data-stu-id="5bad5-266">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="5bad5-267">L'uso di `Single` con una condizione Where è una questione di preferenza personale.</span><span class="sxs-lookup"><span data-stu-id="5bad5-267">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="5bad5-268">Non offre alcun vantaggio rispetto all'uso del metodo `Where`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-268">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="5bad5-269">Caricamento esplicito</span><span class="sxs-lookup"><span data-stu-id="5bad5-269">Explicit loading</span></span>

<span data-ttu-id="5bad5-270">Il codice corrente specifica il caricamento eager per `Enrollments` e `Students`:</span><span class="sxs-lookup"><span data-stu-id="5bad5-270">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="5bad5-271">Si supponga che gli utenti richiedano raramente la visualizzazione delle iscrizioni a un corso.</span><span class="sxs-lookup"><span data-stu-id="5bad5-271">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="5bad5-272">In questo caso, per ottimizzare il funzionamento dell'app è utile caricare i dati delle iscrizioni solo se vengono richiesti.</span><span class="sxs-lookup"><span data-stu-id="5bad5-272">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="5bad5-273">In questa sezione, viene eseguito l'aggiornamento di `OnGetAsync` in modo da usare il caricamento esplicito di `Enrollments` e `Students`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-273">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="5bad5-274">Aggiornare *Pages/Instructors/Index.cshtml.cs* con il codice seguente.</span><span class="sxs-lookup"><span data-stu-id="5bad5-274">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="5bad5-275">Il codice precedente rilascia le chiamate del metodo *ThenInclude* per i dati delle iscrizioni e degli studenti.</span><span class="sxs-lookup"><span data-stu-id="5bad5-275">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="5bad5-276">Se è selezionato un corso, il codice di caricamento esplicito recupera:</span><span class="sxs-lookup"><span data-stu-id="5bad5-276">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="5bad5-277">Le entità `Enrollment` per il corso selezionato.</span><span class="sxs-lookup"><span data-stu-id="5bad5-277">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="5bad5-278">Le entità `Student` per ogni entità `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-278">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="5bad5-279">Si noti che nel codice precedente `.AsNoTracking()` è impostato come commento.</span><span class="sxs-lookup"><span data-stu-id="5bad5-279">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="5bad5-280">Le proprietà di navigazione possono solo essere caricate in modo esplicito per le entità registrate.</span><span class="sxs-lookup"><span data-stu-id="5bad5-280">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="5bad5-281">Testare l'app.</span><span class="sxs-lookup"><span data-stu-id="5bad5-281">Test the app.</span></span> <span data-ttu-id="5bad5-282">Dal punto di vista dell'utente, l'app si comporta in modo identico alla versione precedente.</span><span class="sxs-lookup"><span data-stu-id="5bad5-282">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5bad5-283">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="5bad5-283">Next steps</span></span>

<span data-ttu-id="5bad5-284">La prossima esercitazione illustra come aggiornare i dati correlati.</span><span class="sxs-lookup"><span data-stu-id="5bad5-284">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="5bad5-285">[Esercitazione](xref:data/ef-rp/complex-data-model)
>precedente[Esercitazione successiva](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="5bad5-285">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5bad5-286">In questa esercitazione vengono letti e visualizzati dati correlati.</span><span class="sxs-lookup"><span data-stu-id="5bad5-286">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="5bad5-287">I dati correlati sono dati che EF Core carica all'interno delle proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="5bad5-287">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="5bad5-288">Se si verificano problemi che non si è in grado di risolvere, [scaricare o visualizzare l'app completa](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="5bad5-288">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="5bad5-289">[Istruzioni per il download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="5bad5-289">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="5bad5-290">Le figure seguenti mostrano le pagine completate per questa esercitazione:</span><span class="sxs-lookup"><span data-stu-id="5bad5-290">The following illustrations show the completed pages for this tutorial:</span></span>

![Pagina di indice dei corsi](read-related-data/_static/courses-index.png)

![Pagina di indice degli insegnanti](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="5bad5-293">Caricamento eager, esplicito e lazy dei dati correlati</span><span class="sxs-lookup"><span data-stu-id="5bad5-293">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="5bad5-294">Esistono diversi modi con cui EF Core può caricare i dati correlati nelle proprietà di navigazione di un'entità:</span><span class="sxs-lookup"><span data-stu-id="5bad5-294">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="5bad5-295">[Caricamento eager](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="5bad5-295">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="5bad5-296">Il caricamento eager si verifica quando una query per un solo tipo di entità carica anche entità correlate.</span><span class="sxs-lookup"><span data-stu-id="5bad5-296">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="5bad5-297">Quando l'entità viene letta, vengono recuperati i dati correlati corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="5bad5-297">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="5bad5-298">Ciò in genere ha come risultato una query join singola che recupera tutti i dati necessari.</span><span class="sxs-lookup"><span data-stu-id="5bad5-298">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="5bad5-299">Per alcuni tipi di caricamento eager, EF Core genera più query.</span><span class="sxs-lookup"><span data-stu-id="5bad5-299">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="5bad5-300">La generazione di più query può essere più efficiente rispetto al caso di alcune query in EF6, in cui era presente una singola query.</span><span class="sxs-lookup"><span data-stu-id="5bad5-300">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="5bad5-301">Il caricamento eager viene specificato con i metodi `Include` e `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-301">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Esempio di caricamento eager](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="5bad5-303">Il caricamento eager invia più query quando è inclusa una navigazione di raccolte:</span><span class="sxs-lookup"><span data-stu-id="5bad5-303">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="5bad5-304">Una query per la query principale</span><span class="sxs-lookup"><span data-stu-id="5bad5-304">One query for the main query</span></span> 
  * <span data-ttu-id="5bad5-305">Una query per ogni raccolta "perimetrale" nell'albero del caricamento.</span><span class="sxs-lookup"><span data-stu-id="5bad5-305">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="5bad5-306">Query separate con `Load`: i dati possono essere recuperati in query separate ed EF Core "corregge" le proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="5bad5-306">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="5bad5-307">"Corregge" significa che EF Core popola automaticamente le proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="5bad5-307">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="5bad5-308">Le query separate con `Load` sono più simili a un caricamento esplicito che a un caricamento eager.</span><span class="sxs-lookup"><span data-stu-id="5bad5-308">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Esempio di query separate](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="5bad5-310">Nota: EF Core corregge automaticamente le proprietà di navigazione per qualsiasi altra entità caricata in precedenza nell'istanza contesto.</span><span class="sxs-lookup"><span data-stu-id="5bad5-310">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="5bad5-311">Anche se i dati per una proprietà di navigazione *non* sono inclusi in modo esplicito, la proprietà può comunque essere popolata se alcune o tutte le entità correlate sono state caricate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="5bad5-311">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="5bad5-312">[Caricamento esplicito](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="5bad5-312">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="5bad5-313">Quando un'entità viene letta per la prima volta, i dati correlati non vengono recuperati.</span><span class="sxs-lookup"><span data-stu-id="5bad5-313">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="5bad5-314">Per recuperare i dati correlati quando necessario, è necessario scrivere codice.</span><span class="sxs-lookup"><span data-stu-id="5bad5-314">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="5bad5-315">Il caricamento esplicito con query separate ha come risultato l'invio di più query al database.</span><span class="sxs-lookup"><span data-stu-id="5bad5-315">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="5bad5-316">Con il caricamento esplicito, il codice specifica le proprietà di navigazione da caricare.</span><span class="sxs-lookup"><span data-stu-id="5bad5-316">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="5bad5-317">Per eseguire il caricamento esplicito, usare il metodo `Load`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-317">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="5bad5-318">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5bad5-318">For example:</span></span>

  ![Esempio di caricamento esplicito](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="5bad5-320">[Caricamento lazy](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="5bad5-320">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="5bad5-321">[Il caricamento lazy è stato aggiunto a EF Core nella versione 2.1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="5bad5-321">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="5bad5-322">Quando un'entità viene letta per la prima volta, i dati correlati non vengono recuperati.</span><span class="sxs-lookup"><span data-stu-id="5bad5-322">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="5bad5-323">La prima volta che si accede a una proprietà di navigazione, i dati necessari per quest'ultima vengono recuperati automaticamente.</span><span class="sxs-lookup"><span data-stu-id="5bad5-323">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="5bad5-324">Ogni volta che si accede a una proprietà di navigazione per la prima volta, viene inviata una query al database.</span><span class="sxs-lookup"><span data-stu-id="5bad5-324">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="5bad5-325">L'operatore `Select` carica solo i dati correlati necessari.</span><span class="sxs-lookup"><span data-stu-id="5bad5-325">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="5bad5-326">Creare una pagina Course che visualizza il nome dei dipartimenti</span><span class="sxs-lookup"><span data-stu-id="5bad5-326">Create a Course page that displays department name</span></span>

<span data-ttu-id="5bad5-327">L'entità Course include una proprietà di navigazione che contiene l'entità `Department`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-327">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="5bad5-328">L'entità `Department` contiene il dipartimento a cui il corso è assegnato.</span><span class="sxs-lookup"><span data-stu-id="5bad5-328">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="5bad5-329">Per visualizzare il nome del dipartimento assegnato in un elenco dei corsi:</span><span class="sxs-lookup"><span data-stu-id="5bad5-329">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="5bad5-330">Ottenere la proprietà `Name` dall'entità `Department`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-330">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="5bad5-331">L'entità `Department` proviene dalla proprietà di navigazione `Course.Department`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-331">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="5bad5-333">Scaffolding del modello Course</span><span class="sxs-lookup"><span data-stu-id="5bad5-333">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5bad5-334">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5bad5-334">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="5bad5-335">Seguire le istruzioni in [Eseguire lo scaffolding del modello Student (Studente)](xref:data/ef-rp/intro#scaffold-the-student-model) e usare `Course` per la classe modello.</span><span class="sxs-lookup"><span data-stu-id="5bad5-335">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5bad5-336">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5bad5-336">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="5bad5-337">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="5bad5-337">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="5bad5-338">Il comando precedente esegue lo scaffolding del modello `Course`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-338">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="5bad5-339">Aprire il progetto in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5bad5-339">Open the project in Visual Studio.</span></span>

<span data-ttu-id="5bad5-340">Aprire *Pages/Courses/Index.cshtml.cs* ed esaminare il metodo `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-340">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="5bad5-341">Il motore di scaffolding ha specificato il caricamento eager per la proprietà di navigazione `Department`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-341">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="5bad5-342">Il metodo `Include` specifica il caricamento eager.</span><span class="sxs-lookup"><span data-stu-id="5bad5-342">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="5bad5-343">Eseguire l'app e selezionare il collegamento **Courses** (Corsi).</span><span class="sxs-lookup"><span data-stu-id="5bad5-343">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="5bad5-344">La colonna dei dipartimenti visualizza il `DepartmentID`, che non è utile.</span><span class="sxs-lookup"><span data-stu-id="5bad5-344">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="5bad5-345">Aggiornare il metodo `OnGetAsync` con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="5bad5-345">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="5bad5-346">Il codice precedente aggiunge `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-346">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="5bad5-347">`AsNoTracking` migliora le prestazioni, perché le entità restituite non vengono registrate,</span><span class="sxs-lookup"><span data-stu-id="5bad5-347">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="5bad5-348">dato che non vengono aggiornate nel contesto corrente.</span><span class="sxs-lookup"><span data-stu-id="5bad5-348">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="5bad5-349">Aggiornare *Pages/Courses/Index.cshtml* con il markup evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="5bad5-349">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="5bad5-350">Al codice con scaffolding sono state apportate le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bad5-350">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="5bad5-351">Il titolo è stato modificato da Index (Indice) a Courses (Corsi).</span><span class="sxs-lookup"><span data-stu-id="5bad5-351">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="5bad5-352">È stata aggiunta la colonna **Number** (Numero) con il valore della proprietà `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-352">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="5bad5-353">Per impostazione predefinita, non viene eseguito lo scaffolding delle chiavi primarie, perché in genere non sono significative per gli utenti finali.</span><span class="sxs-lookup"><span data-stu-id="5bad5-353">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="5bad5-354">In questo caso, tuttavia, la chiave primaria è significativa.</span><span class="sxs-lookup"><span data-stu-id="5bad5-354">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="5bad5-355">Modificare la colonna **Department** (Dipartimento) per visualizzare il nome del dipartimento.</span><span class="sxs-lookup"><span data-stu-id="5bad5-355">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="5bad5-356">Il codice visualizza la proprietà `Name` dell'entità `Department` che viene caricata nella proprietà di navigazione `Department`:</span><span class="sxs-lookup"><span data-stu-id="5bad5-356">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="5bad5-357">Eseguire l'app e selezionare la scheda **Courses** (Corsi) per visualizzare l'elenco con i nomi dei dipartimenti.</span><span class="sxs-lookup"><span data-stu-id="5bad5-357">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Pagina di indice dei corsi](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="5bad5-359">Caricamento di dati correlati con Select</span><span class="sxs-lookup"><span data-stu-id="5bad5-359">Loading related data with Select</span></span>

<span data-ttu-id="5bad5-360">Il metodo `OnGetAsync` carica i dati correlati con il metodo `Include`:</span><span class="sxs-lookup"><span data-stu-id="5bad5-360">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="5bad5-361">L'operatore `Select` carica solo i dati correlati necessari.</span><span class="sxs-lookup"><span data-stu-id="5bad5-361">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="5bad5-362">Per singoli elementi, ad esempio per `Department.Name` usa un INNER JOIN SQL.</span><span class="sxs-lookup"><span data-stu-id="5bad5-362">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="5bad5-363">Per le raccolte usa un altro accesso al database, ma anche l'operatore `Include` fa lo stesso sulle raccolte.</span><span class="sxs-lookup"><span data-stu-id="5bad5-363">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="5bad5-364">Il codice seguente carica dati correlati con il metodo `Select`:</span><span class="sxs-lookup"><span data-stu-id="5bad5-364">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="5bad5-365">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="5bad5-365">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="5bad5-366">Per un esempio completo, vedere [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) e [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs).</span><span class="sxs-lookup"><span data-stu-id="5bad5-366">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="5bad5-367">Creare una pagina Instructors (Insegnanti) che mostri i corsi e le iscrizioni</span><span class="sxs-lookup"><span data-stu-id="5bad5-367">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="5bad5-368">In questa sezione viene creata la pagina Instructors (Insegnanti).</span><span class="sxs-lookup"><span data-stu-id="5bad5-368">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="5bad5-369">![Pagina Indice Istruttori](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="5bad5-369">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="5bad5-370">Questa pagina legge e visualizza dati correlati nei modi seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bad5-370">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="5bad5-371">L'elenco di insegnanti visualizza dati correlati provenienti dall'entità `OfficeAssignment`, Office (Ufficio) nella figura precedente.</span><span class="sxs-lookup"><span data-stu-id="5bad5-371">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="5bad5-372">Tra le entità `Instructor` e `OfficeAssignment` c'è una relazione uno-a-zero-o-uno.</span><span class="sxs-lookup"><span data-stu-id="5bad5-372">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="5bad5-373">Per le entità `OfficeAssignment` viene usato il caricamento eager.</span><span class="sxs-lookup"><span data-stu-id="5bad5-373">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="5bad5-374">Il caricamento eager è in genere più efficiente quando i dati correlati devono essere visualizzati.</span><span class="sxs-lookup"><span data-stu-id="5bad5-374">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="5bad5-375">In questo caso, devono essere visualizzate le assegnazioni di ufficio degli insegnanti.</span><span class="sxs-lookup"><span data-stu-id="5bad5-375">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="5bad5-376">Quando l'utente seleziona un insegnante (Harui nella figura precedente), vengono visualizzate le entità `Course` correlate.</span><span class="sxs-lookup"><span data-stu-id="5bad5-376">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="5bad5-377">Tra le entità `Instructor` e `Course` esiste una relazione molti-a-molti.</span><span class="sxs-lookup"><span data-stu-id="5bad5-377">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="5bad5-378">Il caricamento eager viene usato per le entità `Course` e le entità `Department` correlate.</span><span class="sxs-lookup"><span data-stu-id="5bad5-378">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="5bad5-379">In questo caso, query separate potrebbero essere più efficienti, perché sono necessari solo i corsi per l'insegnante selezionato.</span><span class="sxs-lookup"><span data-stu-id="5bad5-379">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="5bad5-380">Questo esempio illustra come usare il caricamento eager per le proprietà di navigazione di entità all'interno di proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="5bad5-380">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="5bad5-381">Quando l'utente seleziona un corso (Chemistry (Chimica) nella figura precedente), vengono visualizzati i dati correlati dell'entità `Enrollments`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-381">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="5bad5-382">Nella figura precedente, vengono visualizzati il voto e il nome degli studenti.</span><span class="sxs-lookup"><span data-stu-id="5bad5-382">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="5bad5-383">Tra le entità `Course` e `Enrollment` esiste una relazione uno-a-molti.</span><span class="sxs-lookup"><span data-stu-id="5bad5-383">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="5bad5-384">Creare un modello per la visualizzazione dell'indice degli insegnanti</span><span class="sxs-lookup"><span data-stu-id="5bad5-384">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="5bad5-385">La pagina Instructors (Insegnanti) mostra i dati di tre tabelle diverse.</span><span class="sxs-lookup"><span data-stu-id="5bad5-385">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="5bad5-386">Viene creato un modello di visualizzazione che include le tre entità che rappresentano le tre tabelle.</span><span class="sxs-lookup"><span data-stu-id="5bad5-386">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="5bad5-387">Nella cartella *SchoolViewModels* creare *InstructorIndexData.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="5bad5-387">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="5bad5-388">Scaffolding del modello Instructor</span><span class="sxs-lookup"><span data-stu-id="5bad5-388">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5bad5-389">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5bad5-389">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="5bad5-390">Seguire le istruzioni in [Eseguire lo scaffolding del modello Student (Studente)](xref:data/ef-rp/intro#scaffold-the-student-model) e usare `Instructor` per la classe modello.</span><span class="sxs-lookup"><span data-stu-id="5bad5-390">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5bad5-391">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5bad5-391">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="5bad5-392">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="5bad5-392">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="5bad5-393">Il comando precedente esegue lo scaffolding del modello `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-393">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="5bad5-394">Eseguire l'app e passare alla pagina Instructors (Insegnanti).</span><span class="sxs-lookup"><span data-stu-id="5bad5-394">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="5bad5-395">Sostituire *Pages/Instructors/Index.cshtml.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="5bad5-395">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="5bad5-396">Il metodo `OnGetAsync` accetta i dati di route facoltativi per l'ID dell'insegnante selezionato.</span><span class="sxs-lookup"><span data-stu-id="5bad5-396">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="5bad5-397">Esaminare la query nel file *Pages/Instructors/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="5bad5-397">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="5bad5-398">La query ha due istruzioni Include, che riguardano:</span><span class="sxs-lookup"><span data-stu-id="5bad5-398">The query has two includes:</span></span>

* <span data-ttu-id="5bad5-399">`OfficeAssignment`: visualizzato nella [visualizzazione degli insegnanti](#IP).</span><span class="sxs-lookup"><span data-stu-id="5bad5-399">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="5bad5-400">`CourseAssignments`: visualizza i corsi tenuti.</span><span class="sxs-lookup"><span data-stu-id="5bad5-400">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="5bad5-401">Aggiornare la pagina di indice degli insegnanti</span><span class="sxs-lookup"><span data-stu-id="5bad5-401">Update the instructors Index page</span></span>

<span data-ttu-id="5bad5-402">Aggiornare *Pages/Instructors/Index.cshtml* con il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="5bad5-402">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="5bad5-403">Il markup precedente apporta le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bad5-403">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="5bad5-404">Aggiorna la direttiva `page` da `@page` a `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-404">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="5bad5-405">`"{id:int?}"` è un modello di route.</span><span class="sxs-lookup"><span data-stu-id="5bad5-405">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="5bad5-406">Il modello di route cambia le stringhe di query di tipo integer nell'URL in dati di route.</span><span class="sxs-lookup"><span data-stu-id="5bad5-406">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="5bad5-407">Se ad esempio si fa clic su sul collegamento **Select** (Seleziona) per un insegnante con la sola direttiva `@page`, viene generato un URL come il seguente:</span><span class="sxs-lookup"><span data-stu-id="5bad5-407">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="5bad5-408">Quando la direttiva della pagina è `@page "{id:int?}"`, l'URL precedente è:</span><span class="sxs-lookup"><span data-stu-id="5bad5-408">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="5bad5-409">Il titolo pagina è **Instructors** (Insegnanti).</span><span class="sxs-lookup"><span data-stu-id="5bad5-409">Page title is **Instructors**.</span></span>
* <span data-ttu-id="5bad5-410">È stata aggiunta la colonna **Office** (Ufficio) che visualizza `item.OfficeAssignment.Location` solo se `item.OfficeAssignment` non è Null.</span><span class="sxs-lookup"><span data-stu-id="5bad5-410">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="5bad5-411">Poiché questa è una relazione uno-a-zero-o-uno, potrebbe non esserci un'entità OfficeAssignment correlata.</span><span class="sxs-lookup"><span data-stu-id="5bad5-411">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="5bad5-412">È stata aggiunta la colonna **Courses** (Corsi) che visualizza i corsi tenuti da ogni insegnante.</span><span class="sxs-lookup"><span data-stu-id="5bad5-412">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="5bad5-413">Per altre informazioni su questa sintassi del rasoio, vedere [Transizione di linea esplicita.](xref:mvc/views/razor#explicit-line-transition)</span><span class="sxs-lookup"><span data-stu-id="5bad5-413">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="5bad5-414">È stato aggiunto codice che aggiunge `class="success"` in modo dinamico all'elemento `tr` dell'insegnante selezionato.</span><span class="sxs-lookup"><span data-stu-id="5bad5-414">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="5bad5-415">In questo modo viene impostato un colore di sfondo per la riga selezionata tramite una classe Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="5bad5-415">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="5bad5-416">È stato aggiunto un nuovo collegamento ipertestuale con etichetta **Select** (Seleziona).</span><span class="sxs-lookup"><span data-stu-id="5bad5-416">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="5bad5-417">Questo collegamento invia l'ID dell'insegnante selezionato al metodo `Index` e imposta un colore di sfondo.</span><span class="sxs-lookup"><span data-stu-id="5bad5-417">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="5bad5-418">Eseguire l'app e selezionare la scheda **Istruttori.** Nella pagina `Location` viene visualizzato l'ufficio dell'entità correlata. `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="5bad5-418">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="5bad5-419">Se OfficeAssignment è Null, nella tabella viene visualizzata una cella vuota.</span><span class="sxs-lookup"><span data-stu-id="5bad5-419">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="5bad5-420">Fare clic sul collegamento **Select** (Seleziona).</span><span class="sxs-lookup"><span data-stu-id="5bad5-420">Click on the **Select** link.</span></span> <span data-ttu-id="5bad5-421">Lo stile delle righe cambia.</span><span class="sxs-lookup"><span data-stu-id="5bad5-421">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="5bad5-422">Aggiungere corsi tenuti dall'insegnante selezionato</span><span class="sxs-lookup"><span data-stu-id="5bad5-422">Add courses taught by selected instructor</span></span>

<span data-ttu-id="5bad5-423">Aggiornare il metodo `OnGetAsync` in *Pages/Instructors/Index.cshtml.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="5bad5-423">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="5bad5-424">Aggiungere `public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="5bad5-424">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="5bad5-425">Esaminare la query aggiornata:</span><span class="sxs-lookup"><span data-stu-id="5bad5-425">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="5bad5-426">La query precedente aggiunge l'entità `Department`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-426">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="5bad5-427">Il codice seguente viene eseguito quando viene selezionato un insegnante (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="5bad5-427">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="5bad5-428">L'insegnante selezionato viene recuperato dall'elenco di insegnanti nel modello di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="5bad5-428">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="5bad5-429">La proprietà `Courses` del modello di visualizzazione viene caricata con le entità `Course` dalla proprietà di navigazione `CourseAssignments` di tale insegnante.</span><span class="sxs-lookup"><span data-stu-id="5bad5-429">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="5bad5-430">Il metodo `Where` restituisce una raccolta.</span><span class="sxs-lookup"><span data-stu-id="5bad5-430">The `Where` method returns a collection.</span></span> <span data-ttu-id="5bad5-431">Nel metodo `Where` precedente viene restituita una sola entità `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-431">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="5bad5-432">Il metodo `Single` converte la raccolta in un'unica entità `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-432">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="5bad5-433">L'entità `Instructor` consente l'accesso alla proprietà `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-433">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="5bad5-434">`CourseAssignments` consente l'accesso alle entità `Course` correlate.</span><span class="sxs-lookup"><span data-stu-id="5bad5-434">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Instructor-to-Courses m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="5bad5-436">Il metodo `Single` viene usato in una raccolta quando quest'ultima ha un solo elemento.</span><span class="sxs-lookup"><span data-stu-id="5bad5-436">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="5bad5-437">Il metodo `Single` genera un'eccezione se la raccolta è vuota o se contiene più elementi.</span><span class="sxs-lookup"><span data-stu-id="5bad5-437">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="5bad5-438">In alternativa, è possibile usare `SingleOrDefault`, che restituisce un valore predefinito (Null in questo caso) se la raccolta è vuota.</span><span class="sxs-lookup"><span data-stu-id="5bad5-438">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="5bad5-439">L'uso di `SingleOrDefault` per una raccolta vuota:</span><span class="sxs-lookup"><span data-stu-id="5bad5-439">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="5bad5-440">Genera un'eccezione, a causa del tentativo di cercare la proprietà `Courses` in un riferimento Null.</span><span class="sxs-lookup"><span data-stu-id="5bad5-440">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="5bad5-441">Il messaggio di eccezione indica meno chiaramente la causa del problema.</span><span class="sxs-lookup"><span data-stu-id="5bad5-441">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="5bad5-442">Il codice seguente popola la proprietà `Enrollments` del modello di visualizzazione quando è selezionato un corso:</span><span class="sxs-lookup"><span data-stu-id="5bad5-442">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="5bad5-443">Aggiungere il markup seguente alla fine della pagina Razor *Pages/Instructors/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="5bad5-443">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="5bad5-444">Quando è selezionato un insegnante, il markup precedente visualizza un elenco dei corsi correlati all'insegnante stesso.</span><span class="sxs-lookup"><span data-stu-id="5bad5-444">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="5bad5-445">Testare l'app.</span><span class="sxs-lookup"><span data-stu-id="5bad5-445">Test the app.</span></span> <span data-ttu-id="5bad5-446">Fare clic su un collegamento **Select** (Seleziona) nella pagina Instructors (Insegnanti).</span><span class="sxs-lookup"><span data-stu-id="5bad5-446">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="5bad5-447">Visualizzare i dati degli studenti</span><span class="sxs-lookup"><span data-stu-id="5bad5-447">Show student data</span></span>

<span data-ttu-id="5bad5-448">In questa sezione, l'app viene aggiornata in modo da visualizzare i dati degli studenti per il corso selezionato.</span><span class="sxs-lookup"><span data-stu-id="5bad5-448">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="5bad5-449">Aggiornare la query nel metodo `OnGetAsync` in *Pages/Instructors/Index.cshtml.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="5bad5-449">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="5bad5-450">Aggiornare *Pages/Instructors/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="5bad5-450">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="5bad5-451">Aggiungere il markup seguente alla fine del file:</span><span class="sxs-lookup"><span data-stu-id="5bad5-451">Add the following markup to the end of the file:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="5bad5-452">Il markup precedente visualizza l'elenco degli studenti iscritti al corso selezionato.</span><span class="sxs-lookup"><span data-stu-id="5bad5-452">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="5bad5-453">Aggiornare la pagina e selezionare un insegnante.</span><span class="sxs-lookup"><span data-stu-id="5bad5-453">Refresh the page and select an instructor.</span></span> <span data-ttu-id="5bad5-454">Selezionare un corso per visualizzare l'elenco degli studenti iscritti e i voti corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="5bad5-454">Select a course to see the list of enrolled students and their grades.</span></span>

![Pagina di indice degli insegnanti con un corso selezionato](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="5bad5-456">Usare Single</span><span class="sxs-lookup"><span data-stu-id="5bad5-456">Using Single</span></span>

<span data-ttu-id="5bad5-457">Il metodo `Single` può passare la condizione `Where` anziché chiamare il metodo `Where` separatamente:</span><span class="sxs-lookup"><span data-stu-id="5bad5-457">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="5bad5-458">L'approccio `Single` precedente non offre alcun vantaggio rispetto all'uso di `Where`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-458">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="5bad5-459">Alcuni sviluppatori preferiscono lo stile dell'approccio `Single`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-459">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="5bad5-460">Caricamento esplicito</span><span class="sxs-lookup"><span data-stu-id="5bad5-460">Explicit loading</span></span>

<span data-ttu-id="5bad5-461">Il codice corrente specifica il caricamento eager per `Enrollments` e `Students`:</span><span class="sxs-lookup"><span data-stu-id="5bad5-461">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="5bad5-462">Si supponga che gli utenti richiedano raramente la visualizzazione delle iscrizioni a un corso.</span><span class="sxs-lookup"><span data-stu-id="5bad5-462">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="5bad5-463">In questo caso, per ottimizzare il funzionamento dell'app è utile caricare i dati delle iscrizioni solo se vengono richiesti.</span><span class="sxs-lookup"><span data-stu-id="5bad5-463">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="5bad5-464">In questa sezione, viene eseguito l'aggiornamento di `OnGetAsync` in modo da usare il caricamento esplicito di `Enrollments` e `Students`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-464">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="5bad5-465">Aggiornare `OnGetAsync` con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="5bad5-465">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="5bad5-466">Il codice precedente rilascia le chiamate del metodo *ThenInclude* per i dati delle iscrizioni e degli studenti.</span><span class="sxs-lookup"><span data-stu-id="5bad5-466">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="5bad5-467">Se è selezionato un corso, il codice evidenziato recupera:</span><span class="sxs-lookup"><span data-stu-id="5bad5-467">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="5bad5-468">Le entità `Enrollment` per il corso selezionato.</span><span class="sxs-lookup"><span data-stu-id="5bad5-468">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="5bad5-469">Le entità `Student` per ogni entità `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="5bad5-469">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="5bad5-470">Si noti che nel codice precedente `.AsNoTracking()` è commentato.</span><span class="sxs-lookup"><span data-stu-id="5bad5-470">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="5bad5-471">Le proprietà di navigazione possono solo essere caricate in modo esplicito per le entità registrate.</span><span class="sxs-lookup"><span data-stu-id="5bad5-471">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="5bad5-472">Testare l'app.</span><span class="sxs-lookup"><span data-stu-id="5bad5-472">Test the app.</span></span> <span data-ttu-id="5bad5-473">Dal punto di vista degli utenti, l'app si comporta in modo identico alla versione precedente.</span><span class="sxs-lookup"><span data-stu-id="5bad5-473">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="5bad5-474">La prossima esercitazione illustra come aggiornare i dati correlati.</span><span class="sxs-lookup"><span data-stu-id="5bad5-474">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5bad5-475">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="5bad5-475">Additional resources</span></span>

* [<span data-ttu-id="5bad5-476">Versione YouTube dell'esercitazione (parte 1)</span><span class="sxs-lookup"><span data-stu-id="5bad5-476">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="5bad5-477">Versione YouTube dell'esercitazione (parte 2)</span><span class="sxs-lookup"><span data-stu-id="5bad5-477">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="5bad5-478">[Successivo](xref:data/ef-rp/complex-data-model)
>[precedente](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="5bad5-478">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end
