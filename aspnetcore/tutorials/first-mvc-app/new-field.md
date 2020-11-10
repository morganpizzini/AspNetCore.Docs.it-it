---
title: "Parte 8: aggiungere un nuovo campo a un'app MVC ASP.NET Core"
author: rick-anderson
description: Parte 8 della serie di esercitazioni su ASP.NET Core MVC.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: 84e344aabc9171020c0117d55eaf1a95e6b768db
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422535"
---
# <a name="part-8-add-a-new-field-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="4f0b6-103">Parte 8: aggiungere un nuovo campo a un'app MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4f0b6-103">Part 8, add a new field to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="4f0b6-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4f0b6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4f0b6-105">In questa sezione vengono usate le Migrazioni Code First di [Entity Framework](/ef/core/get-started/aspnetcore/new-db) per:</span><span class="sxs-lookup"><span data-stu-id="4f0b6-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="4f0b6-106">Aggiungere un nuovo campo al modello.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-106">Add a new field to the model.</span></span>
* <span data-ttu-id="4f0b6-107">Eseguire la migrazione del nuovo campo al database.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-107">Migrate the new field to the database.</span></span>

<span data-ttu-id="4f0b6-108">Quando si usa EF Code First per creare automaticamente un database, Code First:</span><span class="sxs-lookup"><span data-stu-id="4f0b6-108">When EF Code First is used to automatically create a database, Code First:</span></span>

* <span data-ttu-id="4f0b6-109">Aggiunge una tabella al database per rilevare lo schema del database.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-109">Adds a table to the database to  track the schema of the database.</span></span>
* <span data-ttu-id="4f0b6-110">Verifica che il database sia sincronizzato con le classi del modello dalle quali è stato generato.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-110">Verifies the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="4f0b6-111">Se questi elementi non sono sincronizzati, Entity Framework genera un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-111">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="4f0b6-112">In questo modo è più semplice individuare problemi di incoerenza nel database o nel codice.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-112">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="add-a-rating-property-to-the-movie-model"></a><span data-ttu-id="4f0b6-113">Aggiungere una proprietà Rating al modello Movie</span><span class="sxs-lookup"><span data-stu-id="4f0b6-113">Add a Rating Property to the Movie Model</span></span>

<span data-ttu-id="4f0b6-114">Aggiungere una proprietà `Rating` a *Models/Movie.cs* :</span><span class="sxs-lookup"><span data-stu-id="4f0b6-114">Add a `Rating` property to *Models/Movie.cs* :</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?name=snippet)]

<span data-ttu-id="4f0b6-115">Compilare l'app</span><span class="sxs-lookup"><span data-stu-id="4f0b6-115">Build the app</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="4f0b6-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f0b6-116">Visual Studio</span></span>](#tab/visual-studio)

 <span data-ttu-id="4f0b6-117">CTRL+MAIUSC+B</span><span class="sxs-lookup"><span data-stu-id="4f0b6-117">Ctrl+Shift+B</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="4f0b6-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4f0b6-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4f0b6-119">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="4f0b6-119">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4f0b6-120">Comando ⌘ + B</span><span class="sxs-lookup"><span data-stu-id="4f0b6-120">Command ⌘ + B</span></span>

------

<span data-ttu-id="4f0b6-121">Poiché è stato aggiunto un nuovo campo alla `Movie` classe, è necessario aggiornare l'elenco di associazioni di proprietà in modo che la nuova proprietà venga inclusa.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-121">Because you've added a new field to the `Movie` class, you need to update the property binding list so this new property will be included.</span></span> <span data-ttu-id="4f0b6-122">In *MoviesController.cs* aggiornare l'attributo `[Bind]` per i metodi di azione `Create` e `Edit` in modo da includere la proprietà `Rating`:</span><span class="sxs-lookup"><span data-stu-id="4f0b6-122">In *MoviesController.cs* , update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="4f0b6-123">Aggiornare i modelli di vista per visualizzare, creare e modificare la nuova proprietà `Rating` nella vista del browser.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-123">Update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="4f0b6-124">Modificare il file */Views/Movies/Index.cshtml* e aggiungere un campo `Rating`:</span><span class="sxs-lookup"><span data-stu-id="4f0b6-124">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Views/Movies/Index.cshtml?highlight=28-49&range=12-51)]

::: moniker-end

<span data-ttu-id="4f0b6-125">Aggiornare */Views/Movies/Create.cshtml* con un campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-125">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

# <a name="visual-studio--visual-studio-for-mac"></a>[<span data-ttu-id="4f0b6-126">Visual Studio/Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="4f0b6-126">Visual Studio / Visual Studio for Mac</span></span>](#tab/visual-studio+visual-studio-mac)

<span data-ttu-id="4f0b6-127">È possibile copiare e incollare il "gruppo di moduli" precedente e aggiornare i campi usando intelliSense.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-127">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="4f0b6-128">IntelliSense funziona con gli [helper tag](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="4f0b6-128">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

![Lo sviluppatore ha digitato la lettera R per il valore dell'attributo asp-for nel secondo elemento di etichetta della vista.](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="4f0b6-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4f0b6-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

<span data-ttu-id="4f0b6-133">Aggiornare i modelli rimanenti.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-133">Update the remaining templates.</span></span>

<span data-ttu-id="4f0b6-134">Aggiornare la classe `SeedData` in modo che fornisca un valore per la nuova colonna.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-134">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="4f0b6-135">Di seguito viene illustrata una modifica di esempio, ma si apporterà questa modifica per ogni `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-135">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="4f0b6-136">L'app non funzionerà finché non si aggiorna il database in modo da includere il nuovo campo.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-136">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="4f0b6-137">Se viene eseguita a questo punto, viene generato il seguente errore `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="4f0b6-137">If it's run now, the following `SqlException` is thrown:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="4f0b6-138">Questo errore si verifica perché la classe del modello Movie aggiornata è diversa dallo schema della tabella Movie nel database esistente.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-138">This error occurs because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="4f0b6-139">Nella tabella del database non è presente una colonna `Rating`.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-139">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="4f0b6-140">Per correggere questo errore, esistono alcuni approcci:</span><span class="sxs-lookup"><span data-stu-id="4f0b6-140">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="4f0b6-141">Fare in modo che Entity Framework elimini e crei di nuovo automaticamente il database in base al nuovo schema di classi del modello.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-141">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="4f0b6-142">Questo approccio è molto utile nelle prime fasi del ciclo di sviluppo in una fase attiva dello sviluppo di un database di test e consente di migliorare rapidamente lo schema del modello e il database insieme.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-142">This approach is very convenient early in the development cycle when you're doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="4f0b6-143">Lo svantaggio è che si perdono i dati esistenti nel database e non è quindi possibile usare questo approccio in un database di produzione.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-143">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="4f0b6-144">Un modo efficace per sviluppare un'applicazione consiste nell'inizializzare automaticamente un database con dati di test usando un inizializzatore.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-144">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="4f0b6-145">Questo è un approccio ottimale per le fasi iniziali dello sviluppo e quando si usa SQLite.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-145">This is a good approach for early development and when using SQLite.</span></span>

2. <span data-ttu-id="4f0b6-146">Modificare esplicitamente lo schema del database esistente in modo che corrisponda alle classi del modello.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-146">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="4f0b6-147">Il vantaggio di questo approccio è che i dati vengono mantenuti.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-147">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="4f0b6-148">È possibile apportare questa modifica manualmente o creando uno script di modifica del database.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-148">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="4f0b6-149">Usare Migrazioni Code First per aggiornare lo schema del database.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-149">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="4f0b6-150">Per questa esercitazione viene usato Migrazioni Code First.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-150">For this tutorial, Code First Migrations is used.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f0b6-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f0b6-151">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4f0b6-152">Dal menu **Strumenti** selezionare **Gestione pacchetti NuGet > Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-152">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![Menu della Console di Gestione pacchetti](adding-model/_static/pmc.png)

<span data-ttu-id="4f0b6-154">Nella Console di Gestione pacchetti immettere i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="4f0b6-154">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="4f0b6-155">Il comando `Add-Migration` indica al framework di migrazione di esaminare il modello `Movie` corrente con lo schema di database `Movie` corrente e creare il codice necessario per migrare il database nel nuovo modello.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-155">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span>

<span data-ttu-id="4f0b6-156">Il nome "Rating" è arbitrario e viene usato per denominare il file di migrazione.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-156">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="4f0b6-157">È consigliabile usare un nome significativo per il file di migrazione.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-157">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="4f0b6-158">Se vengono eliminati tutti i record del database, il database viene inizializzato dal metodo di inizializzazione e viene incluso il campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-158">If all the records in the DB are deleted, the initialize method will seed the DB and include the `Rating` field.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4f0b6-159">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="4f0b6-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="4f0b6-160">Eliminare il database e la migrazione precedente e utilizzare le migrazioni per ricreare il database:</span><span class="sxs-lookup"><span data-stu-id="4f0b6-160">Delete the database and the previous migration and use migrations to re-create the database:</span></span>

```dotnetcli
dotnet ef migrations remove
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

<span data-ttu-id="4f0b6-161">`dotnet ef migrations remove` Rimuove l'ultima migrazione.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-161">`dotnet ef migrations remove` removes the last migration.</span></span> <span data-ttu-id="4f0b6-162">Se è presente più di una migrazione, eliminare la cartella migrazioni.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-162">If there are more than one migration, delete the Migrations folder.</span></span>

---
<!-- End of VS tabs -->

<span data-ttu-id="4f0b6-163">Eseguire l'app e verificare che sia possibile creare, modificare e visualizzare filmati con un `Rating` campo.</span><span class="sxs-lookup"><span data-stu-id="4f0b6-163">Run the app and verify you can create, edit, and display movies with a `Rating` field.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="4f0b6-164">[Precedente](search.md) 
>  [Avanti](validation.md)</span><span class="sxs-lookup"><span data-stu-id="4f0b6-164">[Previous](search.md)
[Next](validation.md)</span></span>
