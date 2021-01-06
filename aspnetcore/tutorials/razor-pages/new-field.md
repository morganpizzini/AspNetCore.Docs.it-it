---
title: Parte 7, aggiungere un nuovo campo
author: rick-anderson
description: Parte 7 della serie di esercitazioni sulle Razor pagine.
ms.author: riande
ms.custom: mvc, contperf-fy21q2
ms.date: 09/28/2020
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
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 2961fcaa8bbb93527eb80f5aa7b18ef04761fb50
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "97486161"
---
# <a name="part-7-add-a-new-field-to-a-no-locrazor-page-in-aspnet-core"></a><span data-ttu-id="9286e-103">Parte 7, aggiungere un nuovo campo a una Razor pagina in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9286e-103">Part 7, add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="9286e-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9286e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9286e-105">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9286e-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="9286e-106">In questa sezione vengono usate le Migrazioni Code First di [Entity Framework](/ef/core/get-started/aspnetcore/new-db) per:</span><span class="sxs-lookup"><span data-stu-id="9286e-106">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="9286e-107">Aggiungere un nuovo campo al modello.</span><span class="sxs-lookup"><span data-stu-id="9286e-107">Add a new field to the model.</span></span>
* <span data-ttu-id="9286e-108">Eseguire la migrazione nel database della modifica al nuovo schema del campo.</span><span class="sxs-lookup"><span data-stu-id="9286e-108">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="9286e-109">Quando si usa Code First di Entity Framework per creare automaticamente un database, Code First:</span><span class="sxs-lookup"><span data-stu-id="9286e-109">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="9286e-110">Aggiunge una [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) tabella al database per rilevare se lo schema del database è sincronizzato con le classi del modello da cui è stato generato.</span><span class="sxs-lookup"><span data-stu-id="9286e-110">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="9286e-111">Se le classi del modello non sono sincronizzate con il database, EF genera un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="9286e-111">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="9286e-112">Con la verifica automatica che lo schema e il modello sono sincronizzati, è più semplice individuare problemi di codice di database incoerenti.</span><span class="sxs-lookup"><span data-stu-id="9286e-112">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="9286e-113">Aggiunta di una proprietà Rating al modello Movie</span><span class="sxs-lookup"><span data-stu-id="9286e-113">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="9286e-114">Aprire il file *Models/Movie.cs* e aggiungere una proprietà `Rating`:</span><span class="sxs-lookup"><span data-stu-id="9286e-114">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="9286e-115">Compilare l'app.</span><span class="sxs-lookup"><span data-stu-id="9286e-115">Build the app.</span></span>

1. <span data-ttu-id="9286e-116">Modificare *pages/Movies/ Index . cshtml* e aggiungere un `Rating` campo:</span><span class="sxs-lookup"><span data-stu-id="9286e-116">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="9286e-117">Aggiornare le pagine seguenti:</span><span class="sxs-lookup"><span data-stu-id="9286e-117">Update the following pages:</span></span>
   1. <span data-ttu-id="9286e-118">Aggiungere il campo `Rating` alle pagine Delete (Elimina) e Details (Dettagli).</span><span class="sxs-lookup"><span data-stu-id="9286e-118">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="9286e-119">Aggiornare [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="9286e-119">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="9286e-120">Aggiungere il campo `Rating` alla pagina Edit (Modifica).</span><span class="sxs-lookup"><span data-stu-id="9286e-120">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="9286e-121">L'app non funzionerà finché il database non verrà aggiornato in modo da includere il nuovo campo.</span><span class="sxs-lookup"><span data-stu-id="9286e-121">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="9286e-122">L'esecuzione dell'app senza un aggiornamento del database genera un'eccezione `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="9286e-122">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="9286e-123">L' `SqlException` eccezione è causata dal fatto che la classe del modello di film aggiornata è diversa dallo schema della tabella dei film del database.</span><span class="sxs-lookup"><span data-stu-id="9286e-123">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="9286e-124">Non è presente alcuna `Rating` colonna nella tabella di database.</span><span class="sxs-lookup"><span data-stu-id="9286e-124">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="9286e-125">Per correggere questo errore, esistono alcuni approcci:</span><span class="sxs-lookup"><span data-stu-id="9286e-125">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="9286e-126">Fare in modo che Entity Framework elimini e crei di nuovo automaticamente il database usando il nuovo schema di classi del modello.</span><span class="sxs-lookup"><span data-stu-id="9286e-126">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="9286e-127">Questo approccio è utile nelle prime fasi del ciclo di sviluppo e consente di sviluppare rapidamente lo schema del modello e del database insieme.</span><span class="sxs-lookup"><span data-stu-id="9286e-127">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="9286e-128">Lo svantaggio è che si perdono i dati esistenti nel database.</span><span class="sxs-lookup"><span data-stu-id="9286e-128">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="9286e-129">Non usare questo approccio in un database di produzione.</span><span class="sxs-lookup"><span data-stu-id="9286e-129">Don't use this approach on a production database!</span></span> <span data-ttu-id="9286e-130">L'eliminazione del database in caso di modifiche dello schema e l'utilizzo di un inizializzatore per eseguire automaticamente il seeding del database con dati di test è spesso un modo produttivo per sviluppare un'app.</span><span class="sxs-lookup"><span data-stu-id="9286e-130">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="9286e-131">Modificare esplicitamente lo schema del database esistente in modo che corrisponda alle classi del modello.</span><span class="sxs-lookup"><span data-stu-id="9286e-131">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="9286e-132">Il vantaggio di questo approccio consiste nel preservare i dati.</span><span class="sxs-lookup"><span data-stu-id="9286e-132">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="9286e-133">Apportare questa modifica manualmente o creando uno script di modifica del database.</span><span class="sxs-lookup"><span data-stu-id="9286e-133">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="9286e-134">Usare Migrazioni Code First per aggiornare lo schema del database.</span><span class="sxs-lookup"><span data-stu-id="9286e-134">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="9286e-135">Per questa esercitazione usare Migrazioni Code First.</span><span class="sxs-lookup"><span data-stu-id="9286e-135">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="9286e-136">Aggiornare la classe `SeedData` in modo che fornisca un valore per la nuova colonna.</span><span class="sxs-lookup"><span data-stu-id="9286e-136">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="9286e-137">Di seguito viene illustrata una modifica di esempio, ma questa modifica viene apportata per ogni `new Movie` blocco.</span><span class="sxs-lookup"><span data-stu-id="9286e-137">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="9286e-138">Vedere il [file SeedData.cs completato](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="9286e-138">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="9286e-139">Compilare la soluzione.</span><span class="sxs-lookup"><span data-stu-id="9286e-139">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9286e-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9286e-140">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="9286e-141">Aggiungere una migrazione per il campo Rating</span><span class="sxs-lookup"><span data-stu-id="9286e-141">Add a migration for the rating field</span></span>

1. <span data-ttu-id="9286e-142">Dal menu **Strumenti** selezionare **Gestione pacchetti NuGet > Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="9286e-142">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="9286e-143">Nella Console di Gestione pacchetti immettere i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="9286e-143">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="9286e-144">Il comando `Add-Migration` indica al framework di:</span><span class="sxs-lookup"><span data-stu-id="9286e-144">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="9286e-145">Confrontare il `Movie` modello con lo `Movie` schema del database.</span><span class="sxs-lookup"><span data-stu-id="9286e-145">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="9286e-146">Creare il codice per eseguire la migrazione dello schema del database al nuovo modello.</span><span class="sxs-lookup"><span data-stu-id="9286e-146">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="9286e-147">Il nome "Rating" è arbitrario e viene usato per denominare il file di migrazione.</span><span class="sxs-lookup"><span data-stu-id="9286e-147">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="9286e-148">È consigliabile usare un nome significativo per il file di migrazione.</span><span class="sxs-lookup"><span data-stu-id="9286e-148">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="9286e-149">Il `Update-Database` comando indica al Framework di applicare le modifiche dello schema al database e di mantenere i dati esistenti.</span><span class="sxs-lookup"><span data-stu-id="9286e-149">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="9286e-150">Se si eliminano tutti i record nel database, l'inizializzatore eseguirà il seeding del database e includerà il `Rating` campo.</span><span class="sxs-lookup"><span data-stu-id="9286e-150">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="9286e-151">È possibile eseguire questa operazione con i collegamenti di eliminazione nel browser o da [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="9286e-151">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="9286e-152">Un'altra opzione è quella di eliminare il database e usare le migrazioni per ricreare il database.</span><span class="sxs-lookup"><span data-stu-id="9286e-152">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="9286e-153">Per eliminare il database da SSOX:</span><span class="sxs-lookup"><span data-stu-id="9286e-153">To delete the database in SSOX:</span></span>

1. <span data-ttu-id="9286e-154">Selezionare il database in SSOX.</span><span class="sxs-lookup"><span data-stu-id="9286e-154">Select the database in SSOX.</span></span>
1. <span data-ttu-id="9286e-155">Fare clic con il pulsante destro del mouse sul database e scegliere **Elimina**.</span><span class="sxs-lookup"><span data-stu-id="9286e-155">Right-click on the database, and select **Delete**.</span></span>
1. <span data-ttu-id="9286e-156">Selezionare **Chiudi connessioni esistenti**.</span><span class="sxs-lookup"><span data-stu-id="9286e-156">Check **Close existing connections**.</span></span>
1. <span data-ttu-id="9286e-157">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="9286e-157">Select **OK**.</span></span>
1. <span data-ttu-id="9286e-158">In [PMC](xref:tutorials/razor-pages/new-field#pmc)aggiornare il database:</span><span class="sxs-lookup"><span data-stu-id="9286e-158">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9286e-159">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="9286e-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="9286e-160">Eliminare e ricreare il database</span><span class="sxs-lookup"><span data-stu-id="9286e-160">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="9286e-161">Per questa esercitazione, è possibile usare la funzionalità *migrazioni* di Entity Framework Core, ove possibile.</span><span class="sxs-lookup"><span data-stu-id="9286e-161">For this tutorial, you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="9286e-162">Le migrazioni aggiornano lo schema del database in base alle modifiche nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="9286e-162">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="9286e-163">Tuttavia, le migrazioni possono eseguire solo i tipi di modifiche supportate dal provider EF Core e le funzionalità del provider SQLite sono limitate.</span><span class="sxs-lookup"><span data-stu-id="9286e-163">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="9286e-164">Ad esempio l'aggiunta di una colonna è supportata, ma la rimozione o la modifica di una colonna non è supportata.</span><span class="sxs-lookup"><span data-stu-id="9286e-164">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="9286e-165">Se si crea una migrazione per rimuovere o modificare una colonna, il comando `ef migrations add` ha esito positivo, ma il comando `ef database update` ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="9286e-165">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="9286e-166">A causa di queste limitazioni, in questa esercitazione non vengono usate le migrazioni per le modifiche dello schema di SQLite.</span><span class="sxs-lookup"><span data-stu-id="9286e-166">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="9286e-167">In caso di modifiche dello schema, il database viene invece eliminato e ricreato.</span><span class="sxs-lookup"><span data-stu-id="9286e-167">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="9286e-168">La soluzione per ovviare alle limitazioni di SQLite consiste nello scrivere manualmente il codice delle migrazioni per eseguire una ricompilazione della tabella in caso di modifiche nella tabella.</span><span class="sxs-lookup"><span data-stu-id="9286e-168">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="9286e-169">La ricompilazione della tabella comporta:</span><span class="sxs-lookup"><span data-stu-id="9286e-169">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="9286e-170">Creazione di una nuova tabella.</span><span class="sxs-lookup"><span data-stu-id="9286e-170">Creating a new table.</span></span>
>* <span data-ttu-id="9286e-171">Copia dei dati dalla vecchia tabella alla nuova tabella.</span><span class="sxs-lookup"><span data-stu-id="9286e-171">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="9286e-172">Eliminazione della tabella precedente.</span><span class="sxs-lookup"><span data-stu-id="9286e-172">Dropping the old table.</span></span>
>* <span data-ttu-id="9286e-173">Ridenominazione della nuova tabella.</span><span class="sxs-lookup"><span data-stu-id="9286e-173">Renaming the new table.</span></span>
>
><span data-ttu-id="9286e-174">Per altre informazioni, vedere le risorse seguenti:</span><span class="sxs-lookup"><span data-stu-id="9286e-174">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="9286e-175">Limitazioni del provider di database SQLite per EF Core</span><span class="sxs-lookup"><span data-stu-id="9286e-175">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="9286e-176">Personalizzare il codice di migrazione</span><span class="sxs-lookup"><span data-stu-id="9286e-176">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="9286e-177">Seeding dei dati</span><span class="sxs-lookup"><span data-stu-id="9286e-177">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="9286e-178">Istruzione ALTER TABLE di SQLite</span><span class="sxs-lookup"><span data-stu-id="9286e-178">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="9286e-179">Eliminare la cartella della migrazione.</span><span class="sxs-lookup"><span data-stu-id="9286e-179">Delete the migration folder.</span></span>  

1. <span data-ttu-id="9286e-180">Usare i comandi seguenti per ricreare il database.</span><span class="sxs-lookup"><span data-stu-id="9286e-180">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="9286e-181">Eseguire l'app e verificare che sia possibile creare/modificare/visualizzare i film con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="9286e-181">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="9286e-182">Se il database non è inizializzato, impostare un punto di interruzione nel metodo `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="9286e-182">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9286e-183">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="9286e-183">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="9286e-184">[Precedente: Aggiungi ricerca](xref:tutorials/razor-pages/search) 
>  Passaggio [successivo: aggiungere la convalida](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="9286e-184">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="9286e-185">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9286e-185">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="9286e-186">In questa sezione vengono usate le Migrazioni Code First di [Entity Framework](/ef/core/get-started/aspnetcore/new-db) per:</span><span class="sxs-lookup"><span data-stu-id="9286e-186">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="9286e-187">Aggiungere un nuovo campo al modello.</span><span class="sxs-lookup"><span data-stu-id="9286e-187">Add a new field to the model.</span></span>
* <span data-ttu-id="9286e-188">Eseguire la migrazione nel database della modifica al nuovo schema del campo.</span><span class="sxs-lookup"><span data-stu-id="9286e-188">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="9286e-189">Quando si usa Code First di Entity Framework per creare automaticamente un database, Code First:</span><span class="sxs-lookup"><span data-stu-id="9286e-189">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="9286e-190">Aggiunge una [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) tabella al database per rilevare se lo schema del database è sincronizzato con le classi del modello da cui è stato generato.</span><span class="sxs-lookup"><span data-stu-id="9286e-190">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="9286e-191">Se le classi del modello non sono sincronizzate con il database, EF genera un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="9286e-191">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="9286e-192">Con la verifica automatica che lo schema e il modello sono sincronizzati, è più semplice individuare problemi di codice di database incoerenti.</span><span class="sxs-lookup"><span data-stu-id="9286e-192">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="9286e-193">Aggiunta di una proprietà Rating al modello Movie</span><span class="sxs-lookup"><span data-stu-id="9286e-193">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="9286e-194">Aprire il file *Models/Movie.cs* e aggiungere una proprietà `Rating`:</span><span class="sxs-lookup"><span data-stu-id="9286e-194">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="9286e-195">Compilare l'app.</span><span class="sxs-lookup"><span data-stu-id="9286e-195">Build the app.</span></span>

1. <span data-ttu-id="9286e-196">Modificare *pages/Movies/ Index . cshtml* e aggiungere un `Rating` campo:</span><span class="sxs-lookup"><span data-stu-id="9286e-196">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="9286e-197">Aggiornare le pagine seguenti:</span><span class="sxs-lookup"><span data-stu-id="9286e-197">Update the following pages:</span></span>
   1. <span data-ttu-id="9286e-198">Aggiungere il campo `Rating` alle pagine Delete (Elimina) e Details (Dettagli).</span><span class="sxs-lookup"><span data-stu-id="9286e-198">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="9286e-199">Aggiornare [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="9286e-199">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="9286e-200">Aggiungere il campo `Rating` alla pagina Edit (Modifica).</span><span class="sxs-lookup"><span data-stu-id="9286e-200">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="9286e-201">L'app non funzionerà finché il database non verrà aggiornato in modo da includere il nuovo campo.</span><span class="sxs-lookup"><span data-stu-id="9286e-201">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="9286e-202">L'esecuzione dell'app senza un aggiornamento del database genera un'eccezione `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="9286e-202">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="9286e-203">L' `SqlException` eccezione è causata dal fatto che la classe del modello di film aggiornata è diversa dallo schema della tabella dei film del database.</span><span class="sxs-lookup"><span data-stu-id="9286e-203">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="9286e-204">Non è presente alcuna `Rating` colonna nella tabella di database.</span><span class="sxs-lookup"><span data-stu-id="9286e-204">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="9286e-205">Per correggere questo errore, esistono alcuni approcci:</span><span class="sxs-lookup"><span data-stu-id="9286e-205">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="9286e-206">Fare in modo che Entity Framework elimini e crei di nuovo automaticamente il database usando il nuovo schema di classi del modello.</span><span class="sxs-lookup"><span data-stu-id="9286e-206">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="9286e-207">Questo approccio è utile nelle prime fasi del ciclo di sviluppo e consente di sviluppare rapidamente lo schema del modello e del database insieme.</span><span class="sxs-lookup"><span data-stu-id="9286e-207">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="9286e-208">Lo svantaggio è che si perdono i dati esistenti nel database.</span><span class="sxs-lookup"><span data-stu-id="9286e-208">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="9286e-209">Non usare questo approccio in un database di produzione.</span><span class="sxs-lookup"><span data-stu-id="9286e-209">Don't use this approach on a production database!</span></span> <span data-ttu-id="9286e-210">L'eliminazione del database in caso di modifiche dello schema e l'utilizzo di un inizializzatore per eseguire automaticamente il seeding del database con dati di test è spesso un modo produttivo per sviluppare un'app.</span><span class="sxs-lookup"><span data-stu-id="9286e-210">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="9286e-211">Modificare esplicitamente lo schema del database esistente in modo che corrisponda alle classi del modello.</span><span class="sxs-lookup"><span data-stu-id="9286e-211">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="9286e-212">Il vantaggio di questo approccio consiste nel preservare i dati.</span><span class="sxs-lookup"><span data-stu-id="9286e-212">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="9286e-213">Apportare questa modifica manualmente o creando uno script di modifica del database.</span><span class="sxs-lookup"><span data-stu-id="9286e-213">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="9286e-214">Usare Migrazioni Code First per aggiornare lo schema del database.</span><span class="sxs-lookup"><span data-stu-id="9286e-214">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="9286e-215">Per questa esercitazione usare Migrazioni Code First.</span><span class="sxs-lookup"><span data-stu-id="9286e-215">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="9286e-216">Aggiornare la classe `SeedData` in modo che fornisca un valore per la nuova colonna.</span><span class="sxs-lookup"><span data-stu-id="9286e-216">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="9286e-217">Di seguito viene illustrata una modifica di esempio, ma questa modifica viene apportata per ogni `new Movie` blocco.</span><span class="sxs-lookup"><span data-stu-id="9286e-217">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="9286e-218">Vedere il [file SeedData.cs completato](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="9286e-218">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="9286e-219">Compilare la soluzione.</span><span class="sxs-lookup"><span data-stu-id="9286e-219">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9286e-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9286e-220">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="9286e-221">Aggiungere una migrazione per il campo Rating</span><span class="sxs-lookup"><span data-stu-id="9286e-221">Add a migration for the rating field</span></span>

1. <span data-ttu-id="9286e-222">Dal menu **Strumenti** selezionare **Gestione pacchetti NuGet > Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="9286e-222">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="9286e-223">Nella Console di Gestione pacchetti immettere i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="9286e-223">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="9286e-224">Il comando `Add-Migration` indica al framework di:</span><span class="sxs-lookup"><span data-stu-id="9286e-224">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="9286e-225">Confrontare il `Movie` modello con lo `Movie` schema del database.</span><span class="sxs-lookup"><span data-stu-id="9286e-225">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="9286e-226">Creare il codice per eseguire la migrazione dello schema del database al nuovo modello.</span><span class="sxs-lookup"><span data-stu-id="9286e-226">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="9286e-227">Il nome "Rating" è arbitrario e viene usato per denominare il file di migrazione.</span><span class="sxs-lookup"><span data-stu-id="9286e-227">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="9286e-228">È consigliabile usare un nome significativo per il file di migrazione.</span><span class="sxs-lookup"><span data-stu-id="9286e-228">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="9286e-229">Il `Update-Database` comando indica al Framework di applicare le modifiche dello schema al database e di mantenere i dati esistenti.</span><span class="sxs-lookup"><span data-stu-id="9286e-229">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="9286e-230">Se si eliminano tutti i record nel database, l'inizializzatore eseguirà il seeding del database e includerà il `Rating` campo.</span><span class="sxs-lookup"><span data-stu-id="9286e-230">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="9286e-231">È possibile eseguire questa operazione con i collegamenti di eliminazione nel browser o da [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="9286e-231">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="9286e-232">Un'altra opzione è quella di eliminare il database e usare le migrazioni per ricreare il database.</span><span class="sxs-lookup"><span data-stu-id="9286e-232">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="9286e-233">Per eliminare il database da SSOX:</span><span class="sxs-lookup"><span data-stu-id="9286e-233">To delete the database in SSOX:</span></span>

* <span data-ttu-id="9286e-234">Selezionare il database in SSOX.</span><span class="sxs-lookup"><span data-stu-id="9286e-234">Select the database in SSOX.</span></span>
* <span data-ttu-id="9286e-235">Fare clic con il pulsante destro del mouse sul database e scegliere **Elimina**.</span><span class="sxs-lookup"><span data-stu-id="9286e-235">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="9286e-236">Selezionare **Chiudi connessioni esistenti**.</span><span class="sxs-lookup"><span data-stu-id="9286e-236">Check **Close existing connections**.</span></span>
* <span data-ttu-id="9286e-237">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="9286e-237">Select **OK**.</span></span>
* <span data-ttu-id="9286e-238">In [PMC](xref:tutorials/razor-pages/new-field#pmc)aggiornare il database:</span><span class="sxs-lookup"><span data-stu-id="9286e-238">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9286e-239">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="9286e-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="9286e-240">Eliminare e ricreare il database</span><span class="sxs-lookup"><span data-stu-id="9286e-240">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="9286e-241">Per questa esercitazione, usare la funzionalità *migrazioni* di Entity Framework Core laddove possibile.</span><span class="sxs-lookup"><span data-stu-id="9286e-241">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="9286e-242">Le migrazioni aggiornano lo schema del database in base alle modifiche nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="9286e-242">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="9286e-243">Tuttavia, le migrazioni possono eseguire solo i tipi di modifiche supportate dal provider EF Core e le funzionalità del provider SQLite sono limitate.</span><span class="sxs-lookup"><span data-stu-id="9286e-243">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="9286e-244">Ad esempio l'aggiunta di una colonna è supportata, ma la rimozione o la modifica di una colonna non è supportata.</span><span class="sxs-lookup"><span data-stu-id="9286e-244">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="9286e-245">Se si crea una migrazione per rimuovere o modificare una colonna, il comando `ef migrations add` ha esito positivo, ma il comando `ef database update` ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="9286e-245">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="9286e-246">A causa di queste limitazioni, in questa esercitazione non vengono usate le migrazioni per le modifiche dello schema di SQLite.</span><span class="sxs-lookup"><span data-stu-id="9286e-246">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="9286e-247">In caso di modifiche dello schema, il database viene invece eliminato e ricreato.</span><span class="sxs-lookup"><span data-stu-id="9286e-247">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="9286e-248">La soluzione per ovviare alle limitazioni di SQLite consiste nello scrivere manualmente il codice delle migrazioni per eseguire una ricompilazione della tabella in caso di modifiche nella tabella.</span><span class="sxs-lookup"><span data-stu-id="9286e-248">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="9286e-249">La ricompilazione della tabella comporta:</span><span class="sxs-lookup"><span data-stu-id="9286e-249">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="9286e-250">Creazione di una nuova tabella.</span><span class="sxs-lookup"><span data-stu-id="9286e-250">Creating a new table.</span></span>
>* <span data-ttu-id="9286e-251">Copia dei dati dalla vecchia tabella alla nuova tabella.</span><span class="sxs-lookup"><span data-stu-id="9286e-251">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="9286e-252">Eliminazione della tabella precedente.</span><span class="sxs-lookup"><span data-stu-id="9286e-252">Dropping the old table.</span></span>
>* <span data-ttu-id="9286e-253">Ridenominazione della nuova tabella.</span><span class="sxs-lookup"><span data-stu-id="9286e-253">Renaming the new table.</span></span>
>
><span data-ttu-id="9286e-254">Per altre informazioni, vedere le risorse seguenti:</span><span class="sxs-lookup"><span data-stu-id="9286e-254">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="9286e-255">Limitazioni del provider di database SQLite per EF Core</span><span class="sxs-lookup"><span data-stu-id="9286e-255">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="9286e-256">Personalizzare il codice di migrazione</span><span class="sxs-lookup"><span data-stu-id="9286e-256">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="9286e-257">Seeding dei dati</span><span class="sxs-lookup"><span data-stu-id="9286e-257">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="9286e-258">Istruzione ALTER TABLE di SQLite</span><span class="sxs-lookup"><span data-stu-id="9286e-258">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="9286e-259">Eliminare la cartella della migrazione.</span><span class="sxs-lookup"><span data-stu-id="9286e-259">Delete the migration folder.</span></span>  

1. <span data-ttu-id="9286e-260">Usare i comandi seguenti per ricreare il database.</span><span class="sxs-lookup"><span data-stu-id="9286e-260">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="9286e-261">Eseguire l'app e verificare che sia possibile creare/modificare/visualizzare i film con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="9286e-261">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="9286e-262">Se il database non è inizializzato, impostare un punto di interruzione nel metodo `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="9286e-262">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9286e-263">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="9286e-263">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="9286e-264">[Precedente: Aggiungi ricerca](xref:tutorials/razor-pages/search) 
>  Passaggio [successivo: aggiungere la convalida](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="9286e-264">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9286e-265">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9286e-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="9286e-266">In questa sezione vengono usate le Migrazioni Code First di [Entity Framework](/ef/core/get-started/aspnetcore/new-db) per:</span><span class="sxs-lookup"><span data-stu-id="9286e-266">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="9286e-267">Aggiungere un nuovo campo al modello.</span><span class="sxs-lookup"><span data-stu-id="9286e-267">Add a new field to the model.</span></span>
* <span data-ttu-id="9286e-268">Eseguire la migrazione nel database della modifica al nuovo schema del campo.</span><span class="sxs-lookup"><span data-stu-id="9286e-268">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="9286e-269">Quando si usa Code First di Entity Framework per creare automaticamente un database, Code First:</span><span class="sxs-lookup"><span data-stu-id="9286e-269">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="9286e-270">Aggiunge una [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) tabella al database per rilevare se lo schema del database è sincronizzato con le classi del modello da cui è stato generato.</span><span class="sxs-lookup"><span data-stu-id="9286e-270">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="9286e-271">Se le classi del modello non sono sincronizzate con il database, EF genera un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="9286e-271">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="9286e-272">Con la verifica automatica che lo schema e il modello sono sincronizzati, è più semplice individuare problemi di codice di database incoerenti.</span><span class="sxs-lookup"><span data-stu-id="9286e-272">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="9286e-273">Aggiunta di una proprietà Rating al modello Movie</span><span class="sxs-lookup"><span data-stu-id="9286e-273">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="9286e-274">Aprire il file *Models/Movie.cs* e aggiungere una proprietà `Rating`:</span><span class="sxs-lookup"><span data-stu-id="9286e-274">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="9286e-275">Compilare l'app.</span><span class="sxs-lookup"><span data-stu-id="9286e-275">Build the app.</span></span>

<span data-ttu-id="9286e-276">Modificare *pages/Movies/ Index . cshtml* e aggiungere un `Rating` campo:</span><span class="sxs-lookup"><span data-stu-id="9286e-276">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="9286e-277">Aggiornare le pagine seguenti:</span><span class="sxs-lookup"><span data-stu-id="9286e-277">Update the following pages:</span></span>

* <span data-ttu-id="9286e-278">Aggiungere il campo `Rating` alle pagine Delete (Elimina) e Details (Dettagli).</span><span class="sxs-lookup"><span data-stu-id="9286e-278">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="9286e-279">Aggiornare [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="9286e-279">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="9286e-280">Aggiungere il campo `Rating` alla pagina Edit (Modifica).</span><span class="sxs-lookup"><span data-stu-id="9286e-280">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="9286e-281">L'app non funzionerà finché il database non verrà aggiornato in modo da includere il nuovo campo.</span><span class="sxs-lookup"><span data-stu-id="9286e-281">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="9286e-282">Se l'app viene eseguita adesso, l'app genera un'eccezione `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="9286e-282">If the app is run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="9286e-283">Questo errore viene visualizzato perché la classe del modello Movie aggiornata è diversa rispetto allo schema della tabella Movie nel database.</span><span class="sxs-lookup"><span data-stu-id="9286e-283">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="9286e-284">Non è presente alcuna `Rating` colonna nella tabella di database.</span><span class="sxs-lookup"><span data-stu-id="9286e-284">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="9286e-285">Per correggere questo errore, esistono alcuni approcci:</span><span class="sxs-lookup"><span data-stu-id="9286e-285">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="9286e-286">Fare in modo che Entity Framework elimini e crei di nuovo automaticamente il database usando il nuovo schema di classi del modello.</span><span class="sxs-lookup"><span data-stu-id="9286e-286">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="9286e-287">Questo approccio è utile nelle prime fasi del ciclo di sviluppo e consente di sviluppare rapidamente lo schema del modello e del database insieme.</span><span class="sxs-lookup"><span data-stu-id="9286e-287">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="9286e-288">Lo svantaggio è che si perdono i dati esistenti nel database.</span><span class="sxs-lookup"><span data-stu-id="9286e-288">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="9286e-289">Non usare questo approccio in un database di produzione.</span><span class="sxs-lookup"><span data-stu-id="9286e-289">Don't use this approach on a production database!</span></span> <span data-ttu-id="9286e-290">L'eliminazione del database in caso di modifiche dello schema e l'utilizzo di un inizializzatore per eseguire automaticamente il seeding del database con dati di test è spesso un modo produttivo per sviluppare un'app.</span><span class="sxs-lookup"><span data-stu-id="9286e-290">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="9286e-291">Modificare esplicitamente lo schema del database esistente in modo che corrisponda alle classi del modello.</span><span class="sxs-lookup"><span data-stu-id="9286e-291">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="9286e-292">Il vantaggio di questo approccio consiste nel preservare i dati.</span><span class="sxs-lookup"><span data-stu-id="9286e-292">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="9286e-293">Apportare questa modifica manualmente o creando uno script di modifica del database.</span><span class="sxs-lookup"><span data-stu-id="9286e-293">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="9286e-294">Usare Migrazioni Code First per aggiornare lo schema del database.</span><span class="sxs-lookup"><span data-stu-id="9286e-294">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="9286e-295">Per questa esercitazione usare Migrazioni Code First.</span><span class="sxs-lookup"><span data-stu-id="9286e-295">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="9286e-296">Aggiornare la classe `SeedData` in modo che fornisca un valore per la nuova colonna.</span><span class="sxs-lookup"><span data-stu-id="9286e-296">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="9286e-297">Di seguito viene illustrata una modifica di esempio, ma questa modifica viene apportata per ogni `new Movie` blocco.</span><span class="sxs-lookup"><span data-stu-id="9286e-297">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="9286e-298">Vedere il [file SeedData.cs completato](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="9286e-298">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="9286e-299">Compilare la soluzione.</span><span class="sxs-lookup"><span data-stu-id="9286e-299">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9286e-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9286e-300">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="9286e-301">Aggiungere una migrazione per il campo Rating</span><span class="sxs-lookup"><span data-stu-id="9286e-301">Add a migration for the rating field</span></span>

<span data-ttu-id="9286e-302">Dal menu **Strumenti** selezionare **Gestione pacchetti NuGet > Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="9286e-302">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="9286e-303">Nella Console di Gestione pacchetti immettere i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="9286e-303">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="9286e-304">Il comando `Add-Migration` indica al framework di:</span><span class="sxs-lookup"><span data-stu-id="9286e-304">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="9286e-305">Confrontare il `Movie` modello con lo `Movie` schema del database.</span><span class="sxs-lookup"><span data-stu-id="9286e-305">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="9286e-306">Creare il codice per eseguire la migrazione dello schema del database al nuovo modello.</span><span class="sxs-lookup"><span data-stu-id="9286e-306">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="9286e-307">Il nome "Rating" è arbitrario e viene usato per denominare il file di migrazione.</span><span class="sxs-lookup"><span data-stu-id="9286e-307">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="9286e-308">È consigliabile usare un nome significativo per il file di migrazione.</span><span class="sxs-lookup"><span data-stu-id="9286e-308">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="9286e-309">Il comando `Update-Database` indica al framework di applicare le modifiche dello schema al database.</span><span class="sxs-lookup"><span data-stu-id="9286e-309">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="9286e-310">Se si eliminano tutti i record nel database, l'inizializzatore eseguirà il seeding del database e includerà il `Rating` campo.</span><span class="sxs-lookup"><span data-stu-id="9286e-310">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="9286e-311">È possibile eseguire questa operazione con i collegamenti di eliminazione nel browser o da [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="9286e-311">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="9286e-312">Un'altra opzione è quella di eliminare il database e usare le migrazioni per ricreare il database.</span><span class="sxs-lookup"><span data-stu-id="9286e-312">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="9286e-313">Per eliminare il database da SSOX:</span><span class="sxs-lookup"><span data-stu-id="9286e-313">To delete the database in SSOX:</span></span>

* <span data-ttu-id="9286e-314">Selezionare il database in SSOX.</span><span class="sxs-lookup"><span data-stu-id="9286e-314">Select the database in SSOX.</span></span>
* <span data-ttu-id="9286e-315">Fare clic con il pulsante destro del mouse sul database e scegliere **Elimina**.</span><span class="sxs-lookup"><span data-stu-id="9286e-315">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="9286e-316">Selezionare **Chiudi connessioni esistenti**.</span><span class="sxs-lookup"><span data-stu-id="9286e-316">Check **Close existing connections**.</span></span>
* <span data-ttu-id="9286e-317">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="9286e-317">Select **OK**.</span></span>
* <span data-ttu-id="9286e-318">In [PMC](xref:tutorials/razor-pages/new-field#pmc)aggiornare il database:</span><span class="sxs-lookup"><span data-stu-id="9286e-318">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9286e-319">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="9286e-319">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="9286e-320">Eliminare e ricreare il database</span><span class="sxs-lookup"><span data-stu-id="9286e-320">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="9286e-321">Per questa esercitazione, usare la funzionalità *migrazioni* di Entity Framework Core laddove possibile.</span><span class="sxs-lookup"><span data-stu-id="9286e-321">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="9286e-322">Le migrazioni aggiornano lo schema del database in base alle modifiche nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="9286e-322">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="9286e-323">Tuttavia, le migrazioni possono eseguire solo i tipi di modifiche supportate dal provider EF Core e le funzionalità del provider SQLite sono limitate.</span><span class="sxs-lookup"><span data-stu-id="9286e-323">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="9286e-324">Ad esempio l'aggiunta di una colonna è supportata, ma la rimozione o la modifica di una colonna non è supportata.</span><span class="sxs-lookup"><span data-stu-id="9286e-324">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="9286e-325">Se si crea una migrazione per rimuovere o modificare una colonna, il comando `ef migrations add` ha esito positivo, ma il comando `ef database update` ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="9286e-325">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="9286e-326">A causa di queste limitazioni, in questa esercitazione non vengono usate le migrazioni per le modifiche dello schema di SQLite.</span><span class="sxs-lookup"><span data-stu-id="9286e-326">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="9286e-327">In caso di modifiche dello schema, il database viene invece eliminato e ricreato.</span><span class="sxs-lookup"><span data-stu-id="9286e-327">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="9286e-328">La soluzione per ovviare alle limitazioni di SQLite consiste nello scrivere manualmente il codice delle migrazioni per eseguire una ricompilazione della tabella in caso di modifiche nella tabella.</span><span class="sxs-lookup"><span data-stu-id="9286e-328">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="9286e-329">La ricompilazione della tabella comporta:</span><span class="sxs-lookup"><span data-stu-id="9286e-329">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="9286e-330">Creazione di una nuova tabella.</span><span class="sxs-lookup"><span data-stu-id="9286e-330">Creating a new table.</span></span>
>* <span data-ttu-id="9286e-331">Copia dei dati dalla vecchia tabella alla nuova tabella.</span><span class="sxs-lookup"><span data-stu-id="9286e-331">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="9286e-332">Eliminazione della tabella precedente.</span><span class="sxs-lookup"><span data-stu-id="9286e-332">Dropping the old table.</span></span>
>* <span data-ttu-id="9286e-333">Ridenominazione della nuova tabella.</span><span class="sxs-lookup"><span data-stu-id="9286e-333">Renaming the new table.</span></span>
>
><span data-ttu-id="9286e-334">Per altre informazioni, vedere le risorse seguenti:</span><span class="sxs-lookup"><span data-stu-id="9286e-334">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="9286e-335">Limitazioni del provider di database SQLite per EF Core</span><span class="sxs-lookup"><span data-stu-id="9286e-335">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="9286e-336">Personalizzare il codice di migrazione</span><span class="sxs-lookup"><span data-stu-id="9286e-336">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="9286e-337">Seeding dei dati</span><span class="sxs-lookup"><span data-stu-id="9286e-337">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="9286e-338">Istruzione ALTER TABLE di SQLite</span><span class="sxs-lookup"><span data-stu-id="9286e-338">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

<span data-ttu-id="9286e-339">Eliminare il database e usare le migrazioni per ricreare il database.</span><span class="sxs-lookup"><span data-stu-id="9286e-339">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="9286e-340">Per eliminare il database, eliminare il file di database (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="9286e-340">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="9286e-341">Eseguire quindi il comando `ef database update`:</span><span class="sxs-lookup"><span data-stu-id="9286e-341">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="9286e-342">Eseguire l'app e verificare che sia possibile creare/modificare/visualizzare i film con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="9286e-342">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="9286e-343">Se il database non è inizializzato, impostare un punto di interruzione nel metodo `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="9286e-343">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9286e-344">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="9286e-344">Additional resources</span></span>

* [<span data-ttu-id="9286e-345">Versione YouTube dell'esercitazione</span><span class="sxs-lookup"><span data-stu-id="9286e-345">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="9286e-346">[Precedente: Aggiungi ricerca](xref:tutorials/razor-pages/search) 
>  Passaggio [successivo: aggiungere la convalida](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="9286e-346">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end
