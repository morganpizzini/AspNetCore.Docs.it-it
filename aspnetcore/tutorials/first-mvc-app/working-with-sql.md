---
title: Parte 5, usare un database in un'app MVC ASP.NET Core
author: rick-anderson
description: Parte 5 aggiungere un modello a un'app MVC ASP.NET Core
ms.author: riande
ms.date: 8/16/2019
no-loc:
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
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: 23ecee4e3482b3ee82ae597671a8c45515eb4589
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634930"
---
# <a name="part-5-work-with-a-database-in-an-aspnet-core-mvc-app"></a><span data-ttu-id="1bfa5-103">Parte 5, usare un database in un'app MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1bfa5-103">Part 5, work with a database in an ASP.NET Core MVC app</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1bfa5-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1bfa5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1bfa5-105">L'oggetto `MvcMovieContext` gestisce l'attività di connessione al database e di mapping degli oggetti `Movie` ai record di database.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-105">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="1bfa5-106">Il contesto del database viene registrato nel contenitore di [inserimento dipendenze](xref:fundamentals/dependency-injection) nel metodo `ConfigureServices` nel file *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="1bfa5-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bfa5-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bfa5-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="1bfa5-108">Il sistema di [configurazione](xref:fundamentals/configuration/index) di ASP.NET Core legge la `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-108">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="1bfa5-109">Per lo sviluppo locale ottiene la stringa di connessione dal file *appSettings.JSON*:</span><span class="sxs-lookup"><span data-stu-id="1bfa5-109">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="1bfa5-110">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="1bfa5-110">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

<span data-ttu-id="1bfa5-111">Il sistema di [configurazione](xref:fundamentals/configuration/index) di ASP.NET Core legge la `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-111">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="1bfa5-112">Per lo sviluppo locale ottiene la stringa di connessione dal file *appSettings.JSON*:</span><span class="sxs-lookup"><span data-stu-id="1bfa5-112">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="1bfa5-113">Quando l'app viene distribuita in un server di test o produzione, è possibile usare una variabile di ambiente per impostare la stringa di connessione su un'istanza di SQL Server di produzione.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-113">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a production SQL Server.</span></span> <span data-ttu-id="1bfa5-114">Per altre informazioni, vedere [Configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="1bfa5-114">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bfa5-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bfa5-115">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="1bfa5-116">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="1bfa5-116">SQL Server Express LocalDB</span></span>

<span data-ttu-id="1bfa5-117">Local DB è una versione leggera del motore di database di SQL Server Express progettata appositamente per lo sviluppo di programmi.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-117">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="1bfa5-118">Local DB viene avviato su richiesta ed eseguito in modalità utente; non richiede quindi una configurazione complessa.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-118">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="1bfa5-119">Per impostazione predefinita, il database Local DB crea i file con estensione *mdf* nella directory *C:/Utenti/{utente}*.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-119">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="1bfa5-120">Dal menu **Visualizzazione** aprire **Esplora oggetti di SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="1bfa5-120">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu Visualizza](working-with-sql/_static/ssox.png)

* <span data-ttu-id="1bfa5-122">Fare clic con il pulsante destro del mouse sulla tabella `Movie`**> Visualizza finestra di progettazione**</span><span class="sxs-lookup"><span data-stu-id="1bfa5-122">Right click on the `Movie` table **> View Designer**</span></span>

  ![Menu di scelta rapida aperto per la tabella Movie](working-with-sql/_static/design.png)

  ![Tabella Movie aperta nella finestra di progettazione](working-with-sql/_static/dv.png)

<span data-ttu-id="1bfa5-125">Si noti l'icona a forma di chiave accanto a `ID`.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-125">Note the key icon next to `ID`.</span></span> <span data-ttu-id="1bfa5-126">Per impostazione predefinita, Entity Framework userà una proprietà denominata `ID` come chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-126">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="1bfa5-127">Fare clic con il pulsante destro del mouse sulla tabella `Movie`**> Visualizza dati**</span><span class="sxs-lookup"><span data-stu-id="1bfa5-127">Right click on the `Movie` table **> View Data**</span></span>

  ![Menu di scelta rapida aperto per la tabella Movie](working-with-sql/_static/ssox2.png)

  ![Tabella Movie aperta con i dati della tabella](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="1bfa5-130">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="1bfa5-130">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="1bfa5-131">Specificare il valore di inizializzazione del database</span><span class="sxs-lookup"><span data-stu-id="1bfa5-131">Seed the database</span></span>

<span data-ttu-id="1bfa5-132">Creare una nuova classe denominata `SeedData` nella cartella *Models*.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-132">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="1bfa5-133">Sostituire il codice generato con il seguente:</span><span class="sxs-lookup"><span data-stu-id="1bfa5-133">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="1bfa5-134">Se sono presenti eventuali film nel database, l'inizializzatore del valore di inizializzazione viene restituito e non vengono aggiunti film.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="1bfa5-135">Aggiungere l'inizializzatore del valore di inizializzazione</span><span class="sxs-lookup"><span data-stu-id="1bfa5-135">Add the seed initializer</span></span>

<span data-ttu-id="1bfa5-136">Sostituire il contenuto di *Program.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="1bfa5-136">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

<span data-ttu-id="1bfa5-137">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="1bfa5-137">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bfa5-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bfa5-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1bfa5-139">Eliminare tutti i record nel database.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-139">Delete all the records in the DB.</span></span> <span data-ttu-id="1bfa5-140">È possibile eseguire questa operazione con i collegamenti di eliminazione nel browser o da SSOX.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-140">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="1bfa5-141">Forzare l'inizializzazione dell'app (chiamare i metodi nella classe `Startup`) in modo che venga eseguito il metodo di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-141">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="1bfa5-142">Per forzare l'inizializzazione, IIS Express deve essere arrestato e riavviato.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-142">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="1bfa5-143">È possibile eseguire questa operazione adottando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="1bfa5-143">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="1bfa5-144">Fare clic con il pulsante destro del mouse sull'icona dell'area di notifica di IIS Express e toccare **Esci** o **Arresta sito**</span><span class="sxs-lookup"><span data-stu-id="1bfa5-144">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![Icona dell'area di notifica di IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Menu di scelta rapida](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="1bfa5-147">Se Visual Studio è in esecuzione in modalità non di debug, premere F5 per attivare la modalità di debug</span><span class="sxs-lookup"><span data-stu-id="1bfa5-147">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="1bfa5-148">Se Visual Studio è in esecuzione in modalità di debug, arrestare il debugger e premere F5</span><span class="sxs-lookup"><span data-stu-id="1bfa5-148">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="1bfa5-149">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="1bfa5-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="1bfa5-150">Eliminare tutti i record del database; verrà quindi eseguito il metodo di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-150">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="1bfa5-151">Arrestare e avviare l'app per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-151">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="1bfa5-152">L'app mostra i dati inizializzati.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-152">The app shows the seeded data.</span></span>

![App per i film MVC aperta in Microsoft Edge con i dati sui film](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="1bfa5-154">[Precedente](adding-model.md) 
>  [Avanti](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="1bfa5-154">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1bfa5-155">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1bfa5-155">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1bfa5-156">L'oggetto `MvcMovieContext` gestisce l'attività di connessione al database e di mapping degli oggetti `Movie` ai record di database.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-156">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="1bfa5-157">Il contesto del database viene registrato nel contenitore di [inserimento dipendenze](xref:fundamentals/dependency-injection) nel metodo `ConfigureServices` nel file *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="1bfa5-157">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bfa5-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bfa5-158">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="1bfa5-159">Il sistema di [configurazione](xref:fundamentals/configuration/index) di ASP.NET Core legge la `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-159">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="1bfa5-160">Per lo sviluppo locale ottiene la stringa di connessione dal file *appSettings.JSON*:</span><span class="sxs-lookup"><span data-stu-id="1bfa5-160">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="1bfa5-161">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="1bfa5-161">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="1bfa5-162">Il sistema di [configurazione](xref:fundamentals/configuration/index) di ASP.NET Core legge la `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-162">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="1bfa5-163">Per lo sviluppo locale ottiene la stringa di connessione dal file *appSettings.JSON*:</span><span class="sxs-lookup"><span data-stu-id="1bfa5-163">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="1bfa5-164">Quando si distribuisce l'app in un server di test o di produzione, è possibile usare una variabile di ambiente o un altro approccio per impostare la stringa di connessione su un SQL Server reale.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-164">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="1bfa5-165">Per altre informazioni, vedere [Configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="1bfa5-165">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bfa5-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bfa5-166">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="1bfa5-167">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="1bfa5-167">SQL Server Express LocalDB</span></span>

<span data-ttu-id="1bfa5-168">Local DB è una versione leggera del motore di database di SQL Server Express progettata appositamente per lo sviluppo di programmi.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-168">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="1bfa5-169">Local DB viene avviato su richiesta ed eseguito in modalità utente; non richiede quindi una configurazione complessa.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-169">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="1bfa5-170">Per impostazione predefinita, il database Local DB crea i file con estensione *mdf* nella directory *C:/Utenti/{utente}*.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-170">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="1bfa5-171">Dal menu **Visualizzazione** aprire **Esplora oggetti di SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="1bfa5-171">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu Visualizza](working-with-sql/_static/ssox.png)

* <span data-ttu-id="1bfa5-173">Fare clic con il pulsante destro del mouse sulla tabella `Movie`**> Visualizza finestra di progettazione**</span><span class="sxs-lookup"><span data-stu-id="1bfa5-173">Right click on the `Movie` table **> View Designer**</span></span>

  ![Menu di scelta rapida aperto per la tabella Movie](working-with-sql/_static/design.png)

  ![Tabella Movie aperta nella finestra di progettazione](working-with-sql/_static/dv.png)

<span data-ttu-id="1bfa5-176">Si noti l'icona a forma di chiave accanto a `ID`.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-176">Note the key icon next to `ID`.</span></span> <span data-ttu-id="1bfa5-177">Per impostazione predefinita, Entity Framework userà una proprietà denominata `ID` come chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-177">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="1bfa5-178">Fare clic con il pulsante destro del mouse sulla tabella `Movie`**> Visualizza dati**</span><span class="sxs-lookup"><span data-stu-id="1bfa5-178">Right click on the `Movie` table **> View Data**</span></span>

  ![Menu di scelta rapida aperto per la tabella Movie](working-with-sql/_static/ssox2.png)

  ![Tabella Movie aperta con i dati della tabella](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="1bfa5-181">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="1bfa5-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="1bfa5-182">Specificare il valore di inizializzazione del database</span><span class="sxs-lookup"><span data-stu-id="1bfa5-182">Seed the database</span></span>

<span data-ttu-id="1bfa5-183">Creare una nuova classe denominata `SeedData` nella cartella *Models*.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-183">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="1bfa5-184">Sostituire il codice generato con il seguente:</span><span class="sxs-lookup"><span data-stu-id="1bfa5-184">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="1bfa5-185">Se sono presenti eventuali film nel database, l'inizializzatore del valore di inizializzazione viene restituito e non vengono aggiunti film.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-185">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="1bfa5-186">Aggiungere l'inizializzatore del valore di inizializzazione</span><span class="sxs-lookup"><span data-stu-id="1bfa5-186">Add the seed initializer</span></span>

<span data-ttu-id="1bfa5-187">Sostituire il contenuto di *Program.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="1bfa5-187">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

<span data-ttu-id="1bfa5-188">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="1bfa5-188">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bfa5-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bfa5-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1bfa5-190">Eliminare tutti i record nel database.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-190">Delete all the records in the DB.</span></span> <span data-ttu-id="1bfa5-191">È possibile eseguire questa operazione con i collegamenti di eliminazione nel browser o da SSOX.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-191">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="1bfa5-192">Forzare l'inizializzazione dell'app (chiamare i metodi nella classe `Startup`) in modo che venga eseguito il metodo di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-192">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="1bfa5-193">Per forzare l'inizializzazione, IIS Express deve essere arrestato e riavviato.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-193">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="1bfa5-194">È possibile eseguire questa operazione adottando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="1bfa5-194">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="1bfa5-195">Fare clic con il pulsante destro del mouse sull'icona dell'area di notifica di IIS Express e toccare **Esci** o **Arresta sito**</span><span class="sxs-lookup"><span data-stu-id="1bfa5-195">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![Icona dell'area di notifica di IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Menu di scelta rapida](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="1bfa5-198">Se Visual Studio è in esecuzione in modalità non di debug, premere F5 per attivare la modalità di debug</span><span class="sxs-lookup"><span data-stu-id="1bfa5-198">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="1bfa5-199">Se Visual Studio è in esecuzione in modalità di debug, arrestare il debugger e premere F5</span><span class="sxs-lookup"><span data-stu-id="1bfa5-199">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="1bfa5-200">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="1bfa5-200">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="1bfa5-201">Eliminare tutti i record del database; verrà quindi eseguito il metodo di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-201">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="1bfa5-202">Arrestare e avviare l'app per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-202">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="1bfa5-203">L'app mostra i dati inizializzati.</span><span class="sxs-lookup"><span data-stu-id="1bfa5-203">The app shows the seeded data.</span></span>

![App per i film MVC aperta in Microsoft Edge con i dati sui film](working-with-sql/_static/m55_mac.png)

> [!div class="step-by-step"]
> <span data-ttu-id="1bfa5-205">[Precedente](adding-model.md) 
>  [Avanti](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="1bfa5-205">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>

::: moniker-end
