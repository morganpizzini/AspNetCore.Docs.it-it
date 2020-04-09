---
title: Usare un database e ASP.NET Core
author: rick-anderson
description: Questa sezione illustra l'utilizzo di un database e di ASP.NET Core.
ms.author: riande
ms.date: 7/22/2019
uid: tutorials/razor-pages/sql
ms.openlocfilehash: b5acb573f8fa39e5300ecdb359113d8697d78934
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664339"
---
# <a name="work-with-a-database-and-aspnet-core"></a><span data-ttu-id="2a0c1-103">Usare un database e ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2a0c1-103">Work with a database and ASP.NET Core</span></span>

<span data-ttu-id="2a0c1-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="2a0c1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="2a0c1-105">L'oggetto `RazorPagesMovieContext` gestisce l'attività di connessione al database e di mapping degli oggetti `Movie` ai record di database.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-105">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="2a0c1-106">Il contesto del database viene registrato con il contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) nel metodo `ConfigureServices` di *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2a0c1-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a0c1-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a0c1-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2a0c1-108">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="2a0c1-108">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="2a0c1-109">Il sistema di [configurazione](xref:fundamentals/configuration/index) di ASP.NET Core legge la `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-109">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="2a0c1-110">Per lo sviluppo locale, ottiene la stringa di connessione dal file *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="2a0c1-110">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a0c1-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a0c1-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2a0c1-112">Il valore del nome per il database (`Database={Database name}`) sarà diverso per il codice generato.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-112">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="2a0c1-113">Il valore del nome è arbitrario.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-113">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2a0c1-114">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="2a0c1-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="2a0c1-115">Quando l'app viene distribuita in un server di test o produzione, è possibile utilizzare una variabile di ambiente per impostare la stringa di connessione su un server di database reale.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="2a0c1-116">Per altre informazioni, vedere [Configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="2a0c1-116">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a0c1-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a0c1-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="2a0c1-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="2a0c1-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="2a0c1-119">Local DB è una versione leggera del motore di database di SQL Server Express appositamente pensato per lo sviluppo di programmi.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="2a0c1-120">Local DB viene avviato su richiesta ed eseguito in modalità utente; non richiede quindi una configurazione complessa.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="2a0c1-121">Per impostazione predefinita, Local DB crea file con estensione `*.mdf` nella directory `C:\Users\<user>\`.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="2a0c1-122">Dal menu **Visualizzazione** aprire **Esplora oggetti di SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="2a0c1-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu Visualizza](sql/_static/ssox.png)

* <span data-ttu-id="2a0c1-124">Fare clic con il pulsante destro del mouse sulla tabella `Movie` e selezionare**Progettazione viste**:</span><span class="sxs-lookup"><span data-stu-id="2a0c1-124">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Menu di scelta rapida aperti per la tabella Movie](sql/_static/design.png)

  ![Tabelle Movie aperte nella finestra di progettazione](sql/_static/dv.png)

<span data-ttu-id="2a0c1-127">Si noti l'icona a forma di chiave accanto a `ID`.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="2a0c1-128">Per impostazione predefinita, Entity Framework crea una proprietà denominata `ID` per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-128">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="2a0c1-129">Fare clic con il pulsante destro del mouse sulla tabella `Movie` e selezionare**Visualizza dati**:</span><span class="sxs-lookup"><span data-stu-id="2a0c1-129">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Tabella Movie aperta con i dati della tabella](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2a0c1-131">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="2a0c1-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="2a0c1-132">Specificare il valore di inizializzazione del database</span><span class="sxs-lookup"><span data-stu-id="2a0c1-132">Seed the database</span></span>

<span data-ttu-id="2a0c1-133">Creare una nuova classe denominata `SeedData` nella cartella *Models* usando il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="2a0c1-133">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="2a0c1-134">Se sono presenti eventuali film nel database, l'inizializzatore del valore di inizializzazione viene restituito e non vengono aggiunti film.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="2a0c1-135">Aggiungere l'inizializzatore del valore di inizializzazione</span><span class="sxs-lookup"><span data-stu-id="2a0c1-135">Add the seed initializer</span></span>

<span data-ttu-id="2a0c1-136">In *Program.cs* modificare il metodo `Main` per eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="2a0c1-136">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="2a0c1-137">Ottenere un'istanza del contesto di database dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-137">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="2a0c1-138">Chiamare il metodo di inizializzazione, passandolo al contesto.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-138">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="2a0c1-139">Eliminare il contesto dopo che il metodo di inizializzazione è stato completato.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-139">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="2a0c1-140">Il codice seguente illustra il file *Program.cs* aggiornato.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-140">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="2a0c1-141">La seguente eccezione si verifica quando `Update-Database` non è stata eseguita:</span><span class="sxs-lookup"><span data-stu-id="2a0c1-141">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="2a0c1-142">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="2a0c1-142">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a0c1-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a0c1-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a0c1-144">Eliminare tutti i record nel database.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-144">Delete all the records in the DB.</span></span> <span data-ttu-id="2a0c1-145">È possibile farlo con i collegamenti di eliminazione nel browser o da [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="2a0c1-145">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="2a0c1-146">Forzare l'inizializzazione dell'app (chiamare i metodi nella classe `Startup`) in modo che venga eseguito il metodo di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-146">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="2a0c1-147">Per forzare l'inizializzazione, IIS Express deve essere arrestato e riavviato.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-147">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="2a0c1-148">È possibile eseguire questa operazione adottando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="2a0c1-148">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="2a0c1-149">Fare clic con il pulsante destro del mouse sull'icona della barra delle applicazioni iIS Express nell'area di notifica e toccare **Esci** o **Arresta sito**:</span><span class="sxs-lookup"><span data-stu-id="2a0c1-149">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Icona dell'area di notifica di IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu di scelta rapida](sql/_static/stopIIS.png)

    * <span data-ttu-id="2a0c1-152">Se Visual Studio è in esecuzione in modalità non di debug, premere F5 per attivare la modalità di debug.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-152">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="2a0c1-153">Se Visual Studio è in esecuzione in modalità di debug, arrestare il debugger e premere F5.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-153">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2a0c1-154">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="2a0c1-154">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="2a0c1-155">Eliminare tutti i record del database; verrà quindi eseguito il metodo di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-155">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="2a0c1-156">Arrestare e avviare l'app per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-156">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="2a0c1-157">L'app mostra i dati inizializzati.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-157">The app shows the seeded data.</span></span>

---

<span data-ttu-id="2a0c1-158">L'esercitazione successiva consentirà di migliorare la presentazione dei dati.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-158">The next tutorial will improve the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2a0c1-159">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2a0c1-159">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2a0c1-160">[Precedente: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
> [Next: Aggiornamento delle pagine](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="2a0c1-160">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="2a0c1-161">L'oggetto `RazorPagesMovieContext` gestisce l'attività di connessione al database e di mapping degli oggetti `Movie` ai record di database.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-161">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="2a0c1-162">Il contesto del database viene registrato con il contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) nel metodo `ConfigureServices` di *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2a0c1-162">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a0c1-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a0c1-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2a0c1-164">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="2a0c1-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="2a0c1-165">Per altre informazioni sui metodi usati in `ConfigureServices`, vedere:</span><span class="sxs-lookup"><span data-stu-id="2a0c1-165">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="2a0c1-166">[Supporto per il Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea in ASP.NET Core](xref:security/gdpr) per `CookiePolicyOptions`.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-166">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="2a0c1-167">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="2a0c1-167">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="2a0c1-168">Il sistema di [configurazione](xref:fundamentals/configuration/index) di ASP.NET Core legge la `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-168">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="2a0c1-169">Per lo sviluppo locale, ottiene la stringa di connessione dal file *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="2a0c1-169">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a0c1-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a0c1-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2a0c1-171">Il valore del nome per il database (`Database={Database name}`) sarà diverso per il codice generato.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-171">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="2a0c1-172">Il valore del nome è arbitrario.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-172">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a0c1-173">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a0c1-173">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a0c1-174">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="2a0c1-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="2a0c1-175">Quando l'app viene distribuita in un server di test o produzione, è possibile utilizzare una variabile di ambiente per impostare la stringa di connessione su un server di database reale.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-175">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="2a0c1-176">Per altre informazioni, vedere [Configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="2a0c1-176">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a0c1-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a0c1-177">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="2a0c1-178">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="2a0c1-178">SQL Server Express LocalDB</span></span>

<span data-ttu-id="2a0c1-179">Local DB è una versione leggera del motore di database di SQL Server Express appositamente pensato per lo sviluppo di programmi.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-179">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="2a0c1-180">Local DB viene avviato su richiesta ed eseguito in modalità utente; non richiede quindi una configurazione complessa.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-180">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="2a0c1-181">Per impostazione predefinita, Local DB crea file con estensione `*.mdf` nella directory `C:/Users/<user/>`.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-181">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="2a0c1-182">Dal menu **Visualizzazione** aprire **Esplora oggetti di SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="2a0c1-182">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu Visualizza](sql/_static/ssox.png)

* <span data-ttu-id="2a0c1-184">Fare clic con il pulsante destro del mouse sulla tabella `Movie` e selezionare**Progettazione viste**:</span><span class="sxs-lookup"><span data-stu-id="2a0c1-184">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Menu di scelta rapida aperto per la tabella Movie](sql/_static/design.png)

  ![Tabella Movie aperta nella finestra di progettazione](sql/_static/dv.png)

<span data-ttu-id="2a0c1-187">Si noti l'icona a forma di chiave accanto a `ID`.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-187">Note the key icon next to `ID`.</span></span> <span data-ttu-id="2a0c1-188">Per impostazione predefinita, Entity Framework crea una proprietà denominata `ID` per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-188">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="2a0c1-189">Fare clic con il pulsante destro del mouse sulla tabella `Movie` e selezionare**Visualizza dati**:</span><span class="sxs-lookup"><span data-stu-id="2a0c1-189">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Tabella Movie aperta con i dati della tabella](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a0c1-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a0c1-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a0c1-192">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="2a0c1-192">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="2a0c1-193">Specificare il valore di inizializzazione del database</span><span class="sxs-lookup"><span data-stu-id="2a0c1-193">Seed the database</span></span>

<span data-ttu-id="2a0c1-194">Creare una nuova classe denominata `SeedData` nella cartella *Models* usando il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="2a0c1-194">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="2a0c1-195">Se sono presenti eventuali film nel database, l'inizializzatore del valore di inizializzazione viene restituito e non vengono aggiunti film.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-195">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="2a0c1-196">Aggiungere l'inizializzatore del valore di inizializzazione</span><span class="sxs-lookup"><span data-stu-id="2a0c1-196">Add the seed initializer</span></span>

<span data-ttu-id="2a0c1-197">In *Program.cs* modificare il metodo `Main` per eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="2a0c1-197">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="2a0c1-198">Ottenere un'istanza del contesto di database dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-198">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="2a0c1-199">Chiamare il metodo di inizializzazione, passandolo al contesto.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-199">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="2a0c1-200">Eliminare il contesto dopo che il metodo di inizializzazione è stato completato.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-200">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="2a0c1-201">Il codice seguente illustra il file *Program.cs* aggiornato.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-201">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="2a0c1-202">Un'app di produzione non chiamerà `Database.Migrate`.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-202">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="2a0c1-203">Sarà aggiunto al codice precedente per evitare l'eccezione seguente quando `Update-Database` non è stato eseguito:</span><span class="sxs-lookup"><span data-stu-id="2a0c1-203">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="2a0c1-204">SqlException: Impossibile aprire il database "RazorPagesMovieContext-21" richiesto dall'account di accesso.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-204">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="2a0c1-205">Accesso non riuscito.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-205">The login failed.</span></span>
<span data-ttu-id="2a0c1-206">Accesso non riuscito per l'utente "nome-utente".</span><span class="sxs-lookup"><span data-stu-id="2a0c1-206">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="2a0c1-207">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="2a0c1-207">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a0c1-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a0c1-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a0c1-209">Eliminare tutti i record nel database.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-209">Delete all the records in the DB.</span></span> <span data-ttu-id="2a0c1-210">È possibile farlo con i collegamenti di eliminazione nel browser o da [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="2a0c1-210">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="2a0c1-211">Forzare l'inizializzazione dell'app (chiamare i metodi nella classe `Startup`) in modo che venga eseguito il metodo di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-211">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="2a0c1-212">Per forzare l'inizializzazione, IIS Express deve essere arrestato e riavviato.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-212">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="2a0c1-213">È possibile eseguire questa operazione adottando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="2a0c1-213">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="2a0c1-214">Fare clic con il pulsante destro del mouse sull'icona dell'area di notifica di IIS Express e toccare **Esci** o **Arresta sito**:</span><span class="sxs-lookup"><span data-stu-id="2a0c1-214">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Icona dell'area di notifica di IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu di scelta rapida](sql/_static/stopIIS.png)

    * <span data-ttu-id="2a0c1-217">Se Visual Studio è in esecuzione in modalità non di debug, premere F5 per attivare la modalità di debug.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-217">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="2a0c1-218">Se Visual Studio è in esecuzione in modalità di debug, arrestare il debugger e premere F5.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-218">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a0c1-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a0c1-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2a0c1-220">Eliminare tutti i record del database; verrà quindi eseguito il metodo di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-220">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="2a0c1-221">Arrestare e avviare l'app per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-221">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="2a0c1-222">L'app mostra i dati inizializzati.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-222">The app shows the seeded data.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a0c1-223">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="2a0c1-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2a0c1-224">Eliminare tutti i record del database; verrà quindi eseguito il metodo di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-224">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="2a0c1-225">Arrestare e avviare l'app per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-225">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="2a0c1-226">L'app mostra i dati inizializzati.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-226">The app shows the seeded data.</span></span>

---

<span data-ttu-id="2a0c1-227">L'app visualizza i dati sottoposti a seed:</span><span class="sxs-lookup"><span data-stu-id="2a0c1-227">The app shows the seeded data:</span></span>

![App per i film aperta in Chrome con i dati sui film](sql/_static/m55.png)

<span data-ttu-id="2a0c1-229">L'esercitazione successiva consentirà di pulire la presentazione dei dati.</span><span class="sxs-lookup"><span data-stu-id="2a0c1-229">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2a0c1-230">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2a0c1-230">Additional resources</span></span>

* [<span data-ttu-id="2a0c1-231">Versione YouTube dell'esercitazione</span><span class="sxs-lookup"><span data-stu-id="2a0c1-231">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="2a0c1-232">[Precedente: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
> [Next: Aggiornamento delle pagine](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="2a0c1-232">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
