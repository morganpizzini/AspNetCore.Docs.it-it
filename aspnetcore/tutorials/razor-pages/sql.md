---
title: Parte 4, utilizzo di un database
author: rick-anderson
description: 'Parte 4 della serie di esercitazioni sulle :::no-loc(Razor)::: pagine.'
ms.author: riande
ms.date: 09/26/2020
no-loc:
- ':::no-loc(Index):::'
- ':::no-loc(Create):::'
- ':::no-loc(Delete):::'
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 2c5bc221901d9e41984fb591755a8ad94e7e1420
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570237"
---
# <a name="part-4-of-tutorial-series-on-no-locrazor-pages"></a><span data-ttu-id="8334b-103">Parte 4 della serie di esercitazioni sulle :::no-loc(Razor)::: pagine</span><span class="sxs-lookup"><span data-stu-id="8334b-103">Part 4 of tutorial series on :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="8334b-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="8334b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="8334b-105">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="8334b-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="8334b-106">L'oggetto `:::no-loc(Razor):::PagesMovieContext` gestisce l'attività di connessione al database e di mapping degli oggetti `Movie` ai record di database.</span><span class="sxs-lookup"><span data-stu-id="8334b-106">The `:::no-loc(Razor):::PagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="8334b-107">Il contesto del database viene registrato con il contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) nel metodo `ConfigureServices` di *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="8334b-107">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs* :</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8334b-108">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8334b-108">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8334b-109">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8334b-109">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="8334b-110">La chiave viene letta dal sistema di [configurazione](xref:fundamentals/configuration/index) ASP.NET Core `ConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="8334b-110">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="8334b-111">Per lo sviluppo locale, la configurazione ottiene la stringa di connessione dal *:::no-loc(appsettings.json):::* file.</span><span class="sxs-lookup"><span data-stu-id="8334b-111">For local development, configuration gets the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8334b-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8334b-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8334b-113">La stringa di connessione generata sarà simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="8334b-113">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/:::no-loc(appsettings.json):::?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8334b-114">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8334b-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="8334b-115">Quando l'app viene distribuita in un server di test o di produzione, è possibile usare una variabile di ambiente per impostare la stringa di connessione su un server di database di test o di produzione.</span><span class="sxs-lookup"><span data-stu-id="8334b-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="8334b-116">Per ulteriori informazioni, vedere [configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="8334b-116">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8334b-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8334b-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="8334b-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="8334b-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="8334b-119">Local DB è una versione leggera del motore di database di SQL Server Express appositamente pensato per lo sviluppo di programmi.</span><span class="sxs-lookup"><span data-stu-id="8334b-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="8334b-120">Local DB viene avviato su richiesta ed eseguito in modalità utente; non richiede quindi una configurazione complessa.</span><span class="sxs-lookup"><span data-stu-id="8334b-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="8334b-121">Per impostazione predefinita, Local DB crea file con estensione `*.mdf` nella directory `C:\Users\<user>\`.</span><span class="sxs-lookup"><span data-stu-id="8334b-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
1. <span data-ttu-id="8334b-122">Dal menu **Visualizzazione** aprire **Esplora oggetti di SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="8334b-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

   ![Menu Visualizza](sql/_static/5/ssox.png)

1. <span data-ttu-id="8334b-124">Fare clic con il pulsante destro del mouse sulla `Movie` tabella e scegliere **Visualizza finestra di progettazione** :</span><span class="sxs-lookup"><span data-stu-id="8334b-124">Right-click on the `Movie` table and select **View Designer** :</span></span>

   ![Menu di scelta rapida aperti per la tabella Movie](sql/_static/5/design.png)

   ![Tabelle Movie aperte nella finestra di progettazione](sql/_static/dv.png)

   <span data-ttu-id="8334b-127">Si noti l'icona a forma di chiave accanto a `ID`.</span><span class="sxs-lookup"><span data-stu-id="8334b-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="8334b-128">Per impostazione predefinita, Entity Framework crea una proprietà denominata `ID` per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="8334b-128">By default, EF creates a property named `ID` for the primary key.</span></span>

1. <span data-ttu-id="8334b-129">Fare clic con il pulsante destro del mouse sulla `Movie` tabella e selezionare **Visualizza dati** :</span><span class="sxs-lookup"><span data-stu-id="8334b-129">Right-click on the `Movie` table and select **View Data** :</span></span>

   ![Tabella Movie aperta con i dati della tabella](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8334b-131">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8334b-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="8334b-132">SQLite</span><span class="sxs-lookup"><span data-stu-id="8334b-132">SQLite</span></span>

<span data-ttu-id="8334b-133">Nel sito Web di [SQLite](https://www.sqlite.org/) si dichiara:</span><span class="sxs-lookup"><span data-stu-id="8334b-133">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="8334b-134">SQLite è un motore di database SQL autonomo, a elevata affidabilità, incorporato, completo e di dominio pubblico.</span><span class="sxs-lookup"><span data-stu-id="8334b-134">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="8334b-135">SQLite è il motore di database più usato in tutto il mondo.</span><span class="sxs-lookup"><span data-stu-id="8334b-135">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="8334b-136">Sono disponibili molti strumenti di terze parti che è possibile scaricare per gestire e visualizzare un database SQLite.</span><span class="sxs-lookup"><span data-stu-id="8334b-136">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="8334b-137">L'immagine seguente è stata tratta da [DB Browser per SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="8334b-137">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="8334b-138">Se si preferisce uno strumento SQLite in particolare, aggiungere un commento sui motivi della preferenza.</span><span class="sxs-lookup"><span data-stu-id="8334b-138">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![Browser DB per SQLite con database di film](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="8334b-140">Per questa esercitazione, viene usata la funzionalità *migrazioni* di Entity Framework Core, laddove possibile.</span><span class="sxs-lookup"><span data-stu-id="8334b-140">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="8334b-141">Le migrazioni aggiornano lo schema del database in base alle modifiche nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="8334b-141">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="8334b-142">Tuttavia, le migrazioni possono eseguire solo i tipi di modifiche supportate dal provider EF Core e le funzionalità del provider SQLite sono limitate.</span><span class="sxs-lookup"><span data-stu-id="8334b-142">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="8334b-143">Ad esempio l'aggiunta di una colonna è supportata, ma la rimozione o la modifica di una colonna non è supportata.</span><span class="sxs-lookup"><span data-stu-id="8334b-143">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="8334b-144">Se si crea una migrazione per rimuovere o modificare una colonna, il comando `ef migrations add` ha esito positivo, ma il comando `ef database update` ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="8334b-144">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="8334b-145">A causa di queste limitazioni, in questa esercitazione non vengono usate le migrazioni per le modifiche dello schema di SQLite.</span><span class="sxs-lookup"><span data-stu-id="8334b-145">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="8334b-146">Al contrario, quando lo schema viene modificato, il database viene eliminato e ricreato.</span><span class="sxs-lookup"><span data-stu-id="8334b-146">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="8334b-147">La soluzione per ovviare alle limitazioni di SQLite consiste nello scrivere manualmente il codice delle migrazioni per eseguire una ricompilazione della tabella in caso di modifiche nella tabella.</span><span class="sxs-lookup"><span data-stu-id="8334b-147">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="8334b-148">La ricompilazione della tabella comporta:</span><span class="sxs-lookup"><span data-stu-id="8334b-148">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="8334b-149">Creazione di una nuova tabella.</span><span class="sxs-lookup"><span data-stu-id="8334b-149">Creating a new table.</span></span>
>* <span data-ttu-id="8334b-150">Copia dei dati dalla vecchia tabella alla nuova tabella.</span><span class="sxs-lookup"><span data-stu-id="8334b-150">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="8334b-151">Eliminazione della tabella precedente.</span><span class="sxs-lookup"><span data-stu-id="8334b-151">Dropping the old table.</span></span>
>* <span data-ttu-id="8334b-152">Ridenominazione della nuova tabella.</span><span class="sxs-lookup"><span data-stu-id="8334b-152">Renaming the new table.</span></span>
>
><span data-ttu-id="8334b-153">Per altre informazioni, vedere le risorse seguenti:</span><span class="sxs-lookup"><span data-stu-id="8334b-153">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="8334b-154">Limitazioni del provider di database SQLite per EF Core</span><span class="sxs-lookup"><span data-stu-id="8334b-154">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="8334b-155">Personalizzare il codice di migrazione</span><span class="sxs-lookup"><span data-stu-id="8334b-155">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="8334b-156">Seeding dei dati</span><span class="sxs-lookup"><span data-stu-id="8334b-156">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="8334b-157">Istruzione ALTER TABLE di SQLite</span><span class="sxs-lookup"><span data-stu-id="8334b-157">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="8334b-158">Specificare il valore di inizializzazione del database</span><span class="sxs-lookup"><span data-stu-id="8334b-158">Seed the database</span></span>

<span data-ttu-id="8334b-159">:::no-loc(Create)::: nuova classe denominata `SeedData` nella cartella *Models* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8334b-159">:::no-loc(Create)::: a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="8334b-160">Se sono presenti film nel database, l'inizializzatore del valore di inizializzazione viene restituito e non viene aggiunto alcun film.</span><span class="sxs-lookup"><span data-stu-id="8334b-160">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="8334b-161">Aggiungere l'inizializzatore del valore di inizializzazione</span><span class="sxs-lookup"><span data-stu-id="8334b-161">Add the seed initializer</span></span>

<span data-ttu-id="8334b-162">Sostituire il contenuto di *Program.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8334b-162">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/Program.cs)]

<span data-ttu-id="8334b-163">Nel codice precedente, il `Main` metodo è stato modificato per eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="8334b-163">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="8334b-164">Ottenere un'istanza del contesto di database dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8334b-164">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="8334b-165">Chiamare il `seedData.Initialize` metodo passando all'istanza del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="8334b-165">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="8334b-166">Eliminare il contesto dopo che il metodo di inizializzazione è stato completato.</span><span class="sxs-lookup"><span data-stu-id="8334b-166">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="8334b-167">L' [istruzione using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) garantisce che il contesto venga eliminato.</span><span class="sxs-lookup"><span data-stu-id="8334b-167">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="8334b-168">Quando `Update-Database` non è stato eseguito, si verifica l'eccezione seguente:</span><span class="sxs-lookup"><span data-stu-id="8334b-168">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="8334b-169">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="8334b-169">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8334b-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8334b-170">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="8334b-171">:::no-loc(Delete)::: tutti i record del database.</span><span class="sxs-lookup"><span data-stu-id="8334b-171">:::no-loc(Delete)::: all the records in the database.</span></span> <span data-ttu-id="8334b-172">Usare i collegamenti di eliminazione nel browser o da [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="8334b-172">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>

1. <span data-ttu-id="8334b-173">Forzare l'inizializzazione dell'app chiamando i metodi nella `Startup` classe, in modo che venga eseguito il metodo di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="8334b-173">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="8334b-174">Per forzare l'inizializzazione, IIS Express deve essere arrestato e riavviato.</span><span class="sxs-lookup"><span data-stu-id="8334b-174">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="8334b-175">Arrestare e riavviare IIS con uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="8334b-175">Stop and restart IIS with any of the following approaches:</span></span>

   1. <span data-ttu-id="8334b-176">Fare clic con il pulsante destro del mouse sull'icona della barra di sistema IIS Express nell'area di notifica e selezionare **Esci** o **Arresta sito** :</span><span class="sxs-lookup"><span data-stu-id="8334b-176">Right-click the IIS Express system tray icon in the notification area and select **Exit** or **Stop Site** :</span></span>

      ![Icona dell'area di notifica di IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

      ![Menu di scelta rapida](sql/_static/stopIIS.png)

   1. <span data-ttu-id="8334b-179">Se l'app è in esecuzione in modalità non di debug, premere <kbd>F5</kbd> per l'esecuzione in modalità di debug.</span><span class="sxs-lookup"><span data-stu-id="8334b-179">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
   1. <span data-ttu-id="8334b-180">Se l'app è in modalità di debug, arrestare il debugger e premere <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="8334b-180">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8334b-181">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8334b-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="8334b-182">:::no-loc(Delete)::: tutti i record nel database, in modo che venga eseguito il metodo di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="8334b-182">:::no-loc(Delete)::: all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="8334b-183">Arrestare e avviare l'app per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="8334b-183">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="8334b-184">L'app visualizza i dati sottoposti a seed:</span><span class="sxs-lookup"><span data-stu-id="8334b-184">The app shows the seeded data:</span></span>

![Applicazione film aperta nel browser che mostra i dati dei film](sql/_static/5/m55.png)

## <a name="additional-resources"></a><span data-ttu-id="8334b-186">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="8334b-186">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="8334b-187">[Precedente: con ponteggi :::no-loc(Razor)::: Pagine](xref:tutorials/razor-pages/page) 
>  [successive: aggiornare le pagine](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="8334b-187">[Previous: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="8334b-188">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="8334b-188">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="8334b-189">L'oggetto `:::no-loc(Razor):::PagesMovieContext` gestisce l'attività di connessione al database e di mapping degli oggetti `Movie` ai record di database.</span><span class="sxs-lookup"><span data-stu-id="8334b-189">The `:::no-loc(Razor):::PagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="8334b-190">Il contesto del database viene registrato con il contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) nel metodo `ConfigureServices` di *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="8334b-190">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs* :</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8334b-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8334b-191">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8334b-192">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8334b-192">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="8334b-193">La chiave viene letta dal sistema di [configurazione](xref:fundamentals/configuration/index) ASP.NET Core `ConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="8334b-193">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="8334b-194">Per lo sviluppo locale, la configurazione ottiene la stringa di connessione dal *:::no-loc(appsettings.json):::* file.</span><span class="sxs-lookup"><span data-stu-id="8334b-194">For local development, configuration gets the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8334b-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8334b-195">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8334b-196">La stringa di connessione generata sarà simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="8334b-196">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/:::no-loc(appsettings.json):::?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8334b-197">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8334b-197">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="8334b-198">Quando l'app viene distribuita in un server di test o di produzione, è possibile usare una variabile di ambiente per impostare la stringa di connessione su un server di database di test o di produzione.</span><span class="sxs-lookup"><span data-stu-id="8334b-198">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="8334b-199">Per altre informazioni, vedere [Configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="8334b-199">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8334b-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8334b-200">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="8334b-201">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="8334b-201">SQL Server Express LocalDB</span></span>

<span data-ttu-id="8334b-202">Local DB è una versione leggera del motore di database di SQL Server Express appositamente pensato per lo sviluppo di programmi.</span><span class="sxs-lookup"><span data-stu-id="8334b-202">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="8334b-203">Local DB viene avviato su richiesta ed eseguito in modalità utente; non richiede quindi una configurazione complessa.</span><span class="sxs-lookup"><span data-stu-id="8334b-203">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="8334b-204">Per impostazione predefinita, Local DB crea file con estensione `*.mdf` nella directory `C:\Users\<user>\`.</span><span class="sxs-lookup"><span data-stu-id="8334b-204">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="8334b-205">Dal menu **Visualizzazione** aprire **Esplora oggetti di SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="8334b-205">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu Visualizza](sql/_static/ssox.png)

* <span data-ttu-id="8334b-207">Fare clic con il pulsante destro del mouse sulla `Movie` tabella e scegliere **Visualizza finestra di progettazione** :</span><span class="sxs-lookup"><span data-stu-id="8334b-207">Right-click on the `Movie` table and select **View Designer** :</span></span>

  ![Menu di scelta rapida aperti per la tabella Movie](sql/_static/design.png)

  ![Tabelle Movie aperte nella finestra di progettazione](sql/_static/dv.png)

<span data-ttu-id="8334b-210">Si noti l'icona a forma di chiave accanto a `ID`.</span><span class="sxs-lookup"><span data-stu-id="8334b-210">Note the key icon next to `ID`.</span></span> <span data-ttu-id="8334b-211">Per impostazione predefinita, Entity Framework crea una proprietà denominata `ID` per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="8334b-211">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="8334b-212">Fare clic con il pulsante destro del mouse sulla `Movie` tabella e selezionare **Visualizza dati** :</span><span class="sxs-lookup"><span data-stu-id="8334b-212">Right-click on the `Movie` table and select **View Data** :</span></span>

  ![Tabella Movie aperta con i dati della tabella](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8334b-214">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8334b-214">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="8334b-215">SQLite</span><span class="sxs-lookup"><span data-stu-id="8334b-215">SQLite</span></span>

<span data-ttu-id="8334b-216">Nel sito Web di [SQLite](https://www.sqlite.org/) si dichiara:</span><span class="sxs-lookup"><span data-stu-id="8334b-216">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="8334b-217">SQLite è un motore di database SQL autonomo, a elevata affidabilità, incorporato, completo e di dominio pubblico.</span><span class="sxs-lookup"><span data-stu-id="8334b-217">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="8334b-218">SQLite è il motore di database più usato in tutto il mondo.</span><span class="sxs-lookup"><span data-stu-id="8334b-218">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="8334b-219">Sono disponibili molti strumenti di terze parti che è possibile scaricare per gestire e visualizzare un database SQLite.</span><span class="sxs-lookup"><span data-stu-id="8334b-219">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="8334b-220">L'immagine seguente è stata tratta da [DB Browser per SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="8334b-220">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="8334b-221">Se si preferisce uno strumento SQLite in particolare, aggiungere un commento sui motivi della preferenza.</span><span class="sxs-lookup"><span data-stu-id="8334b-221">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![Browser DB per SQLite con database di film](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="8334b-223">Per questa esercitazione, viene usata la funzionalità *migrazioni* di Entity Framework Core, laddove possibile.</span><span class="sxs-lookup"><span data-stu-id="8334b-223">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="8334b-224">Le migrazioni aggiornano lo schema del database in base alle modifiche nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="8334b-224">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="8334b-225">Tuttavia, le migrazioni possono eseguire solo i tipi di modifiche supportate dal provider EF Core e le funzionalità del provider SQLite sono limitate.</span><span class="sxs-lookup"><span data-stu-id="8334b-225">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="8334b-226">Ad esempio l'aggiunta di una colonna è supportata, ma la rimozione o la modifica di una colonna non è supportata.</span><span class="sxs-lookup"><span data-stu-id="8334b-226">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="8334b-227">Se si crea una migrazione per rimuovere o modificare una colonna, il comando `ef migrations add` ha esito positivo, ma il comando `ef database update` ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="8334b-227">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="8334b-228">A causa di queste limitazioni, in questa esercitazione non vengono usate le migrazioni per le modifiche dello schema di SQLite.</span><span class="sxs-lookup"><span data-stu-id="8334b-228">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="8334b-229">Al contrario, quando lo schema viene modificato, il database viene eliminato e ricreato.</span><span class="sxs-lookup"><span data-stu-id="8334b-229">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="8334b-230">La soluzione per ovviare alle limitazioni di SQLite consiste nello scrivere manualmente il codice delle migrazioni per eseguire una ricompilazione della tabella in caso di modifiche nella tabella.</span><span class="sxs-lookup"><span data-stu-id="8334b-230">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="8334b-231">La ricompilazione della tabella comporta:</span><span class="sxs-lookup"><span data-stu-id="8334b-231">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="8334b-232">Creazione di una nuova tabella.</span><span class="sxs-lookup"><span data-stu-id="8334b-232">Creating a new table.</span></span>
>* <span data-ttu-id="8334b-233">Copia dei dati dalla vecchia tabella alla nuova tabella.</span><span class="sxs-lookup"><span data-stu-id="8334b-233">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="8334b-234">Eliminazione della tabella precedente.</span><span class="sxs-lookup"><span data-stu-id="8334b-234">Dropping the old table.</span></span>
>* <span data-ttu-id="8334b-235">Ridenominazione della nuova tabella.</span><span class="sxs-lookup"><span data-stu-id="8334b-235">Renaming the new table.</span></span>
>
><span data-ttu-id="8334b-236">Per altre informazioni, vedere le risorse seguenti:</span><span class="sxs-lookup"><span data-stu-id="8334b-236">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="8334b-237">Limitazioni del provider di database SQLite per EF Core</span><span class="sxs-lookup"><span data-stu-id="8334b-237">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="8334b-238">Personalizzare il codice di migrazione</span><span class="sxs-lookup"><span data-stu-id="8334b-238">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="8334b-239">Seeding dei dati</span><span class="sxs-lookup"><span data-stu-id="8334b-239">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="8334b-240">Istruzione ALTER TABLE di SQLite</span><span class="sxs-lookup"><span data-stu-id="8334b-240">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="8334b-241">Specificare il valore di inizializzazione del database</span><span class="sxs-lookup"><span data-stu-id="8334b-241">Seed the database</span></span>

<span data-ttu-id="8334b-242">:::no-loc(Create)::: nuova classe denominata `SeedData` nella cartella *Models* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8334b-242">:::no-loc(Create)::: a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="8334b-243">Se sono presenti film nel database, l'inizializzatore del valore di inizializzazione viene restituito e non viene aggiunto alcun film.</span><span class="sxs-lookup"><span data-stu-id="8334b-243">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="8334b-244">Aggiungere l'inizializzatore del valore di inizializzazione</span><span class="sxs-lookup"><span data-stu-id="8334b-244">Add the seed initializer</span></span>

<span data-ttu-id="8334b-245">Sostituire il contenuto di *Program.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8334b-245">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Program.cs)]

<span data-ttu-id="8334b-246">Nel codice precedente, il `Main` metodo è stato modificato per eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="8334b-246">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="8334b-247">Ottenere un'istanza del contesto di database dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8334b-247">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="8334b-248">Chiamare il `seedData.Initialize` metodo passando all'istanza del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="8334b-248">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="8334b-249">Eliminare il contesto dopo che il metodo di inizializzazione è stato completato.</span><span class="sxs-lookup"><span data-stu-id="8334b-249">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="8334b-250">L' [istruzione using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) garantisce che il contesto venga eliminato.</span><span class="sxs-lookup"><span data-stu-id="8334b-250">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="8334b-251">Quando `Update-Database` non è stato eseguito, si verifica l'eccezione seguente:</span><span class="sxs-lookup"><span data-stu-id="8334b-251">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="8334b-252">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="8334b-252">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8334b-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8334b-253">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8334b-254">:::no-loc(Delete)::: tutti i record del database.</span><span class="sxs-lookup"><span data-stu-id="8334b-254">:::no-loc(Delete)::: all the records in the database.</span></span> <span data-ttu-id="8334b-255">Usare i collegamenti di eliminazione nel browser o da [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span><span class="sxs-lookup"><span data-stu-id="8334b-255">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span></span>
* <span data-ttu-id="8334b-256">Forzare l'inizializzazione dell'app chiamando i metodi nella `Startup` classe, in modo che venga eseguito il metodo di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="8334b-256">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="8334b-257">Per forzare l'inizializzazione, IIS Express deve essere arrestato e riavviato.</span><span class="sxs-lookup"><span data-stu-id="8334b-257">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="8334b-258">Arrestare e riavviare IIS con uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="8334b-258">Stop and restart IIS with any of the following approaches:</span></span>

  * <span data-ttu-id="8334b-259">Fare clic con il pulsante destro del mouse sull'icona dell'area di notifica di IIS Express e toccare **Esci** o **Arresta sito** :</span><span class="sxs-lookup"><span data-stu-id="8334b-259">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site** :</span></span>

    ![Icona dell'area di notifica di IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu di scelta rapida](sql/_static/stopIIS.png)

    * <span data-ttu-id="8334b-262">Se l'app è in esecuzione in modalità non di debug, premere <kbd>F5</kbd> per l'esecuzione in modalità di debug.</span><span class="sxs-lookup"><span data-stu-id="8334b-262">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="8334b-263">Se l'app è in modalità di debug, arrestare il debugger e premere <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="8334b-263">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8334b-264">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8334b-264">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="8334b-265">:::no-loc(Delete)::: tutti i record nel database, in modo che venga eseguito il metodo di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="8334b-265">:::no-loc(Delete)::: all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="8334b-266">Arrestare e avviare l'app per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="8334b-266">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="8334b-267">L'app visualizza i dati sottoposti a seed:</span><span class="sxs-lookup"><span data-stu-id="8334b-267">The app shows the seeded data:</span></span>

![App per i film aperta in Chrome con i dati sui film](sql/_static/m55https.png)

## <a name="additional-resources"></a><span data-ttu-id="8334b-269">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="8334b-269">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="8334b-270">[Precedente: con ponteggi :::no-loc(Razor)::: Pagine](xref:tutorials/razor-pages/page) 
>  [successive: aggiornare le pagine](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="8334b-270">[Previous: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8334b-271">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="8334b-271">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="8334b-272">L'oggetto `:::no-loc(Razor):::PagesMovieContext` gestisce l'attività di connessione al database e di mapping degli oggetti `Movie` ai record di database.</span><span class="sxs-lookup"><span data-stu-id="8334b-272">The `:::no-loc(Razor):::PagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="8334b-273">Il contesto del database viene registrato con il contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) nel metodo `ConfigureServices` di *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="8334b-273">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs* :</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8334b-274">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8334b-274">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8334b-275">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8334b-275">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="8334b-276">Per altre informazioni sui metodi usati in `ConfigureServices`, vedere:</span><span class="sxs-lookup"><span data-stu-id="8334b-276">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="8334b-277">[Supporto per il Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea in ASP.NET Core](xref:security/gdpr) per `:::no-loc(Cookie):::PolicyOptions`.</span><span class="sxs-lookup"><span data-stu-id="8334b-277">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `:::no-loc(Cookie):::PolicyOptions`.</span></span>
* [<span data-ttu-id="8334b-278">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="8334b-278">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="8334b-279">La chiave viene letta dal sistema di [configurazione](xref:fundamentals/configuration/index) ASP.NET Core `ConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="8334b-279">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="8334b-280">Per lo sviluppo locale, la configurazione ottiene la stringa di connessione dal *:::no-loc(appsettings.json):::* file.</span><span class="sxs-lookup"><span data-stu-id="8334b-280">For local development, configuration gets the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8334b-281">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8334b-281">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8334b-282">La stringa di connessione generata sarà simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="8334b-282">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/:::no-loc(appsettings.json):::)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8334b-283">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8334b-283">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8334b-284">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8334b-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="8334b-285">Quando l'app viene distribuita in un server di test o di produzione, è possibile usare una variabile di ambiente per impostare la stringa di connessione su un server di database di test o di produzione.</span><span class="sxs-lookup"><span data-stu-id="8334b-285">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="8334b-286">Per altre informazioni, vedere [Configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="8334b-286">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8334b-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8334b-287">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="8334b-288">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="8334b-288">SQL Server Express LocalDB</span></span>

<span data-ttu-id="8334b-289">Local DB è una versione leggera del motore di database di SQL Server Express appositamente pensato per lo sviluppo di programmi.</span><span class="sxs-lookup"><span data-stu-id="8334b-289">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="8334b-290">Local DB viene avviato su richiesta ed eseguito in modalità utente; non richiede quindi una configurazione complessa.</span><span class="sxs-lookup"><span data-stu-id="8334b-290">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="8334b-291">Per impostazione predefinita, Local DB crea file con estensione `*.mdf` nella directory `C:/Users/<user/>`.</span><span class="sxs-lookup"><span data-stu-id="8334b-291">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="8334b-292">Dal menu **Visualizzazione** aprire **Esplora oggetti di SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="8334b-292">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu Visualizza](sql/_static/ssox.png)

* <span data-ttu-id="8334b-294">Fare clic con il pulsante destro del mouse sulla `Movie` tabella e scegliere **Visualizza finestra di progettazione** :</span><span class="sxs-lookup"><span data-stu-id="8334b-294">Right-click on the `Movie` table and select **View Designer** :</span></span>

  ![Menu di scelta rapida aperto per la tabella Movie](sql/_static/design.png)

  ![Tabella Movie aperta nella finestra di progettazione](sql/_static/dv.png)

<span data-ttu-id="8334b-297">Si noti l'icona a forma di chiave accanto a `ID`.</span><span class="sxs-lookup"><span data-stu-id="8334b-297">Note the key icon next to `ID`.</span></span> <span data-ttu-id="8334b-298">Per impostazione predefinita, Entity Framework crea una proprietà denominata `ID` per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="8334b-298">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="8334b-299">Fare clic con il pulsante destro del mouse sulla `Movie` tabella e selezionare **Visualizza dati** :</span><span class="sxs-lookup"><span data-stu-id="8334b-299">Right-click on the `Movie` table and select **View Data** :</span></span>

  ![Tabella Movie aperta con i dati della tabella](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="8334b-301">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8334b-301">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8334b-302">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8334b-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="8334b-303">Specificare il valore di inizializzazione del database</span><span class="sxs-lookup"><span data-stu-id="8334b-303">Seed the database</span></span>

<span data-ttu-id="8334b-304">:::no-loc(Create)::: nuova classe denominata `SeedData` nella cartella *Models* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8334b-304">:::no-loc(Create)::: a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="8334b-305">Se sono presenti film nel database, l'inizializzatore del valore di inizializzazione viene restituito e non viene aggiunto alcun film.</span><span class="sxs-lookup"><span data-stu-id="8334b-305">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="8334b-306">Aggiungere l'inizializzatore del valore di inizializzazione</span><span class="sxs-lookup"><span data-stu-id="8334b-306">Add the seed initializer</span></span>

<span data-ttu-id="8334b-307">Sostituire il contenuto di *Program.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8334b-307">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Program.cs)]

<span data-ttu-id="8334b-308">Nel codice precedente, il `Main` metodo è stato modificato per eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="8334b-308">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="8334b-309">Ottenere un'istanza del contesto di database dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8334b-309">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="8334b-310">Chiamare il `seedData.Initialize` metodo passando all'istanza del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="8334b-310">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="8334b-311">Eliminare il contesto dopo che il metodo di inizializzazione è stato completato.</span><span class="sxs-lookup"><span data-stu-id="8334b-311">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="8334b-312">L' [istruzione using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) garantisce che il contesto venga eliminato.</span><span class="sxs-lookup"><span data-stu-id="8334b-312">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="8334b-313">Un'app di produzione non chiamerà `Database.Migrate`.</span><span class="sxs-lookup"><span data-stu-id="8334b-313">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="8334b-314">Sarà aggiunto al codice precedente per evitare l'eccezione seguente quando `Update-Database` non è stato eseguito:</span><span class="sxs-lookup"><span data-stu-id="8334b-314">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="8334b-315">SqlException: Impossibile aprire il database " :::no-loc(Razor)::: PagesMovieContext-21" richiesto dall'account di accesso.</span><span class="sxs-lookup"><span data-stu-id="8334b-315">SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="8334b-316">Accesso non riuscito.</span><span class="sxs-lookup"><span data-stu-id="8334b-316">The login failed.</span></span>
<span data-ttu-id="8334b-317">Accesso non riuscito per l'utente "nome-utente".</span><span class="sxs-lookup"><span data-stu-id="8334b-317">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="8334b-318">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="8334b-318">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8334b-319">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8334b-319">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8334b-320">:::no-loc(Delete)::: tutti i record del database.</span><span class="sxs-lookup"><span data-stu-id="8334b-320">:::no-loc(Delete)::: all the records in the database.</span></span> <span data-ttu-id="8334b-321">Questa operazione può essere eseguita con i collegamenti di eliminazione nel browser o da [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="8334b-321">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="8334b-322">Forzare l'inizializzazione dell'app chiamando i metodi nella `Startup` classe, in modo che venga eseguito il metodo di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="8334b-322">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="8334b-323">Per forzare l'inizializzazione, IIS Express deve essere arrestato e riavviato.</span><span class="sxs-lookup"><span data-stu-id="8334b-323">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="8334b-324">È possibile eseguire questa operazione adottando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="8334b-324">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="8334b-325">Fare clic con il pulsante destro del mouse sull'icona dell'area di notifica di IIS Express e toccare **Esci** o **Arresta sito** :</span><span class="sxs-lookup"><span data-stu-id="8334b-325">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site** :</span></span>

    ![Icona dell'area di notifica di IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu di scelta rapida](sql/_static/stopIIS.png)

    * <span data-ttu-id="8334b-328">Se l'app è in esecuzione in modalità non di debug, premere <kbd>F5</kbd> per l'esecuzione in modalità di debug.</span><span class="sxs-lookup"><span data-stu-id="8334b-328">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="8334b-329">Se l'app è in modalità di debug, arrestare il debugger e premere <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="8334b-329">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8334b-330">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8334b-330">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8334b-331">:::no-loc(Delete)::: tutti i record nel database, in modo che venga eseguito il metodo di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="8334b-331">:::no-loc(Delete)::: all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="8334b-332">Arrestare e avviare l'app per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="8334b-332">Stop and start the app to seed the database.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8334b-333">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8334b-333">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8334b-334">:::no-loc(Delete)::: tutti i record nel database, in modo che venga eseguito il metodo di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="8334b-334">:::no-loc(Delete)::: all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="8334b-335">Arrestare e avviare l'app per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="8334b-335">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="8334b-336">L'app visualizza i dati sottoposti a seed:</span><span class="sxs-lookup"><span data-stu-id="8334b-336">The app shows the seeded data:</span></span>

![App per i film aperta in Chrome con i dati sui film](sql/_static/m55https.png)

<span data-ttu-id="8334b-338">L'esercitazione successiva consentirà di pulire la presentazione dei dati.</span><span class="sxs-lookup"><span data-stu-id="8334b-338">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8334b-339">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="8334b-339">Additional resources</span></span>

* [<span data-ttu-id="8334b-340">Versione YouTube dell'esercitazione</span><span class="sxs-lookup"><span data-stu-id="8334b-340">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="8334b-341">[Precedente: con ponteggi :::no-loc(Razor)::: Pagine](xref:tutorials/razor-pages/page) 
>  [successive: aggiornare le pagine](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="8334b-341">[Previous: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
