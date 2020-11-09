---
title: 'Parte 4, Razor pagine con EF Core nelle migrazioni ASP.NET Core'
author: rick-anderson
description: 'Parte 4 di Razor pagine e Entity Framework serie di esercitazioni.'
ms.author: riande
ms.date: 07/22/2019
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
uid: data/ef-rp/migrations
ms.openlocfilehash: e6d1b9f041e892aaa37840c28fdb3153bf098b0d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061106"
---
# <a name="part-4-no-locrazor-pages-with-ef-core-migrations-in-aspnet-core"></a><span data-ttu-id="91492-103">Parte 4, Razor pagine con migrazioni di EF core in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="91492-103">Part 4, Razor Pages with EF Core migrations in ASP.NET Core</span></span>

<span data-ttu-id="91492-104">[Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="91492-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="91492-105">In questa esercitazione viene presentata la funzionalità delle migrazioni di EF Core per la gestione delle modifiche al modello di dati.</span><span class="sxs-lookup"><span data-stu-id="91492-105">This tutorial introduces the EF Core migrations feature for managing data model changes.</span></span>

<span data-ttu-id="91492-106">Quando viene sviluppata una nuova app, il modello di dati cambia spesso.</span><span class="sxs-lookup"><span data-stu-id="91492-106">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="91492-107">Ogni volta che vengono apportate modifiche al modello, questo non è più sincronizzato con il database.</span><span class="sxs-lookup"><span data-stu-id="91492-107">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="91492-108">Questa serie di esercitazioni è iniziata con la configurazione di Entity Framework per creare il database se non esiste già.</span><span class="sxs-lookup"><span data-stu-id="91492-108">This tutorial series started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="91492-109">Ogni volta che il modello di dati viene modificato, è necessario eliminare il database.</span><span class="sxs-lookup"><span data-stu-id="91492-109">Each time the data model changes, you have to drop the database.</span></span> <span data-ttu-id="91492-110">Alla successiva esecuzione dell'app, la chiamata a `EnsureCreated` ricrea il database in modo che corrisponda al nuovo modello di dati.</span><span class="sxs-lookup"><span data-stu-id="91492-110">The next time the app runs, the call to `EnsureCreated` re-creates the database to match the new data model.</span></span> <span data-ttu-id="91492-111">Viene quindi eseguita la classe `DbInitializer` per il seeding del nuovo database.</span><span class="sxs-lookup"><span data-stu-id="91492-111">The `DbInitializer` class then runs to seed the new database.</span></span>

<span data-ttu-id="91492-112">Questo approccio che consiste nel mantenere il database sincronizzato con il modello di dati funziona bene fino a quando non si distribuisce l'app nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="91492-112">This approach to keeping the database in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="91492-113">Quando l'app è in esecuzione nell'ambiente di produzione, in genere esegue l'archiviazione di dati che devono essere mantenuti.</span><span class="sxs-lookup"><span data-stu-id="91492-113">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="91492-114">L'app non può essere avviata con un database di test ogni volta che viene apportata una modifica, come l'aggiunta di una colonna.</span><span class="sxs-lookup"><span data-stu-id="91492-114">The app can't start with a test database each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="91492-115">La funzionalità delle migrazioni di EF Core risolve questo problema consentendo a EF Core di aggiornare lo schema del database anziché crearne uno nuovo.</span><span class="sxs-lookup"><span data-stu-id="91492-115">The EF Core Migrations feature solves this problem by enabling EF Core to update the database schema instead of creating a new database.</span></span>

<span data-ttu-id="91492-116">Anziché eliminare e ricreare il database quando il modello di dati viene modificato, le migrazioni aggiornano lo schema e mantengono i dati esistenti.</span><span class="sxs-lookup"><span data-stu-id="91492-116">Rather than dropping and recreating the database when the data model changes, migrations updates the schema and retains existing data.</span></span>

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a><span data-ttu-id="91492-117">Eliminare il database</span><span class="sxs-lookup"><span data-stu-id="91492-117">Drop the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91492-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91492-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="91492-119">Usare **Esplora oggetti di SQL Server** (SSOX) per eliminare il database oppure eseguire il comando seguente nella **console di Gestione pacchetti** :</span><span class="sxs-lookup"><span data-stu-id="91492-119">Use **SQL Server Object Explorer** (SSOX) to delete the database, or run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="91492-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91492-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="91492-121">Al prompt dei comandi eseguire il comando seguente per installare l'interfaccia della riga di comando EF:</span><span class="sxs-lookup"><span data-stu-id="91492-121">Run the following command at a command prompt to install the EF CLI:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* <span data-ttu-id="91492-122">Al prompt dei comandi passare alla cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="91492-122">In the command prompt, navigate to the project folder.</span></span> <span data-ttu-id="91492-123">La cartella del progetto contiene il file *ContosoUniversity.csproj* .</span><span class="sxs-lookup"><span data-stu-id="91492-123">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="91492-124">Eliminare il file *CU.db* oppure eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="91492-124">Delete the *CU.db* file, or run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a><span data-ttu-id="91492-125">Creare una migrazione iniziale</span><span class="sxs-lookup"><span data-stu-id="91492-125">Create an initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91492-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91492-126">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="91492-127">Eseguire i comandi seguenti nella console di Gestione pacchetti:</span><span class="sxs-lookup"><span data-stu-id="91492-127">Run the following commands in the PMC:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="91492-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91492-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="91492-129">Assicurarsi che il prompt dei comandi si trovi nella cartella del progetto ed eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="91492-129">Make sure the command prompt is in the project folder, and run the following commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a><span data-ttu-id="91492-130">Metodi Up e Down</span><span class="sxs-lookup"><span data-stu-id="91492-130">Up and Down methods</span></span>

<span data-ttu-id="91492-131">Il comando `migrations add` di EF Core ha generato il codice per la creazione del database.</span><span class="sxs-lookup"><span data-stu-id="91492-131">The EF Core `migrations add` command generated code to create the database.</span></span> <span data-ttu-id="91492-132">Questo codice di migrazione è presente nel file *migrations \<timestamp> _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="91492-132">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="91492-133">Il metodo `Up` della classe `InitialCreate` crea le tabelle di database che corrispondono al set di entità del modello di dati.</span><span class="sxs-lookup"><span data-stu-id="91492-133">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="91492-134">Il metodo `Down` le elimina, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="91492-134">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

<span data-ttu-id="91492-135">Il codice precedente è per la migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="91492-135">The preceding code is for the initial migration.</span></span> <span data-ttu-id="91492-136">Il codice:</span><span class="sxs-lookup"><span data-stu-id="91492-136">The code:</span></span>

* <span data-ttu-id="91492-137">È stato generato dal comando `migrations add InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="91492-137">Was generated by the `migrations add InitialCreate` command.</span></span> 
* <span data-ttu-id="91492-138">Viene eseguito dal comando `database update`.</span><span class="sxs-lookup"><span data-stu-id="91492-138">Is executed by the `database update` command.</span></span>
* <span data-ttu-id="91492-139">Crea un database per il modello di dati specificato dalla classe del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="91492-139">Creates a database for the data model specified by the database context class.</span></span>

<span data-ttu-id="91492-140">Il parametro del nome della migrazione, nell'esempio "InitialCreate", viene usato per il nome del file.</span><span class="sxs-lookup"><span data-stu-id="91492-140">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="91492-141">Il nome della migrazione può essere qualsiasi nome di file valido.</span><span class="sxs-lookup"><span data-stu-id="91492-141">The migration name can be any valid file name.</span></span> <span data-ttu-id="91492-142">È consigliabile scegliere una parola o una frase che riepiloghi cosa viene fatto nella migrazione.</span><span class="sxs-lookup"><span data-stu-id="91492-142">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="91492-143">Ad esempio, una migrazione che ha aggiunto una tabella di reparto potrebbe essere denominata "AddDepartmentTable".</span><span class="sxs-lookup"><span data-stu-id="91492-143">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

## <a name="the-migrations-history-table"></a><span data-ttu-id="91492-144">Tabella di cronologia delle migrazioni</span><span class="sxs-lookup"><span data-stu-id="91492-144">The migrations history table</span></span>

* <span data-ttu-id="91492-145">Usare SSOX o lo strumento SQLite per esaminare il database.</span><span class="sxs-lookup"><span data-stu-id="91492-145">Use SSOX or your SQLite tool to inspect the database.</span></span>
* <span data-ttu-id="91492-146">Si noti l'aggiunta di una tabella `__EFMigrationsHistory`.</span><span class="sxs-lookup"><span data-stu-id="91492-146">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="91492-147">La tabella `__EFMigrationsHistory` tiene traccia di quali migrazioni sono state applicate al database.</span><span class="sxs-lookup"><span data-stu-id="91492-147">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the database.</span></span>
* <span data-ttu-id="91492-148">Visualizzare i dati nella tabella `__EFMigrationsHistory`.</span><span class="sxs-lookup"><span data-stu-id="91492-148">View the data in the `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="91492-149">Viene visualizzata una riga per la prima migrazione.</span><span class="sxs-lookup"><span data-stu-id="91492-149">It shows one row for the first migration.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="91492-150">Snapshot del modello di dati</span><span class="sxs-lookup"><span data-stu-id="91492-150">The data model snapshot</span></span>

<span data-ttu-id="91492-151">Le migrazioni creano uno *snapshot* del modello di dati corrente in *Migrations/SchoolContextModelSnapshot.cs* .</span><span class="sxs-lookup"><span data-stu-id="91492-151">Migrations creates a *snapshot* of the current data model in *Migrations/SchoolContextModelSnapshot.cs* .</span></span> <span data-ttu-id="91492-152">Quando si aggiunge una migrazione, EF determina le modifiche apportate confrontando il modello di dati corrente con il file dello snapshot.</span><span class="sxs-lookup"><span data-stu-id="91492-152">When you add a migration, EF determines what changed by comparing the current data model to the snapshot file.</span></span>

<span data-ttu-id="91492-153">Poiché il file di snapshot tiene traccia dello stato del modello di dati, non è possibile eliminare una migrazione eliminando il file `<timestamp>_<migrationname>.cs`.</span><span class="sxs-lookup"><span data-stu-id="91492-153">Because the snapshot file tracks the state of the data model, you can't delete a migration by deleting the `<timestamp>_<migrationname>.cs` file.</span></span> <span data-ttu-id="91492-154">Per eliminare la migrazione più recente, è necessario usare il comando `migrations remove`.</span><span class="sxs-lookup"><span data-stu-id="91492-154">To back out the most recent migration, you have to use the `migrations remove` command.</span></span> <span data-ttu-id="91492-155">Tale comando elimina la migrazione e garantisce che lo snapshot venga reimpostato correttamente.</span><span class="sxs-lookup"><span data-stu-id="91492-155">That command deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="91492-156">Per altre informazioni, vedere [migrazioni di DotNet EF Rimuovi](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="91492-156">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

## <a name="remove-ensurecreated"></a><span data-ttu-id="91492-157">Rimuovere EnsureCreated</span><span class="sxs-lookup"><span data-stu-id="91492-157">Remove EnsureCreated</span></span>

<span data-ttu-id="91492-158">Questa serie di esercitazioni è iniziata usando `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="91492-158">This tutorial series started by using `EnsureCreated`.</span></span> <span data-ttu-id="91492-159">`EnsureCreated` non crea una tabella di cronologia delle migrazioni e pertanto non può essere usato con le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="91492-159">`EnsureCreated` doesn't create a migrations history table and so can't be used with migrations.</span></span> <span data-ttu-id="91492-160">È progettato per il test o per la creazione rapida di prototipi in cui il database viene eliminato e ricreato frequentemente.</span><span class="sxs-lookup"><span data-stu-id="91492-160">It's designed for testing or rapid prototyping where the database is dropped and re-created frequently.</span></span>

<span data-ttu-id="91492-161">Da questo punto in poi, le esercitazioni useranno le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="91492-161">From this point forward, the tutorials will use migrations.</span></span>

<span data-ttu-id="91492-162">In *Data/DBInitializer.cs* impostare come commento la riga seguente:</span><span class="sxs-lookup"><span data-stu-id="91492-162">In *Data/DBInitializer.cs* , comment out the following line:</span></span>

```csharp
context.Database.EnsureCreated();
```
<span data-ttu-id="91492-163">Eseguire l'app e verificare che il database sia sottoposto a seeding.</span><span class="sxs-lookup"><span data-stu-id="91492-163">Run the app and verify that the database is seeded.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="91492-164">Applicazione delle migrazioni nell'ambiente di produzione</span><span class="sxs-lookup"><span data-stu-id="91492-164">Applying migrations in production</span></span>

<span data-ttu-id="91492-165">È consigliabile fare in modo che le app nell'ambiente di produzione **non** chiamino [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) all'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="91492-165">We recommend that production apps **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="91492-166">`Migrate` non deve essere chiamato da un'app distribuita in una server farm.</span><span class="sxs-lookup"><span data-stu-id="91492-166">`Migrate` shouldn't be called from an app that is deployed to a server farm.</span></span> <span data-ttu-id="91492-167">Se l'app viene distribuita in più istanze del server, è difficile assicurarsi che gli aggiornamenti dello schema di database non vengano eseguiti da più server o che non siano in conflitto con l'accesso in lettura/scrittura.</span><span class="sxs-lookup"><span data-stu-id="91492-167">If the app is scaled out to multiple server instances, it's hard to ensure database schema updates don't happen from multiple servers or conflict with read/write access.</span></span>

<span data-ttu-id="91492-168">La migrazione del database deve essere eseguita come parte della distribuzione e in modo controllato.</span><span class="sxs-lookup"><span data-stu-id="91492-168">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="91492-169">Gli approcci alla migrazione di database in ambiente di produzione includono:</span><span class="sxs-lookup"><span data-stu-id="91492-169">Production database migration approaches include:</span></span>

* <span data-ttu-id="91492-170">Uso delle migrazioni per creare script SQL e uso degli script SQL nella distribuzione.</span><span class="sxs-lookup"><span data-stu-id="91492-170">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="91492-171">Esecuzione di `dotnet ef database update` da un ambiente controllato.</span><span class="sxs-lookup"><span data-stu-id="91492-171">Running `dotnet ef database update` from a controlled environment.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="91492-172">risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="91492-172">Troubleshooting</span></span>

<span data-ttu-id="91492-173">Se l'app usa SQL Server Local DB e visualizza l'eccezione seguente:</span><span class="sxs-lookup"><span data-stu-id="91492-173">If the app uses SQL Server LocalDB and displays the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="91492-174">È possibile che la soluzione esegua `dotnet ef database update` al prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="91492-174">The solution may be to run `dotnet ef database update` at a command prompt.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="91492-175">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="91492-175">Additional resources</span></span>

* <span data-ttu-id="91492-176">[Interfaccia della riga di comando EF Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="91492-176">[EF Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="91492-177">Console di Gestione pacchetti (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="91492-177">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a><span data-ttu-id="91492-178">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="91492-178">Next steps</span></span>

<span data-ttu-id="91492-179">L'esercitazione successiva compila il modello di dati, aggiungendo le proprietà delle entità e le nuove entità.</span><span class="sxs-lookup"><span data-stu-id="91492-179">The next tutorial builds out the data model, adding entity properties and new entities.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="91492-180">[Esercitazione precedente](xref:data/ef-rp/sort-filter-page) 
>  [Esercitazione successiva](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="91492-180">[Previous tutorial](xref:data/ef-rp/sort-filter-page)
[Next tutorial](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="91492-181">In questa esercitazione viene usata la funzionalità delle migrazioni EF Core per la gestione delle modifiche al modello di dati.</span><span class="sxs-lookup"><span data-stu-id="91492-181">In this tutorial, the EF Core migrations feature for managing data model changes is used.</span></span>

<span data-ttu-id="91492-182">Se si verificano problemi che non si è in grado di risolvere, scaricare l'[app completa](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="91492-182">If you run into problems you can't solve, download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

<span data-ttu-id="91492-183">Quando viene sviluppata una nuova app, il modello di dati cambia spesso.</span><span class="sxs-lookup"><span data-stu-id="91492-183">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="91492-184">Ogni volta che vengono apportate modifiche al modello, questo non è più sincronizzato con il database.</span><span class="sxs-lookup"><span data-stu-id="91492-184">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="91492-185">L'esercitazione è iniziata con la configurazione di Entity Framework per creare il database se non esiste già.</span><span class="sxs-lookup"><span data-stu-id="91492-185">This tutorial started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="91492-186">Ogni volta che il modello di dati cambia:</span><span class="sxs-lookup"><span data-stu-id="91492-186">Each time the data model changes:</span></span>

* <span data-ttu-id="91492-187">Il database viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="91492-187">The DB is dropped.</span></span>
* <span data-ttu-id="91492-188">EF ne crea uno nuovo che corrisponde al modello.</span><span class="sxs-lookup"><span data-stu-id="91492-188">EF creates a new one that matches the model.</span></span>
* <span data-ttu-id="91492-189">L'app esegue il seeding del database con dati di test.</span><span class="sxs-lookup"><span data-stu-id="91492-189">The app seeds the DB with test data.</span></span>

<span data-ttu-id="91492-190">Questo approccio che consiste nel mantenere il database sincronizzato con il modello di dati funziona bene fino a quando non si distribuisce l'app nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="91492-190">This approach to keeping the DB in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="91492-191">Quando l'app è in esecuzione nell'ambiente di produzione, in genere esegue l'archiviazione di dati che devono essere mantenuti.</span><span class="sxs-lookup"><span data-stu-id="91492-191">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="91492-192">L'app non può essere avviata con un database di test ogni volta che viene apportata una modifica, come ad esempio l'aggiunta di una colonna.</span><span class="sxs-lookup"><span data-stu-id="91492-192">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="91492-193">La funzionalità delle migrazioni di EF Core risolve questo problema consentendo a EF Core di aggiornare lo schema del database anziché creare un nuovo database.</span><span class="sxs-lookup"><span data-stu-id="91492-193">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new DB.</span></span>

<span data-ttu-id="91492-194">Anziché eliminare e ricreare il database quando il modello di dati viene modificato, le migrazioni aggiornano lo schema e mantengono i dati esistenti.</span><span class="sxs-lookup"><span data-stu-id="91492-194">Rather than dropping and recreating the DB when the data model changes, migrations updates the schema and retains existing data.</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="91492-195">Eliminare il database</span><span class="sxs-lookup"><span data-stu-id="91492-195">Drop the database</span></span>

<span data-ttu-id="91492-196">Usare **Esplora oggetti di SQL Server** o il comando `database drop`:</span><span class="sxs-lookup"><span data-stu-id="91492-196">Use **SQL Server Object Explorer** (SSOX) or the `database drop` command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91492-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91492-197">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="91492-198">Nella **console di Gestione pacchetti** eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="91492-198">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
```

<span data-ttu-id="91492-199">Eseguire `Get-Help about_EntityFrameworkCore` dalla console di Gestione pacchetti per ottenere informazioni.</span><span class="sxs-lookup"><span data-stu-id="91492-199">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="91492-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91492-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="91492-201">Aprire una finestra di comando e passare alla cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="91492-201">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="91492-202">La cartella del progetto contiene il file *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="91492-202">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="91492-203">Digitare quanto segue nella finestra di comando:</span><span class="sxs-lookup"><span data-stu-id="91492-203">Enter the following in the command window:</span></span>

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a><span data-ttu-id="91492-204">Creare una migrazione iniziale e aggiornare il database</span><span class="sxs-lookup"><span data-stu-id="91492-204">Create an initial migration and update the DB</span></span>

<span data-ttu-id="91492-205">Compilare il progetto e creare la prima migrazione.</span><span class="sxs-lookup"><span data-stu-id="91492-205">Build the project and create the first migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91492-206">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91492-206">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="91492-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91492-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="91492-208">Esaminare i metodi Up e Down</span><span class="sxs-lookup"><span data-stu-id="91492-208">Examine the Up and Down methods</span></span>

<span data-ttu-id="91492-209">Il comando `migrations add` di EF Core ha generato codice per la creazione del database.</span><span class="sxs-lookup"><span data-stu-id="91492-209">The EF Core `migrations add` command  generated code to create the DB.</span></span> <span data-ttu-id="91492-210">Questo codice di migrazione è presente nel file *migrations \<timestamp> _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="91492-210">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="91492-211">Il metodo `Up` della classe `InitialCreate` crea le tabelle di database che corrispondono al set di entità del modello di dati.</span><span class="sxs-lookup"><span data-stu-id="91492-211">The `Up` method of the `InitialCreate` class creates the DB tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="91492-212">Il metodo `Down` le elimina, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="91492-212">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

<span data-ttu-id="91492-213">Le migrazioni chiamano il metodo `Up` per implementare le modifiche al modello di dati per una migrazione.</span><span class="sxs-lookup"><span data-stu-id="91492-213">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="91492-214">Quando si immette un comando per annullare l'aggiornamento, le migrazioni chiamano il metodo `Down`.</span><span class="sxs-lookup"><span data-stu-id="91492-214">When you enter a command to roll back the update, migrations calls the `Down` method.</span></span>

<span data-ttu-id="91492-215">Il codice precedente è per la migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="91492-215">The preceding code is for the initial migration.</span></span> <span data-ttu-id="91492-216">Tale codice è stato creato quando è stato eseguito il comando `migrations add InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="91492-216">That code was created when the `migrations add InitialCreate` command was run.</span></span> <span data-ttu-id="91492-217">Il parametro del nome della migrazione, nell'esempio "InitialCreate", viene usato per il nome del file.</span><span class="sxs-lookup"><span data-stu-id="91492-217">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="91492-218">Il nome della migrazione può essere qualsiasi nome di file valido.</span><span class="sxs-lookup"><span data-stu-id="91492-218">The migration name can be any valid file name.</span></span> <span data-ttu-id="91492-219">È consigliabile scegliere una parola o una frase che riepiloghi cosa viene fatto nella migrazione.</span><span class="sxs-lookup"><span data-stu-id="91492-219">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="91492-220">Ad esempio, una migrazione che ha aggiunto una tabella di reparto potrebbe essere denominata "AddDepartmentTable".</span><span class="sxs-lookup"><span data-stu-id="91492-220">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

<span data-ttu-id="91492-221">Se la migrazione iniziale viene creata e il database esiste:</span><span class="sxs-lookup"><span data-stu-id="91492-221">If the initial migration is created and the DB exists:</span></span>

* <span data-ttu-id="91492-222">Viene generato il codice di creazione del database.</span><span class="sxs-lookup"><span data-stu-id="91492-222">The DB creation code is generated.</span></span>
* <span data-ttu-id="91492-223">Non è necessario che venga eseguito il codice di creazione del database perché il database corrisponde già al modello di dati.</span><span class="sxs-lookup"><span data-stu-id="91492-223">The DB creation code doesn't need to run because the DB already matches the data model.</span></span> <span data-ttu-id="91492-224">Se viene eseguito il codice di creazione del database, non apporta alcuna modifica perché il database corrisponde già al modello di dati.</span><span class="sxs-lookup"><span data-stu-id="91492-224">If the DB creation code is run, it doesn't make any changes because the DB already matches the data model.</span></span>

<span data-ttu-id="91492-225">Quando l'app viene distribuita in un nuovo ambiente, è necessario eseguire il codice di creazione del database per creare il database.</span><span class="sxs-lookup"><span data-stu-id="91492-225">When the app is deployed to a new environment, the DB creation code must be run to create the DB.</span></span>

<span data-ttu-id="91492-226">Il database infatti non esiste in quanto è stato precedentemente eliminato, pertanto la migrazione crea il nuovo database.</span><span class="sxs-lookup"><span data-stu-id="91492-226">Previously the DB was dropped and doesn't exist, so migrations creates the new DB.</span></span>

### <a name="the-data-model-snapshot"></a><span data-ttu-id="91492-227">Snapshot del modello di dati</span><span class="sxs-lookup"><span data-stu-id="91492-227">The data model snapshot</span></span>

<span data-ttu-id="91492-228">Le migrazioni creano uno *snapshot* dello schema del database corrente in *Migrations/SchoolContextModelSnapshot.cs* .</span><span class="sxs-lookup"><span data-stu-id="91492-228">Migrations create a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs* .</span></span> <span data-ttu-id="91492-229">Quando si aggiunge una migrazione, EF determina le modifiche apportate confrontando il modello di dati con il file dello snapshot.</span><span class="sxs-lookup"><span data-stu-id="91492-229">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="91492-230">Per eliminare una migrazione, usare il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="91492-230">To delete a migration, use the following command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91492-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91492-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="91492-232">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="91492-232">Remove-Migration</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="91492-233">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91492-233">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

<span data-ttu-id="91492-234">Per altre informazioni, vedere [migrazioni di DotNet EF Rimuovi](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="91492-234">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

---

<span data-ttu-id="91492-235">Il comando di rimozione migrazioni elimina la migrazione e garantisce che lo snapshot venga reimpostato correttamente.</span><span class="sxs-lookup"><span data-stu-id="91492-235">The remove migrations command deletes the migration and ensures the snapshot is correctly reset.</span></span>

### <a name="remove-ensurecreated-and-test-the-app"></a><span data-ttu-id="91492-236">Rimuovere EnsureCreated e testare l'app</span><span class="sxs-lookup"><span data-stu-id="91492-236">Remove EnsureCreated and test the app</span></span>

<span data-ttu-id="91492-237">Per lo sviluppo iniziale è stato usato il comando `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="91492-237">For early development, `EnsureCreated` was used.</span></span> <span data-ttu-id="91492-238">In questa esercitazione vengono usate le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="91492-238">In this tutorial, migrations are used.</span></span> <span data-ttu-id="91492-239">`EnsureCreated` presenta le limitazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="91492-239">`EnsureCreated` has the following limitations:</span></span>

* <span data-ttu-id="91492-240">Ignora le migrazioni e crea il database e lo schema.</span><span class="sxs-lookup"><span data-stu-id="91492-240">Bypasses migrations and creates the DB and schema.</span></span>
* <span data-ttu-id="91492-241">Non crea una tabella delle migrazioni.</span><span class="sxs-lookup"><span data-stu-id="91492-241">Doesn't create a migrations table.</span></span>
* <span data-ttu-id="91492-242">*Non* può essere usato con le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="91492-242">Can *not* be used with migrations.</span></span>
* <span data-ttu-id="91492-243">È progettato per il test o per la creazione rapida di prototipi in cui il database viene eliminato e ricreato frequentemente.</span><span class="sxs-lookup"><span data-stu-id="91492-243">Is designed for testing or rapid prototyping where the DB is dropped and re-created frequently.</span></span>

<span data-ttu-id="91492-244">Rimuovere `EnsureCreated`:</span><span class="sxs-lookup"><span data-stu-id="91492-244">Remove `EnsureCreated`:</span></span>

```csharp
context.Database.EnsureCreated();
```

<span data-ttu-id="91492-245">Eseguire l'app e verificare che il database venga inizializzato.</span><span class="sxs-lookup"><span data-stu-id="91492-245">Run the app and verify the DB is seeded.</span></span>

### <a name="inspect-the-database"></a><span data-ttu-id="91492-246">Esaminare il database</span><span class="sxs-lookup"><span data-stu-id="91492-246">Inspect the database</span></span>

<span data-ttu-id="91492-247">Per controllare il database, usare **Esplora oggetti di SQL Server** .</span><span class="sxs-lookup"><span data-stu-id="91492-247">Use **SQL Server Object Explorer** to inspect the DB.</span></span> <span data-ttu-id="91492-248">Si noti l'aggiunta di una tabella `__EFMigrationsHistory`.</span><span class="sxs-lookup"><span data-stu-id="91492-248">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="91492-249">La tabella `__EFMigrationsHistory` tiene traccia di quali migrazioni sono state applicate al database.</span><span class="sxs-lookup"><span data-stu-id="91492-249">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the DB.</span></span> <span data-ttu-id="91492-250">Visualizzare i dati nella tabella `__EFMigrationsHistory`: viene visualizzata una riga per la prima migrazione.</span><span class="sxs-lookup"><span data-stu-id="91492-250">View the data in the `__EFMigrationsHistory` table, it shows one row for the first migration.</span></span> <span data-ttu-id="91492-251">Nell'ultimo log nell'esempio di output della CLI precedente viene visualizzata l'istruzione INSERT che crea tale riga.</span><span class="sxs-lookup"><span data-stu-id="91492-251">The last log in the preceding CLI output example shows the INSERT statement that creates this row.</span></span>

<span data-ttu-id="91492-252">Eseguire l'app e verificare che tutto funzioni.</span><span class="sxs-lookup"><span data-stu-id="91492-252">Run the app and verify that everything works.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="91492-253">Applicazione delle migrazioni nell'ambiente di produzione</span><span class="sxs-lookup"><span data-stu-id="91492-253">Applying migrations in production</span></span>

<span data-ttu-id="91492-254">È consigliabile fare in modo che le app nell'ambiente di produzione **non** chiamino [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) all'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="91492-254">We recommend production apps should **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="91492-255">L'elemento `Migrate` non deve essere chiamato da un'app nella server farm.</span><span class="sxs-lookup"><span data-stu-id="91492-255">`Migrate` shouldn't be called from an app in server farm.</span></span> <span data-ttu-id="91492-256">Ad esempio, se l'app è stata distribuita in un cloud con scale-out, ovvero se sono in esecuzione più istanze dell'app.</span><span class="sxs-lookup"><span data-stu-id="91492-256">For example, if the app has been cloud deployed with scale-out (multiple instances of the app are running).</span></span>

<span data-ttu-id="91492-257">La migrazione del database deve essere eseguita come parte della distribuzione e in modo controllato.</span><span class="sxs-lookup"><span data-stu-id="91492-257">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="91492-258">Gli approcci alla migrazione di database in ambiente di produzione includono:</span><span class="sxs-lookup"><span data-stu-id="91492-258">Production database migration approaches include:</span></span>

* <span data-ttu-id="91492-259">Uso delle migrazioni per creare script SQL e uso degli script SQL nella distribuzione.</span><span class="sxs-lookup"><span data-stu-id="91492-259">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="91492-260">Esecuzione di `dotnet ef database update` da un ambiente controllato.</span><span class="sxs-lookup"><span data-stu-id="91492-260">Running `dotnet ef database update` from a controlled environment.</span></span>

<span data-ttu-id="91492-261">EF Core usa la tabella `__MigrationsHistory` per stabilire se è necessario eseguire migrazioni.</span><span class="sxs-lookup"><span data-stu-id="91492-261">EF Core uses the `__MigrationsHistory` table to see if any migrations need to run.</span></span> <span data-ttu-id="91492-262">Se il database è aggiornato, non viene eseguita alcuna migrazione.</span><span class="sxs-lookup"><span data-stu-id="91492-262">If the DB is up-to-date, no migration is run.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="91492-263">risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="91492-263">Troubleshooting</span></span>

<span data-ttu-id="91492-264">Scaricare l'[app completa](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span><span class="sxs-lookup"><span data-stu-id="91492-264">Download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span></span>

<span data-ttu-id="91492-265">L'app genera l'eccezione seguente:</span><span class="sxs-lookup"><span data-stu-id="91492-265">The app generates the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="91492-266">Soluzione: eseguire `dotnet ef database update`</span><span class="sxs-lookup"><span data-stu-id="91492-266">Solution: Run `dotnet ef database update`</span></span>

### <a name="additional-resources"></a><span data-ttu-id="91492-267">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="91492-267">Additional resources</span></span>

* [<span data-ttu-id="91492-268">Versione YouTube dell'esercitazione</span><span class="sxs-lookup"><span data-stu-id="91492-268">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* <span data-ttu-id="91492-269">[Interfaccia della riga di comando di .NET Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="91492-269">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="91492-270">Console di Gestione pacchetti (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="91492-270">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> <span data-ttu-id="91492-271">[Precedente](xref:data/ef-rp/sort-filter-page) 
>  [Avanti](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="91492-271">[Previous](xref:data/ef-rp/sort-filter-page)
[Next](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

