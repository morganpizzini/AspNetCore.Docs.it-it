---
title: "Parte 4: aggiungere un modello a un'app MVC ASP.NET Core"
author: rick-anderson
description: Parte 4 della serie di esercitazioni su ASP.NET Core MVC.
ms.author: riande
ms.date: 11/16/2020
no-loc:
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
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: bfda45afeea67a11ad775996d94a06125df08bc6
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854587"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="58c30-103">Parte 4: aggiungere un modello a un'app MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="58c30-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="58c30-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="58c30-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="58c30-105">In questa sezione si aggiungono alcune classi per la gestione di filmati in un database.</span><span class="sxs-lookup"><span data-stu-id="58c30-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="58c30-106">Queste classi saranno la parte "**M** odel" dell'app **M** VC.</span><span class="sxs-lookup"><span data-stu-id="58c30-106">These classes will be the "**M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="58c30-107">Si usano queste classi con [Entity Framework Core](/ef/core) (EF Core) per usare un database.</span><span class="sxs-lookup"><span data-stu-id="58c30-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="58c30-108">EF Core è un framework object-relational mapping (ORM) che semplifica il codice di accesso ai dati che è necessario scrivere.</span><span class="sxs-lookup"><span data-stu-id="58c30-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="58c30-109">Le classi di modello create sono dette classi POCO (da **P** lain **O** ld **C** LR **O** bjects) perché non hanno alcuna dipendenza in EF Core.</span><span class="sxs-lookup"><span data-stu-id="58c30-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="58c30-110">Definiscono semplicemente le proprietà dei dati che verranno archiviati nel database.</span><span class="sxs-lookup"><span data-stu-id="58c30-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="58c30-111">In questa esercitazione si scrivono innanzitutto le classi di modello e EF Core crea il database.</span><span class="sxs-lookup"><span data-stu-id="58c30-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="58c30-112">Aggiungere una classe del modello di dati</span><span class="sxs-lookup"><span data-stu-id="58c30-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58c30-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58c30-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58c30-114">Fare clic con il pulsante destro del mouse sulla cartella *Models* > **Aggiungi** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="58c30-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="58c30-115">Denominare il file *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="58c30-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="58c30-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58c30-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="58c30-117">Aggiungere un file denominato *Movie.cs* alla cartella *Models*.</span><span class="sxs-lookup"><span data-stu-id="58c30-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58c30-118">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="58c30-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="58c30-119">Fare clic con il pulsante destro del mouse sulla cartella *Models* > **Aggiungi**  >  **nuova classe**  >  **vuota** classe.</span><span class="sxs-lookup"><span data-stu-id="58c30-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="58c30-120">Denominare il file *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="58c30-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="58c30-121">Aggiornare il file *Movie.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="58c30-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="58c30-122">La classe `Movie` contiene un campo `Id`, richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="58c30-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="58c30-123">L' <xref:System.ComponentModel.DataAnnotations.DataType> attributo on `ReleaseDate` specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="58c30-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="58c30-124">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="58c30-124">With this attribute:</span></span>

* <span data-ttu-id="58c30-125">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="58c30-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="58c30-126">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="58c30-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="58c30-127">L'attributo [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="58c30-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="58c30-128">Aggiungere pacchetti NuGet</span><span class="sxs-lookup"><span data-stu-id="58c30-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58c30-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58c30-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58c30-130">Dal menu **Strumenti** scegliere **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="58c30-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menu della Console di Gestione pacchetti](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="58c30-132">Nella console di Gestione pacchetti eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="58c30-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="58c30-133">Il comando precedente aggiunge il provider EF Core SQL Server.</span><span class="sxs-lookup"><span data-stu-id="58c30-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="58c30-134">Il pacchetto del provider installa il pacchetto di EF Core come dipendenza.</span><span class="sxs-lookup"><span data-stu-id="58c30-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="58c30-135">I pacchetti aggiuntivi vengono installati automaticamente nel passaggio di scaffolding più avanti nell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="58c30-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="58c30-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58c30-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58c30-137">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="58c30-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="58c30-138">Scegliere **Gestisci pacchetti NuGet** dal menu **progetto** .</span><span class="sxs-lookup"><span data-stu-id="58c30-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="58c30-139">Nel campo di **ricerca** in alto a destra, immettere `Microsoft.EntityFrameworkCore.SQLite` e premere il tasto **invio** per eseguire la ricerca.</span><span class="sxs-lookup"><span data-stu-id="58c30-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="58c30-140">Selezionare il pacchetto NuGet corrispondente e premere il pulsante **Aggiungi pacchetto** .</span><span class="sxs-lookup"><span data-stu-id="58c30-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Aggiungi Entity Framework Core pacchetto NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="58c30-142">Verrà visualizzata la finestra di dialogo **Seleziona progetti** con il `MvcMovie` progetto selezionato.</span><span class="sxs-lookup"><span data-stu-id="58c30-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="58c30-143">Premere il pulsante **OK** .</span><span class="sxs-lookup"><span data-stu-id="58c30-143">Press the **Ok** button.</span></span>

<span data-ttu-id="58c30-144">Verrà visualizzata una finestra di dialogo **accettazione licenza** .</span><span class="sxs-lookup"><span data-stu-id="58c30-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="58c30-145">Esaminare le licenze nel modo desiderato, quindi fare clic sul pulsante **Accetto** .</span><span class="sxs-lookup"><span data-stu-id="58c30-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="58c30-146">Ripetere i passaggi precedenti per installare i pacchetti NuGet seguenti:</span><span class="sxs-lookup"><span data-stu-id="58c30-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

<span data-ttu-id="58c30-147">Eseguire il comando dell'interfaccia della riga di comando .NET seguente:</span><span class="sxs-lookup"><span data-stu-id="58c30-147">Run the following .NET CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-aspnet-codegenerator
```

<span data-ttu-id="58c30-148">Il comando precedente aggiunge lo [strumento di impalcatura ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="58c30-148">The preceding command adds the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="58c30-149">Creare una classe di contesto di database</span><span class="sxs-lookup"><span data-stu-id="58c30-149">Create a database context class</span></span>

<span data-ttu-id="58c30-150">Una classe di contesto di database è necessaria per coordinare le funzionalità di EF Core (creazione, lettura, aggiornamento, eliminazione) per il modello `Movie`.</span><span class="sxs-lookup"><span data-stu-id="58c30-150">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="58c30-151">Il contesto di database viene derivato da [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) e specifica le entità da includere nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="58c30-151">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="58c30-152">Creare una cartella *Data*.</span><span class="sxs-lookup"><span data-stu-id="58c30-152">Create a *Data* folder.</span></span>

<span data-ttu-id="58c30-153">Aggiungere un file *Data/MvcMovieContext.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="58c30-153">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="58c30-154">Il codice precedente crea una [proprietà \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="58c30-154">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="58c30-155">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="58c30-155">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="58c30-156">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="58c30-156">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="58c30-157">Registrare il contesto del database</span><span class="sxs-lookup"><span data-stu-id="58c30-157">Register the database context</span></span>

<span data-ttu-id="58c30-158">ASP.NET Core viene compilato con l'[inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="58c30-158">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="58c30-159">I servizi, ad esempio il contesto di database di EF Core, devono essere registrati per l'inserimento delle dipendenze durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="58c30-159">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="58c30-160">I componenti che richiedono questi servizi, ad esempio Razor le pagine, vengono forniti tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="58c30-160">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="58c30-161">Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="58c30-161">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="58c30-162">In questa sezione viene registrato il contesto di database per il contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="58c30-162">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="58c30-163">Aggiungere le istruzioni `using` seguenti all'inizio di *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="58c30-163">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="58c30-164">Aggiungere il codice evidenziato seguente in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="58c30-164">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58c30-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58c30-165">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="58c30-166">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="58c30-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="58c30-167">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="58c30-167">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="58c30-168">Per lo sviluppo locale, il [sistema di configurazione ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal *appsettings.json* file.</span><span class="sxs-lookup"><span data-stu-id="58c30-168">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="58c30-169">Aggiungere una stringa di connessione del database</span><span class="sxs-lookup"><span data-stu-id="58c30-169">Add a database connection string</span></span>

<span data-ttu-id="58c30-170">Aggiungere una stringa di connessione al *appsettings.json* file:</span><span class="sxs-lookup"><span data-stu-id="58c30-170">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58c30-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58c30-171">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="58c30-172">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="58c30-172">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="58c30-173">Compilare il progetto per controllare se sono presenti errori del compilatore.</span><span class="sxs-lookup"><span data-stu-id="58c30-173">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="58c30-174">Eseguire lo scaffolding delle pagine Movie</span><span class="sxs-lookup"><span data-stu-id="58c30-174">Scaffold movie pages</span></span>

<span data-ttu-id="58c30-175">Usare lo strumento di scaffolding per generare le pagine per le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) per il modello Movie.</span><span class="sxs-lookup"><span data-stu-id="58c30-175">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58c30-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58c30-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58c30-177">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella \*Controller\***> Aggiungi > Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="58c30-177">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![vista del passaggio precedente](adding-model/_static/add_controller21.png)

<span data-ttu-id="58c30-179">Nella finestra di dialogo **Aggiungi scaffolding** selezionare **Controller MVC con visualizzazioni, che usa Entity Framework**.</span><span class="sxs-lookup"><span data-stu-id="58c30-179">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Finestra di dialogo Aggiungi scaffolding](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="58c30-181">Completare la finestra di dialogo **Aggiungi controller**:</span><span class="sxs-lookup"><span data-stu-id="58c30-181">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="58c30-182">**Classe di modello:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="58c30-182">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="58c30-183">**Classe del contesto dati:** *MvcMovieContext (MvcMovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="58c30-183">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Aggiungere il contesto dati](adding-model/_static/dc5.png)

* <span data-ttu-id="58c30-185">**Viste:** mantenere il valore predefinito di ogni opzione selezionata</span><span class="sxs-lookup"><span data-stu-id="58c30-185">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="58c30-186">**Nome del controller:** mantenere il valore predefinito *MoviesController*</span><span class="sxs-lookup"><span data-stu-id="58c30-186">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="58c30-187">Selezionare **Aggiungi**</span><span class="sxs-lookup"><span data-stu-id="58c30-187">Select **Add**</span></span>

<span data-ttu-id="58c30-188">Visual Studio crea:</span><span class="sxs-lookup"><span data-stu-id="58c30-188">Visual Studio creates:</span></span>

* <span data-ttu-id="58c30-189">Un controller di film (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="58c30-189">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="58c30-190">Razor Visualizza file per le pagine create, DELETE, Details, Edit e index (*views/Movies/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="58c30-190">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="58c30-191">La creazione automatica di questi file è nota come *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="58c30-191">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="58c30-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58c30-192">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="58c30-193">Aprire una finestra di comando nella directory del progetto (la directory che contiene i file *Program.cs*, *Startup.cs* e *csproj*).</span><span class="sxs-lookup"><span data-stu-id="58c30-193">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="58c30-194">In Linux esportare il percorso dello strumento di scaffolding:</span><span class="sxs-lookup"><span data-stu-id="58c30-194">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="58c30-195">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="58c30-195">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58c30-196">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="58c30-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="58c30-197">Aprire una finestra di comando nella directory del progetto (la directory che contiene i file *Program.cs*, *Startup.cs* e *csproj*).</span><span class="sxs-lookup"><span data-stu-id="58c30-197">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="58c30-198">Esportare il percorso dello strumento di impalcatura:</span><span class="sxs-lookup"><span data-stu-id="58c30-198">Export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="58c30-199">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="58c30-199">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="58c30-200">Non è possibile usare ancora le pagine sottoposte a scaffolding perché il database non esiste.</span><span class="sxs-lookup"><span data-stu-id="58c30-200">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="58c30-201">Se si esegue l'app e si fa clic sul collegamento dell' **app Movie** , viene visualizzato un messaggio di errore *non è possibile aprire il database* o una *tabella di questo tipo:* il messaggio di errore Movie.</span><span class="sxs-lookup"><span data-stu-id="58c30-201">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="58c30-202">Migrazione iniziale</span><span class="sxs-lookup"><span data-stu-id="58c30-202">Initial migration</span></span>

<span data-ttu-id="58c30-203">Usare la funzionalità [Migrazioni](xref:data/ef-mvc/migrations) di EF Core per creare il database.</span><span class="sxs-lookup"><span data-stu-id="58c30-203">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="58c30-204">Migrazioni è un set di strumenti che consentono di creare e aggiornare un database in modo che corrisponda al modello di dati.</span><span class="sxs-lookup"><span data-stu-id="58c30-204">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58c30-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58c30-205">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58c30-206">Dal menu **Strumenti** scegliere **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="58c30-206">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="58c30-207">Nella Console di Gestione pacchetti immettere i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="58c30-207">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="58c30-208">`Add-Migration InitialCreate`: Genera un file di migrazione *Migrations/{timestamp} _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="58c30-208">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="58c30-209">L'argomento `InitialCreate` è il nome della migrazione.</span><span class="sxs-lookup"><span data-stu-id="58c30-209">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="58c30-210">È possibile usare qualsiasi nome, ma per convenzione viene selezionato un nome che descrive la migrazione.</span><span class="sxs-lookup"><span data-stu-id="58c30-210">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="58c30-211">Trattandosi della prima migrazione, la classe generata contiene il codice per creare lo schema del database.</span><span class="sxs-lookup"><span data-stu-id="58c30-211">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="58c30-212">Lo schema del database si basa sul modello specificato nella classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="58c30-212">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="58c30-213">`Update-Database`: Aggiorna il database alla migrazione più recente, che ha creato il comando precedente.</span><span class="sxs-lookup"><span data-stu-id="58c30-213">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="58c30-214">Il comando esegue il metodo `Up` nel file *Migrations/{time-stamp}_InitialCreate.cs*, che crea il database.</span><span class="sxs-lookup"><span data-stu-id="58c30-214">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="58c30-215">Il comando database update genera l'avviso seguente:</span><span class="sxs-lookup"><span data-stu-id="58c30-215">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="58c30-216">No type was specified for the decimal column 'Price' on entity type 'Movie'. (Nessun tipo specificato per la colonna decimale 'Price' nel tipo di entità 'Movie').</span><span class="sxs-lookup"><span data-stu-id="58c30-216">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="58c30-217">This will cause values to be silently truncated if they do not fit in the default precision and scale. (I valori saranno quindi automaticamente troncati se non rispettano la precisione e la scala predefinite).</span><span class="sxs-lookup"><span data-stu-id="58c30-217">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="58c30-218">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Specificare in modo esplicito il tipo di colonna di SQL Server che può supportare tutti i valori usando 'HasColumnType()').</span><span class="sxs-lookup"><span data-stu-id="58c30-218">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="58c30-219">È possibile ignorare tale avviso. Verrà risolto in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="58c30-219">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="58c30-220">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="58c30-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="58c30-221">Eseguire i seguenti comandi dell'interfaccia della riga di comando di .NET Core:</span><span class="sxs-lookup"><span data-stu-id="58c30-221">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="58c30-222">`ef migrations add InitialCreate`: Genera un file di migrazione *Migrations/{timestamp} _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="58c30-222">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="58c30-223">L'argomento `InitialCreate` è il nome della migrazione.</span><span class="sxs-lookup"><span data-stu-id="58c30-223">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="58c30-224">È possibile usare qualsiasi nome, ma per convenzione viene selezionato un nome che descrive la migrazione.</span><span class="sxs-lookup"><span data-stu-id="58c30-224">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="58c30-225">Trattandosi della prima migrazione, la classe generata contiene il codice per creare lo schema del database.</span><span class="sxs-lookup"><span data-stu-id="58c30-225">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="58c30-226">Lo schema del database è basato sul modello specificato nella classe `MvcMovieContext` (nel file *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="58c30-226">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="58c30-227">`ef database update`: Aggiorna il database alla migrazione più recente, che ha creato il comando precedente.</span><span class="sxs-lookup"><span data-stu-id="58c30-227">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="58c30-228">Il comando esegue il metodo `Up` nel file *Migrations/{time-stamp}_InitialCreate.cs*, che crea il database.</span><span class="sxs-lookup"><span data-stu-id="58c30-228">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="58c30-229">Classe InitialCreate</span><span class="sxs-lookup"><span data-stu-id="58c30-229">The InitialCreate class</span></span>

<span data-ttu-id="58c30-230">Esaminare il file di migrazione *Migrations/{timestamp}_InitialCreate.cs*:</span><span class="sxs-lookup"><span data-stu-id="58c30-230">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="58c30-231">Il metodo `Up` crea la tabella Movie e configura `Id` come chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="58c30-231">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="58c30-232">Il metodo `Down` annulla le modifiche dello schema apportate dalla migrazione `Up`.</span><span class="sxs-lookup"><span data-stu-id="58c30-232">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="58c30-233">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="58c30-233">Test the app</span></span>

* <span data-ttu-id="58c30-234">Eseguire l'app e fare clic sul collegamento **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="58c30-234">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="58c30-235">Se si ottiene un'eccezione simile a una delle seguenti:</span><span class="sxs-lookup"><span data-stu-id="58c30-235">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58c30-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58c30-236">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="58c30-237">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="58c30-237">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="58c30-238">Probabilmente non è stato eseguito il [passaggio delle migrazioni](#migration).</span><span class="sxs-lookup"><span data-stu-id="58c30-238">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="58c30-239">Testare la pagina **create** .</span><span class="sxs-lookup"><span data-stu-id="58c30-239">Test the **Create** page.</span></span> <span data-ttu-id="58c30-240">Immettere e inviare i dati.</span><span class="sxs-lookup"><span data-stu-id="58c30-240">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="58c30-241">Potrebbe non essere possibile immettere virgole decimali nel campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="58c30-241">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="58c30-242">Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese (Stati Uniti), è necessario localizzare l'app.</span><span class="sxs-lookup"><span data-stu-id="58c30-242">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="58c30-243">Per istruzioni sulla globalizzazione, vedere [questo problema su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="58c30-243">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="58c30-244">Testare le pagine **Edit**, **Details** e **Delete**.</span><span class="sxs-lookup"><span data-stu-id="58c30-244">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="58c30-245">Inserimento delle dipendenze nel controller</span><span class="sxs-lookup"><span data-stu-id="58c30-245">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58c30-246">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58c30-246">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58c30-247">Aprire il file *Controllers/MoviesController.cs* ed esaminare il costruttore:</span><span class="sxs-lookup"><span data-stu-id="58c30-247">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="58c30-248">Il costruttore usa l'[inserimento dipendenze](xref:fundamentals/dependency-injection) per inserire il contesto del database (`MvcMovieContext`) nel controller.</span><span class="sxs-lookup"><span data-stu-id="58c30-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="58c30-249">Il contesto di database viene usato in ognuno dei metodi [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) nel controller.</span><span class="sxs-lookup"><span data-stu-id="58c30-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="58c30-250">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="58c30-250">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="58c30-251">Il costruttore usa l'[inserimento dipendenze](xref:fundamentals/dependency-injection) per inserire il contesto del database (`MvcMovieContext`) nel controller.</span><span class="sxs-lookup"><span data-stu-id="58c30-251">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="58c30-252">Il contesto di database viene usato in ognuno dei metodi [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) nel controller.</span><span class="sxs-lookup"><span data-stu-id="58c30-252">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="58c30-253">Usare SQLite per lo sviluppo, SQL Server per la produzione</span><span class="sxs-lookup"><span data-stu-id="58c30-253">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="58c30-254">Quando si seleziona SQLite, il codice generato dal modello è pronto per lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="58c30-254">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="58c30-255">Il codice seguente illustra come inserire all' <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> avvio.</span><span class="sxs-lookup"><span data-stu-id="58c30-255">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="58c30-256">`IWebHostEnvironment` viene inserito in modo `ConfigureServices` che possa usare SQLite per lo sviluppo e la SQL Server nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="58c30-256">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="58c30-257">Modelli fortemente tipizzati e parola chiave @model</span><span class="sxs-lookup"><span data-stu-id="58c30-257">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="58c30-258">In un passaggio precedente di questa esercitazione è stato esaminato come un controller può passare oggetti o dati a una vista usando il dizionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="58c30-258">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="58c30-259">Il dizionario `ViewData` è un oggetto dinamico che fornisce un modo pratico ad associazione tardiva per passare informazioni a una vista.</span><span class="sxs-lookup"><span data-stu-id="58c30-259">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="58c30-260">MVC consente anche di passare oggetti modello fortemente tipizzati a una vista.</span><span class="sxs-lookup"><span data-stu-id="58c30-260">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="58c30-261">Questo approccio fortemente tipizzato consente il controllo del codice in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="58c30-261">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="58c30-262">Con il meccanismo di scaffolding è stato usato questo approccio, ovvero il passaggio di un modello fortemente tipizzato, con le viste e la classe `MoviesController`.</span><span class="sxs-lookup"><span data-stu-id="58c30-262">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="58c30-263">Aprire il metodo `Details` nel file *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="58c30-263">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="58c30-264">In genere il parametro `id` viene passato come dati di route.</span><span class="sxs-lookup"><span data-stu-id="58c30-264">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="58c30-265">Ad esempio `https://localhost:5001/movies/details/1` imposta:</span><span class="sxs-lookup"><span data-stu-id="58c30-265">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="58c30-266">Il controller sul controller `movies` (primo segmento di URL).</span><span class="sxs-lookup"><span data-stu-id="58c30-266">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="58c30-267">L'azione su `details` (secondo segmento di URL).</span><span class="sxs-lookup"><span data-stu-id="58c30-267">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="58c30-268">L'ID su 1 (ultimo segmento di URL).</span><span class="sxs-lookup"><span data-stu-id="58c30-268">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="58c30-269">È possibile anche passare `id` con una stringa di query nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="58c30-269">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="58c30-270">Il `id` parametro viene definito come [tipo Nullable](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) nel caso in cui non venga fornito un valore ID.</span><span class="sxs-lookup"><span data-stu-id="58c30-270">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="58c30-271">Un'[espressione lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) viene passata a `FirstOrDefaultAsync` per selezionare le entità film che corrispondono al valore della stringa di query o dei dati di route.</span><span class="sxs-lookup"><span data-stu-id="58c30-271">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="58c30-272">Se viene trovato un film, viene passata un'istanza del modello `Movie` alla vista `Details`:</span><span class="sxs-lookup"><span data-stu-id="58c30-272">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="58c30-273">Esaminare il contenuto del file *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="58c30-273">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="58c30-274">L'istruzione `@model` all'inizio del file di vista specifica il tipo di oggetto previsto dalla vista.</span><span class="sxs-lookup"><span data-stu-id="58c30-274">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="58c30-275">Quando è stato creato il controller dei film, è stata inclusa l'istruzione `@model` seguente:</span><span class="sxs-lookup"><span data-stu-id="58c30-275">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="58c30-276">Questa direttiva `@model` consente di accedere al film che il controller ha passato alla vista.</span><span class="sxs-lookup"><span data-stu-id="58c30-276">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="58c30-277">L'oggetto `Model` è fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="58c30-277">The `Model` object is strongly typed.</span></span> <span data-ttu-id="58c30-278">Ad esempio, nella vista *Details.cshtml* il codice passa ogni campo di film agli helper HTML `DisplayNameFor` e `DisplayFor` con l'oggetto `Model` fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="58c30-278">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="58c30-279">Le viste e i metodi `Create` e `Edit` passano anche un oggetto modello `Movie`.</span><span class="sxs-lookup"><span data-stu-id="58c30-279">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="58c30-280">Esaminare la vista *Index.cshtml* e il metodo `Index` nel controller Movies.</span><span class="sxs-lookup"><span data-stu-id="58c30-280">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="58c30-281">Si noti che il codice crea un oggetto `List` quando chiama il metodo `View`.</span><span class="sxs-lookup"><span data-stu-id="58c30-281">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="58c30-282">Il codice passa questo elenco `Movies` dal metodo di azione `Index` alla vista:</span><span class="sxs-lookup"><span data-stu-id="58c30-282">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="58c30-283">Al momento della creazione del controller di film, lo scaffolding ha incluso l'istruzione `@model` all'inizio del file *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="58c30-283">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="58c30-284">La direttiva `@model` consente di accedere all'elenco di film che il controller ha passato alla vista usando un oggetto `Model` fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="58c30-284">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="58c30-285">Ad esempio, nella vista *Index.cshtml* il codice scorre i film con un'istruzione `foreach` sull'oggetto fortemente tipizzato `Model`:</span><span class="sxs-lookup"><span data-stu-id="58c30-285">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="58c30-286">Poiché l'oggetto `Model` è fortemente tipizzato (come un oggetto `IEnumerable<Movie>`), ogni elemento nel ciclo viene tipizzato come `Movie`.</span><span class="sxs-lookup"><span data-stu-id="58c30-286">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="58c30-287">Tra gli altri vantaggi, si ottiene un controllo del codice in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="58c30-287">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="58c30-288">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="58c30-288">Additional resources</span></span>

* [<span data-ttu-id="58c30-289">Helper per i tag</span><span class="sxs-lookup"><span data-stu-id="58c30-289">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="58c30-290">Globalizzazione e localizzazione</span><span class="sxs-lookup"><span data-stu-id="58c30-290">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="58c30-291">[Precedente aggiunta di una vista](adding-view.md) 
>  [Prossima collaborazione con SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="58c30-291">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="58c30-292">Aggiungere una classe del modello di dati</span><span class="sxs-lookup"><span data-stu-id="58c30-292">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58c30-293">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58c30-293">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58c30-294">Fare clic con il pulsante destro del mouse sulla cartella *Models* > **Aggiungi** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="58c30-294">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="58c30-295">Denominare il file *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="58c30-295">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="58c30-296">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58c30-296">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="58c30-297">Aggiungere un file denominato *Movie.cs* alla cartella *Models*.</span><span class="sxs-lookup"><span data-stu-id="58c30-297">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58c30-298">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="58c30-298">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="58c30-299">Fare clic con il pulsante destro del mouse sulla cartella *Models* > **Aggiungi**  >  **nuova classe**  >  **vuota** classe.</span><span class="sxs-lookup"><span data-stu-id="58c30-299">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="58c30-300">Denominare il file *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="58c30-300">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="58c30-301">Aggiornare il file *Movie.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="58c30-301">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="58c30-302">La classe `Movie` contiene un campo `Id`, richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="58c30-302">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="58c30-303">L' <xref:System.ComponentModel.DataAnnotations.DataType> attributo on `ReleaseDate` specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="58c30-303">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="58c30-304">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="58c30-304">With this attribute:</span></span>

* <span data-ttu-id="58c30-305">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="58c30-305">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="58c30-306">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="58c30-306">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="58c30-307">L'attributo [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="58c30-307">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="58c30-308">Aggiungere pacchetti NuGet</span><span class="sxs-lookup"><span data-stu-id="58c30-308">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58c30-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58c30-309">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58c30-310">Dal menu **Strumenti** scegliere **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="58c30-310">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menu della Console di Gestione pacchetti](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="58c30-312">Nella console di Gestione pacchetti eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="58c30-312">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="58c30-313">Il comando precedente aggiunge il provider EF Core SQL Server.</span><span class="sxs-lookup"><span data-stu-id="58c30-313">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="58c30-314">Il pacchetto del provider installa il pacchetto di EF Core come dipendenza.</span><span class="sxs-lookup"><span data-stu-id="58c30-314">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="58c30-315">I pacchetti aggiuntivi vengono installati automaticamente nel passaggio di scaffolding più avanti nell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="58c30-315">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="58c30-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58c30-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58c30-317">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="58c30-317">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="58c30-318">Scegliere **Gestisci pacchetti NuGet** dal menu **progetto** .</span><span class="sxs-lookup"><span data-stu-id="58c30-318">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="58c30-319">Nel campo di **ricerca** in alto a destra, immettere `Microsoft.EntityFrameworkCore.SQLite` e premere il tasto **invio** per eseguire la ricerca.</span><span class="sxs-lookup"><span data-stu-id="58c30-319">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="58c30-320">Selezionare il pacchetto NuGet corrispondente e premere il pulsante **Aggiungi pacchetto** .</span><span class="sxs-lookup"><span data-stu-id="58c30-320">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Aggiungi Entity Framework Core pacchetto NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="58c30-322">Verrà visualizzata la finestra di dialogo **Seleziona progetti** con il `MvcMovie` progetto selezionato.</span><span class="sxs-lookup"><span data-stu-id="58c30-322">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="58c30-323">Premere il pulsante **OK** .</span><span class="sxs-lookup"><span data-stu-id="58c30-323">Press the **Ok** button.</span></span>

<span data-ttu-id="58c30-324">Verrà visualizzata una finestra di dialogo **accettazione licenza** .</span><span class="sxs-lookup"><span data-stu-id="58c30-324">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="58c30-325">Esaminare le licenze nel modo desiderato, quindi fare clic sul pulsante **Accetto** .</span><span class="sxs-lookup"><span data-stu-id="58c30-325">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="58c30-326">Ripetere i passaggi precedenti per installare i pacchetti NuGet seguenti:</span><span class="sxs-lookup"><span data-stu-id="58c30-326">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="58c30-327">Creare una classe di contesto di database</span><span class="sxs-lookup"><span data-stu-id="58c30-327">Create a database context class</span></span>

<span data-ttu-id="58c30-328">Una classe di contesto di database è necessaria per coordinare le funzionalità di EF Core (creazione, lettura, aggiornamento, eliminazione) per il modello `Movie`.</span><span class="sxs-lookup"><span data-stu-id="58c30-328">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="58c30-329">Il contesto di database viene derivato da [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) e specifica le entità da includere nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="58c30-329">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="58c30-330">Creare una cartella *Data*.</span><span class="sxs-lookup"><span data-stu-id="58c30-330">Create a *Data* folder.</span></span>

<span data-ttu-id="58c30-331">Aggiungere un file *Data/MvcMovieContext.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="58c30-331">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="58c30-332">Il codice precedente crea una [proprietà \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="58c30-332">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="58c30-333">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="58c30-333">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="58c30-334">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="58c30-334">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="58c30-335">Registrare il contesto del database</span><span class="sxs-lookup"><span data-stu-id="58c30-335">Register the database context</span></span>

<span data-ttu-id="58c30-336">ASP.NET Core viene compilato con l'[inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="58c30-336">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="58c30-337">I servizi, ad esempio il contesto di database di EF Core, devono essere registrati per l'inserimento delle dipendenze durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="58c30-337">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="58c30-338">I componenti che richiedono questi servizi, ad esempio Razor le pagine, vengono forniti tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="58c30-338">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="58c30-339">Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="58c30-339">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="58c30-340">In questa sezione viene registrato il contesto di database per il contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="58c30-340">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="58c30-341">Aggiungere le istruzioni `using` seguenti all'inizio di *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="58c30-341">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="58c30-342">Aggiungere il codice evidenziato seguente in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="58c30-342">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58c30-343">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58c30-343">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="58c30-344">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="58c30-344">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="58c30-345">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="58c30-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="58c30-346">Per lo sviluppo locale, il [sistema di configurazione ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal *appsettings.json* file.</span><span class="sxs-lookup"><span data-stu-id="58c30-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="58c30-347">Aggiungere una stringa di connessione del database</span><span class="sxs-lookup"><span data-stu-id="58c30-347">Add a database connection string</span></span>

<span data-ttu-id="58c30-348">Aggiungere una stringa di connessione al *appsettings.json* file:</span><span class="sxs-lookup"><span data-stu-id="58c30-348">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58c30-349">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58c30-349">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-13)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="58c30-350">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="58c30-350">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-13)]

---

<span data-ttu-id="58c30-351">Compilare il progetto per controllare se sono presenti errori del compilatore.</span><span class="sxs-lookup"><span data-stu-id="58c30-351">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="58c30-352">Eseguire lo scaffolding delle pagine Movie</span><span class="sxs-lookup"><span data-stu-id="58c30-352">Scaffold movie pages</span></span>

<span data-ttu-id="58c30-353">Usare lo strumento di scaffolding per generare le pagine per le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) per il modello Movie.</span><span class="sxs-lookup"><span data-stu-id="58c30-353">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58c30-354">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58c30-354">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58c30-355">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella \*Controller\***> Aggiungi > Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="58c30-355">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![vista del passaggio precedente](adding-model/_static/add_controller21.png)

<span data-ttu-id="58c30-357">Nella finestra di dialogo **Aggiungi scaffolding** selezionare **Controller MVC con visualizzazioni, che usa Entity Framework**.</span><span class="sxs-lookup"><span data-stu-id="58c30-357">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Finestra di dialogo Aggiungi scaffolding](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="58c30-359">Completare la finestra di dialogo **Aggiungi controller**:</span><span class="sxs-lookup"><span data-stu-id="58c30-359">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="58c30-360">**Classe di modello:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="58c30-360">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="58c30-361">**Classe del contesto dati:** *MvcMovieContext (MvcMovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="58c30-361">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Aggiungere il contesto dati](adding-model/_static/dc3.png)

* <span data-ttu-id="58c30-363">**Viste:** mantenere il valore predefinito di ogni opzione selezionata</span><span class="sxs-lookup"><span data-stu-id="58c30-363">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="58c30-364">**Nome del controller:** mantenere il valore predefinito *MoviesController*</span><span class="sxs-lookup"><span data-stu-id="58c30-364">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="58c30-365">Selezionare **Aggiungi**</span><span class="sxs-lookup"><span data-stu-id="58c30-365">Select **Add**</span></span>

<span data-ttu-id="58c30-366">Visual Studio crea:</span><span class="sxs-lookup"><span data-stu-id="58c30-366">Visual Studio creates:</span></span>

* <span data-ttu-id="58c30-367">Un controller di film (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="58c30-367">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="58c30-368">Razor Visualizza file per le pagine create, DELETE, Details, Edit e index (*views/Movies/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="58c30-368">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="58c30-369">La creazione automatica di questi file è nota come *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="58c30-369">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="58c30-370">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58c30-370">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="58c30-371">Aprire una finestra di comando nella directory del progetto (la directory che contiene i file *Program.cs*, *Startup.cs* e *csproj*).</span><span class="sxs-lookup"><span data-stu-id="58c30-371">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="58c30-372">In Linux esportare il percorso dello strumento di scaffolding:</span><span class="sxs-lookup"><span data-stu-id="58c30-372">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="58c30-373">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="58c30-373">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58c30-374">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="58c30-374">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="58c30-375">Aprire una finestra di comando nella directory del progetto (la directory che contiene i file *Program.cs*, *Startup.cs* e *csproj*).</span><span class="sxs-lookup"><span data-stu-id="58c30-375">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="58c30-376">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="58c30-376">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="58c30-377">Non è possibile usare ancora le pagine sottoposte a scaffolding perché il database non esiste.</span><span class="sxs-lookup"><span data-stu-id="58c30-377">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="58c30-378">Se si esegue l'app e si fa clic sul collegamento dell' **app Movie** , viene visualizzato un messaggio di errore *non è possibile aprire il database* o una *tabella di questo tipo:* il messaggio di errore Movie.</span><span class="sxs-lookup"><span data-stu-id="58c30-378">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="58c30-379">Migrazione iniziale</span><span class="sxs-lookup"><span data-stu-id="58c30-379">Initial migration</span></span>

<span data-ttu-id="58c30-380">Usare la funzionalità [Migrazioni](xref:data/ef-mvc/migrations) di EF Core per creare il database.</span><span class="sxs-lookup"><span data-stu-id="58c30-380">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="58c30-381">Migrazioni è un set di strumenti che consentono di creare e aggiornare un database in modo che corrisponda al modello di dati.</span><span class="sxs-lookup"><span data-stu-id="58c30-381">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58c30-382">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58c30-382">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58c30-383">Dal menu **Strumenti** scegliere **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="58c30-383">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="58c30-384">Nella Console di Gestione pacchetti immettere i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="58c30-384">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="58c30-385">`Add-Migration InitialCreate`: Genera un file di migrazione *Migrations/{timestamp} _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="58c30-385">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="58c30-386">L'argomento `InitialCreate` è il nome della migrazione.</span><span class="sxs-lookup"><span data-stu-id="58c30-386">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="58c30-387">È possibile usare qualsiasi nome, ma per convenzione viene selezionato un nome che descrive la migrazione.</span><span class="sxs-lookup"><span data-stu-id="58c30-387">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="58c30-388">Trattandosi della prima migrazione, la classe generata contiene il codice per creare lo schema del database.</span><span class="sxs-lookup"><span data-stu-id="58c30-388">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="58c30-389">Lo schema del database si basa sul modello specificato nella classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="58c30-389">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="58c30-390">`Update-Database`: Aggiorna il database alla migrazione più recente, che ha creato il comando precedente.</span><span class="sxs-lookup"><span data-stu-id="58c30-390">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="58c30-391">Il comando esegue il metodo `Up` nel file *Migrations/{time-stamp}_InitialCreate.cs*, che crea il database.</span><span class="sxs-lookup"><span data-stu-id="58c30-391">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="58c30-392">Il comando database update genera l'avviso seguente:</span><span class="sxs-lookup"><span data-stu-id="58c30-392">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="58c30-393">No type was specified for the decimal column 'Price' on entity type 'Movie'. (Nessun tipo specificato per la colonna decimale 'Price' nel tipo di entità 'Movie').</span><span class="sxs-lookup"><span data-stu-id="58c30-393">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="58c30-394">This will cause values to be silently truncated if they do not fit in the default precision and scale. (I valori saranno quindi automaticamente troncati se non rispettano la precisione e la scala predefinite).</span><span class="sxs-lookup"><span data-stu-id="58c30-394">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="58c30-395">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Specificare in modo esplicito il tipo di colonna di SQL Server che può supportare tutti i valori usando 'HasColumnType()').</span><span class="sxs-lookup"><span data-stu-id="58c30-395">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="58c30-396">È possibile ignorare tale avviso. Verrà risolto in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="58c30-396">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="58c30-397">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="58c30-397">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="58c30-398">Eseguire i seguenti comandi dell'interfaccia della riga di comando di .NET Core:</span><span class="sxs-lookup"><span data-stu-id="58c30-398">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="58c30-399">`ef migrations add InitialCreate`: Genera un file di migrazione *Migrations/{timestamp} _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="58c30-399">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="58c30-400">L'argomento `InitialCreate` è il nome della migrazione.</span><span class="sxs-lookup"><span data-stu-id="58c30-400">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="58c30-401">È possibile usare qualsiasi nome, ma per convenzione viene selezionato un nome che descrive la migrazione.</span><span class="sxs-lookup"><span data-stu-id="58c30-401">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="58c30-402">Trattandosi della prima migrazione, la classe generata contiene il codice per creare lo schema del database.</span><span class="sxs-lookup"><span data-stu-id="58c30-402">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="58c30-403">Lo schema del database è basato sul modello specificato nella classe `MvcMovieContext` (nel file *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="58c30-403">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="58c30-404">`ef database update`: Aggiorna il database alla migrazione più recente, che ha creato il comando precedente.</span><span class="sxs-lookup"><span data-stu-id="58c30-404">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="58c30-405">Il comando esegue il metodo `Up` nel file *Migrations/{time-stamp}_InitialCreate.cs*, che crea il database.</span><span class="sxs-lookup"><span data-stu-id="58c30-405">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="58c30-406">Classe InitialCreate</span><span class="sxs-lookup"><span data-stu-id="58c30-406">The InitialCreate class</span></span>

<span data-ttu-id="58c30-407">Esaminare il file di migrazione *Migrations/{timestamp}_InitialCreate.cs*:</span><span class="sxs-lookup"><span data-stu-id="58c30-407">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="58c30-408">Il metodo `Up` crea la tabella Movie e configura `Id` come chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="58c30-408">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="58c30-409">Il metodo `Down` annulla le modifiche dello schema apportate dalla migrazione `Up`.</span><span class="sxs-lookup"><span data-stu-id="58c30-409">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="58c30-410">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="58c30-410">Test the app</span></span>

* <span data-ttu-id="58c30-411">Eseguire l'app e fare clic sul collegamento **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="58c30-411">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="58c30-412">Se si ottiene un'eccezione simile a una delle seguenti:</span><span class="sxs-lookup"><span data-stu-id="58c30-412">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58c30-413">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58c30-413">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="58c30-414">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="58c30-414">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="58c30-415">Probabilmente non è stato eseguito il [passaggio delle migrazioni](#migration).</span><span class="sxs-lookup"><span data-stu-id="58c30-415">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="58c30-416">Testare la pagina **create** .</span><span class="sxs-lookup"><span data-stu-id="58c30-416">Test the **Create** page.</span></span> <span data-ttu-id="58c30-417">Immettere e inviare i dati.</span><span class="sxs-lookup"><span data-stu-id="58c30-417">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="58c30-418">Potrebbe non essere possibile immettere virgole decimali nel campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="58c30-418">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="58c30-419">Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese (Stati Uniti), è necessario localizzare l'app.</span><span class="sxs-lookup"><span data-stu-id="58c30-419">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="58c30-420">Per istruzioni sulla globalizzazione, vedere [questo problema su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="58c30-420">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="58c30-421">Testare le pagine **Edit**, **Details** e **Delete**.</span><span class="sxs-lookup"><span data-stu-id="58c30-421">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="58c30-422">Inserimento delle dipendenze nel controller</span><span class="sxs-lookup"><span data-stu-id="58c30-422">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58c30-423">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58c30-423">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58c30-424">Aprire il file *Controllers/MoviesController.cs* ed esaminare il costruttore:</span><span class="sxs-lookup"><span data-stu-id="58c30-424">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="58c30-425">Il costruttore usa l'[inserimento dipendenze](xref:fundamentals/dependency-injection) per inserire il contesto del database (`MvcMovieContext`) nel controller.</span><span class="sxs-lookup"><span data-stu-id="58c30-425">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="58c30-426">Il contesto di database viene usato in ognuno dei metodi [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) nel controller.</span><span class="sxs-lookup"><span data-stu-id="58c30-426">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="58c30-427">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="58c30-427">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="58c30-428">Il costruttore usa l'[inserimento dipendenze](xref:fundamentals/dependency-injection) per inserire il contesto del database (`MvcMovieContext`) nel controller.</span><span class="sxs-lookup"><span data-stu-id="58c30-428">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="58c30-429">Il contesto di database viene usato in ognuno dei metodi [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) nel controller.</span><span class="sxs-lookup"><span data-stu-id="58c30-429">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="58c30-430">Usare SQLite per lo sviluppo, SQL Server per la produzione</span><span class="sxs-lookup"><span data-stu-id="58c30-430">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="58c30-431">Quando si seleziona SQLite, il codice generato dal modello è pronto per lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="58c30-431">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="58c30-432">Il codice seguente illustra come inserire all' <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> avvio.</span><span class="sxs-lookup"><span data-stu-id="58c30-432">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="58c30-433">`IWebHostEnvironment` viene inserito in modo `ConfigureServices` che possa usare SQLite per lo sviluppo e la SQL Server nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="58c30-433">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="58c30-434">Modelli fortemente tipizzati e parola chiave @model</span><span class="sxs-lookup"><span data-stu-id="58c30-434">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="58c30-435">In un passaggio precedente di questa esercitazione è stato esaminato come un controller può passare oggetti o dati a una vista usando il dizionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="58c30-435">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="58c30-436">Il dizionario `ViewData` è un oggetto dinamico che fornisce un modo pratico ad associazione tardiva per passare informazioni a una vista.</span><span class="sxs-lookup"><span data-stu-id="58c30-436">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="58c30-437">MVC consente anche di passare oggetti modello fortemente tipizzati a una vista.</span><span class="sxs-lookup"><span data-stu-id="58c30-437">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="58c30-438">Questo approccio fortemente tipizzato consente il controllo del codice in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="58c30-438">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="58c30-439">Con il meccanismo di scaffolding è stato usato questo approccio, ovvero il passaggio di un modello fortemente tipizzato, con le viste e la classe `MoviesController`.</span><span class="sxs-lookup"><span data-stu-id="58c30-439">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="58c30-440">Aprire il metodo `Details` nel file *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="58c30-440">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="58c30-441">In genere il parametro `id` viene passato come dati di route.</span><span class="sxs-lookup"><span data-stu-id="58c30-441">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="58c30-442">Ad esempio `https://localhost:5001/movies/details/1` imposta:</span><span class="sxs-lookup"><span data-stu-id="58c30-442">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="58c30-443">Il controller sul controller `movies` (primo segmento di URL).</span><span class="sxs-lookup"><span data-stu-id="58c30-443">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="58c30-444">L'azione su `details` (secondo segmento di URL).</span><span class="sxs-lookup"><span data-stu-id="58c30-444">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="58c30-445">L'ID su 1 (ultimo segmento di URL).</span><span class="sxs-lookup"><span data-stu-id="58c30-445">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="58c30-446">È possibile anche passare `id` con una stringa di query nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="58c30-446">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="58c30-447">Il `id` parametro viene definito come [tipo Nullable](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) nel caso in cui non venga fornito un valore ID.</span><span class="sxs-lookup"><span data-stu-id="58c30-447">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="58c30-448">Un'[espressione lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) viene passata a `FirstOrDefaultAsync` per selezionare le entità film che corrispondono al valore della stringa di query o dei dati di route.</span><span class="sxs-lookup"><span data-stu-id="58c30-448">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="58c30-449">Se viene trovato un film, viene passata un'istanza del modello `Movie` alla vista `Details`:</span><span class="sxs-lookup"><span data-stu-id="58c30-449">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="58c30-450">Esaminare il contenuto del file *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="58c30-450">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="58c30-451">L'istruzione `@model` all'inizio del file di vista specifica il tipo di oggetto previsto dalla vista.</span><span class="sxs-lookup"><span data-stu-id="58c30-451">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="58c30-452">Quando è stato creato il controller dei film, è stata inclusa l'istruzione `@model` seguente:</span><span class="sxs-lookup"><span data-stu-id="58c30-452">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="58c30-453">Questa direttiva `@model` consente di accedere al film che il controller ha passato alla vista.</span><span class="sxs-lookup"><span data-stu-id="58c30-453">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="58c30-454">L'oggetto `Model` è fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="58c30-454">The `Model` object is strongly typed.</span></span> <span data-ttu-id="58c30-455">Ad esempio, nella vista *Details.cshtml* il codice passa ogni campo di film agli helper HTML `DisplayNameFor` e `DisplayFor` con l'oggetto `Model` fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="58c30-455">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="58c30-456">Le viste e i metodi `Create` e `Edit` passano anche un oggetto modello `Movie`.</span><span class="sxs-lookup"><span data-stu-id="58c30-456">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="58c30-457">Esaminare la vista *Index.cshtml* e il metodo `Index` nel controller Movies.</span><span class="sxs-lookup"><span data-stu-id="58c30-457">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="58c30-458">Si noti che il codice crea un oggetto `List` quando chiama il metodo `View`.</span><span class="sxs-lookup"><span data-stu-id="58c30-458">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="58c30-459">Il codice passa questo elenco `Movies` dal metodo di azione `Index` alla vista:</span><span class="sxs-lookup"><span data-stu-id="58c30-459">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="58c30-460">Al momento della creazione del controller di film, lo scaffolding ha incluso l'istruzione `@model` all'inizio del file *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="58c30-460">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="58c30-461">La direttiva `@model` consente di accedere all'elenco di film che il controller ha passato alla vista usando un oggetto `Model` fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="58c30-461">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="58c30-462">Ad esempio, nella vista *Index.cshtml* il codice scorre i film con un'istruzione `foreach` sull'oggetto fortemente tipizzato `Model`:</span><span class="sxs-lookup"><span data-stu-id="58c30-462">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="58c30-463">Poiché l'oggetto `Model` è fortemente tipizzato (come un oggetto `IEnumerable<Movie>`), ogni elemento nel ciclo viene tipizzato come `Movie`.</span><span class="sxs-lookup"><span data-stu-id="58c30-463">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="58c30-464">Tra gli altri vantaggi, si ottiene un controllo del codice in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="58c30-464">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="58c30-465">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="58c30-465">Additional resources</span></span>

* [<span data-ttu-id="58c30-466">Helper per i tag</span><span class="sxs-lookup"><span data-stu-id="58c30-466">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="58c30-467">Globalizzazione e localizzazione</span><span class="sxs-lookup"><span data-stu-id="58c30-467">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="58c30-468">[Precedente aggiunta di una vista](adding-view.md) 
>  [Prossima collaborazione con SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="58c30-468">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="58c30-469">Aggiungere una classe del modello di dati</span><span class="sxs-lookup"><span data-stu-id="58c30-469">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58c30-470">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58c30-470">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58c30-471">Fare clic con il pulsante destro del mouse sulla cartella *Models* > **Aggiungi** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="58c30-471">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="58c30-472">Denominare la classe **Movie**.</span><span class="sxs-lookup"><span data-stu-id="58c30-472">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="58c30-473">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="58c30-473">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="58c30-474">Aggiungere una classe alla cartella *Modello* denominata *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="58c30-474">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="58c30-475">Eseguire lo scaffolding del modello di filmato</span><span class="sxs-lookup"><span data-stu-id="58c30-475">Scaffold the movie model</span></span>

<span data-ttu-id="58c30-476">In questa sezione viene eseguito lo scaffolding del modello *Movie*.</span><span class="sxs-lookup"><span data-stu-id="58c30-476">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="58c30-477">Lo strumento di scaffolding crea quindi le pagine per le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) per il modello *Movie*.</span><span class="sxs-lookup"><span data-stu-id="58c30-477">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58c30-478">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58c30-478">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58c30-479">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella \*Controller\***> Aggiungi > Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="58c30-479">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![vista del passaggio precedente](adding-model/_static/add_controller21.png)

<span data-ttu-id="58c30-481">Nella finestra di dialogo **Aggiungi scaffolding** selezionare **Controller MVC con visualizzazioni, che usa Entity Framework**.</span><span class="sxs-lookup"><span data-stu-id="58c30-481">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Finestra di dialogo Aggiungi scaffolding](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="58c30-483">Completare la finestra di dialogo **Aggiungi controller**:</span><span class="sxs-lookup"><span data-stu-id="58c30-483">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="58c30-484">**Classe di modello:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="58c30-484">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="58c30-485">**Classe del contesto dati:** selezionare l'icona **+** e aggiungere il valore predefinito **MvcMovie.Models.MvcMovieContext**</span><span class="sxs-lookup"><span data-stu-id="58c30-485">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Aggiungere il contesto dati](adding-model/_static/dc.png)

* <span data-ttu-id="58c30-487">**Viste:** mantenere il valore predefinito di ogni opzione selezionata</span><span class="sxs-lookup"><span data-stu-id="58c30-487">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="58c30-488">**Nome del controller:** mantenere il valore predefinito *MoviesController*</span><span class="sxs-lookup"><span data-stu-id="58c30-488">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="58c30-489">Selezionare **Aggiungi**</span><span class="sxs-lookup"><span data-stu-id="58c30-489">Select **Add**</span></span>

![Finestra di dialogo Aggiungi controller](adding-model/_static/add_controller2.png)

<span data-ttu-id="58c30-491">Visual Studio crea:</span><span class="sxs-lookup"><span data-stu-id="58c30-491">Visual Studio creates:</span></span>

* <span data-ttu-id="58c30-492">Una [classe del contesto di database](xref:data/ef-mvc/intro#create-the-database-context) Entity Framework Core (*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="58c30-492">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="58c30-493">Un controller di film (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="58c30-493">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="58c30-494">Razor Visualizza file per le pagine create, DELETE, Details, Edit e index (*views/Movies/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="58c30-494">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="58c30-495">La creazione automatica del contesto di database e di viste e metodi di azione [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, delete) è nota come *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="58c30-495">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="58c30-496">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58c30-496">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="58c30-497">Aprire una finestra di comando nella directory del progetto (la directory che contiene i file *Program.cs*, *Startup.cs* e *csproj*).</span><span class="sxs-lookup"><span data-stu-id="58c30-497">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="58c30-498">Installare lo strumento di scaffolding:</span><span class="sxs-lookup"><span data-stu-id="58c30-498">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="58c30-499">In Linux esportare il percorso dello strumento di scaffolding:</span><span class="sxs-lookup"><span data-stu-id="58c30-499">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="58c30-500">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="58c30-500">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58c30-501">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="58c30-501">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="58c30-502">Aprire una finestra di comando nella directory del progetto (la directory che contiene i file *Program.cs*, *Startup.cs* e *csproj*).</span><span class="sxs-lookup"><span data-stu-id="58c30-502">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="58c30-503">Installare lo strumento di scaffolding:</span><span class="sxs-lookup"><span data-stu-id="58c30-503">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="58c30-504">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="58c30-504">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="58c30-505">Se si esegue l'app e si fa clic sul collegamento **Mvc Movie**, viene visualizzato un errore simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="58c30-505">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58c30-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58c30-506">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="58c30-507">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="58c30-507">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="58c30-508">È necessario creare il database, quindi si usa la funzionalità di [migrazioni](xref:data/ef-mvc/migrations) di Entity Framework Core a tale scopo.</span><span class="sxs-lookup"><span data-stu-id="58c30-508">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="58c30-509">Le migrazioni consentono di creare un database che corrisponde al modello di dati e aggiornare lo schema del database quando cambia il modello di dati.</span><span class="sxs-lookup"><span data-stu-id="58c30-509">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="58c30-510">Migrazione iniziale</span><span class="sxs-lookup"><span data-stu-id="58c30-510">Initial migration</span></span>

<span data-ttu-id="58c30-511">In questa sezione vengono completate le attività seguenti:</span><span class="sxs-lookup"><span data-stu-id="58c30-511">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="58c30-512">Aggiungere una migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="58c30-512">Add an initial migration.</span></span>
* <span data-ttu-id="58c30-513">Aggiornare il database con la migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="58c30-513">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58c30-514">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58c30-514">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="58c30-515">Dal menu **Strumenti** scegliere **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="58c30-515">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![Menu della Console di Gestione pacchetti](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="58c30-517">Nella Console di Gestione pacchetti immettere i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="58c30-517">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="58c30-518">Il comando `Add-Migration` genera un codice per creare lo schema del database iniziale.</span><span class="sxs-lookup"><span data-stu-id="58c30-518">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="58c30-519">Lo schema del database si basa sul modello specificato nella classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="58c30-519">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="58c30-520">L'argomento `Initial` è il nome della migrazione.</span><span class="sxs-lookup"><span data-stu-id="58c30-520">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="58c30-521">È possibile usare qualsiasi nome, ma per convenzione viene usato un nome che descrive la migrazione.</span><span class="sxs-lookup"><span data-stu-id="58c30-521">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="58c30-522">Per altre informazioni, vedere <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="58c30-522">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="58c30-523">Il `Update-Database` comando esegue il `Up` metodo nel file *Migrations/{Time-Stamp} _InitialCreate. cs* , che crea il database.</span><span class="sxs-lookup"><span data-stu-id="58c30-523">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="58c30-524">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="58c30-524">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="58c30-525">Il comando `ef migrations add InitialCreate` genera un codice per creare lo schema del database iniziale.</span><span class="sxs-lookup"><span data-stu-id="58c30-525">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="58c30-526">Lo schema del database è basato sul modello specificato nella classe `MvcMovieContext` (nel file *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="58c30-526">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="58c30-527">L'argomento `InitialCreate` è il nome della migrazione.</span><span class="sxs-lookup"><span data-stu-id="58c30-527">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="58c30-528">È possibile usare qualsiasi nome, ma per convenzione viene selezionato un nome che descrive la migrazione.</span><span class="sxs-lookup"><span data-stu-id="58c30-528">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="58c30-529">Esaminare il contesto registrato con l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="58c30-529">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="58c30-530">ASP.NET Core viene compilato con l'[inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="58c30-530">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="58c30-531">I servizi, ad esempio il contesto di database di EF Core, vengono registrati con l'inserimento delle dipendenze durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="58c30-531">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="58c30-532">I componenti che richiedono questi servizi, ad esempio Razor le pagine, vengono forniti tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="58c30-532">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="58c30-533">Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="58c30-533">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58c30-534">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58c30-534">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58c30-535">Lo strumento di scaffolding ha creato automaticamente un contesto del database e lo ha registrato con il contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="58c30-535">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="58c30-536">Esaminare il metodo `Startup.ConfigureServices` seguente.</span><span class="sxs-lookup"><span data-stu-id="58c30-536">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="58c30-537">La riga evidenziata è stata aggiunta dallo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="58c30-537">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="58c30-538">`MvcMovieContext` coordina la funzionalità di EF Core (Create, Read, Update, Delete e così via) per il modello `Movie`.</span><span class="sxs-lookup"><span data-stu-id="58c30-538">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="58c30-539">Il contesto dei dati (`MvcMovieContext`) è derivato da [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="58c30-539">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="58c30-540">Il contesto dei dati specifica le entità incluse nel modello di dati:</span><span class="sxs-lookup"><span data-stu-id="58c30-540">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="58c30-541">Il codice precedente crea una [proprietà \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="58c30-541">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="58c30-542">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="58c30-542">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="58c30-543">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="58c30-543">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="58c30-544">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="58c30-544">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="58c30-545">Per lo sviluppo locale, il [sistema di configurazione ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal *appsettings.json* file.</span><span class="sxs-lookup"><span data-stu-id="58c30-545">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="58c30-546">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="58c30-546">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="58c30-547">È stato creato un contesto del database, poi registrato per il contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="58c30-547">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="58c30-548">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="58c30-548">Test the app</span></span>

* <span data-ttu-id="58c30-549">Eseguire l'app e accodare `/Movies` all'URL nel browser (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="58c30-549">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="58c30-550">Se si verifica un'eccezione di database simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="58c30-550">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="58c30-551">Non è stato eseguita la [migrazione](#pmc).</span><span class="sxs-lookup"><span data-stu-id="58c30-551">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="58c30-552">Eseguire il test del collegamento **Crea**.</span><span class="sxs-lookup"><span data-stu-id="58c30-552">Test the **Create** link.</span></span> <span data-ttu-id="58c30-553">Immettere e inviare i dati.</span><span class="sxs-lookup"><span data-stu-id="58c30-553">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="58c30-554">Potrebbe non essere possibile immettere virgole decimali nel campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="58c30-554">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="58c30-555">Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese (Stati Uniti), è necessario localizzare l'app.</span><span class="sxs-lookup"><span data-stu-id="58c30-555">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="58c30-556">Per istruzioni sulla globalizzazione, vedere [questo problema su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="58c30-556">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="58c30-557">Testare i collegamenti **Modifica**, **Dettagli** ed **Elimina**.</span><span class="sxs-lookup"><span data-stu-id="58c30-557">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="58c30-558">Esaminare la classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="58c30-558">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="58c30-559">Il codice evidenziato riportato in precedenza mostra il contesto del database dei film che viene aggiunto al contenitore di [inserimento dipendenze](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="58c30-559">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="58c30-560">`services.AddDbContext<MvcMovieContext>(options =>` specifica il database da usare e la stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="58c30-560">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="58c30-561">`=>` è un [operatore lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="58c30-561">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="58c30-562">Aprire il file *Controllers/MoviesController.cs* ed esaminare il costruttore:</span><span class="sxs-lookup"><span data-stu-id="58c30-562">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="58c30-563">Il costruttore usa l'[inserimento dipendenze](xref:fundamentals/dependency-injection) per inserire il contesto del database (`MvcMovieContext`) nel controller.</span><span class="sxs-lookup"><span data-stu-id="58c30-563">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="58c30-564">Il contesto di database viene usato in ognuno dei metodi [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) nel controller.</span><span class="sxs-lookup"><span data-stu-id="58c30-564">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="58c30-565">Modelli fortemente tipizzati e parola chiave @model</span><span class="sxs-lookup"><span data-stu-id="58c30-565">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="58c30-566">In un passaggio precedente di questa esercitazione è stato esaminato come un controller può passare oggetti o dati a una vista usando il dizionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="58c30-566">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="58c30-567">Il dizionario `ViewData` è un oggetto dinamico che fornisce un modo pratico ad associazione tardiva per passare informazioni a una vista.</span><span class="sxs-lookup"><span data-stu-id="58c30-567">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="58c30-568">MVC consente anche di passare oggetti modello fortemente tipizzati a una vista.</span><span class="sxs-lookup"><span data-stu-id="58c30-568">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="58c30-569">Questo approccio fortemente tipizzato consente un migliore controllo del codice in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="58c30-569">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="58c30-570">Con il meccanismo di scaffolding è stato usato questo approccio, ovvero il passaggio di un modello fortemente tipizzato, con le viste e la classe `MoviesController` quando sono stati creati i metodi e le viste.</span><span class="sxs-lookup"><span data-stu-id="58c30-570">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="58c30-571">Aprire il metodo `Details` nel file *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="58c30-571">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="58c30-572">In genere il parametro `id` viene passato come dati di route.</span><span class="sxs-lookup"><span data-stu-id="58c30-572">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="58c30-573">Ad esempio `https://localhost:5001/movies/details/1` imposta:</span><span class="sxs-lookup"><span data-stu-id="58c30-573">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="58c30-574">Il controller sul controller `movies` (primo segmento di URL).</span><span class="sxs-lookup"><span data-stu-id="58c30-574">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="58c30-575">L'azione su `details` (secondo segmento di URL).</span><span class="sxs-lookup"><span data-stu-id="58c30-575">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="58c30-576">L'ID su 1 (ultimo segmento di URL).</span><span class="sxs-lookup"><span data-stu-id="58c30-576">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="58c30-577">È possibile anche passare `id` con una stringa di query nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="58c30-577">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="58c30-578">Il `id` parametro viene definito come [tipo Nullable](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) nel caso in cui non venga fornito un valore ID.</span><span class="sxs-lookup"><span data-stu-id="58c30-578">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="58c30-579">Un'[espressione lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) viene passata a `FirstOrDefaultAsync` per selezionare le entità film che corrispondono al valore della stringa di query o dei dati di route.</span><span class="sxs-lookup"><span data-stu-id="58c30-579">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="58c30-580">Se viene trovato un film, viene passata un'istanza del modello `Movie` alla vista `Details`:</span><span class="sxs-lookup"><span data-stu-id="58c30-580">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="58c30-581">Esaminare il contenuto del file *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="58c30-581">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="58c30-582">Includendo un'istruzione `@model` all'inizio del file di vista, è possibile specificare il tipo di oggetto previsto dalla vista.</span><span class="sxs-lookup"><span data-stu-id="58c30-582">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="58c30-583">Al momento della creazione del controller di film, l'istruzione `@model` seguente è stata inclusa automaticamente all'inizio del file *Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="58c30-583">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="58c30-584">Questa direttiva `@model` consente di accedere al film passato dal controller alla vista usando un oggetto `Model` fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="58c30-584">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="58c30-585">Ad esempio, nella vista *Details.cshtml* il codice passa ogni campo di film agli helper HTML `DisplayNameFor` e `DisplayFor` con l'oggetto `Model` fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="58c30-585">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="58c30-586">Le viste e i metodi `Create` e `Edit` passano anche un oggetto modello `Movie`.</span><span class="sxs-lookup"><span data-stu-id="58c30-586">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="58c30-587">Esaminare la vista *Index.cshtml* e il metodo `Index` nel controller Movies.</span><span class="sxs-lookup"><span data-stu-id="58c30-587">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="58c30-588">Si noti che il codice crea un oggetto `List` quando chiama il metodo `View`.</span><span class="sxs-lookup"><span data-stu-id="58c30-588">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="58c30-589">Il codice passa questo elenco `Movies` dal metodo di azione `Index` alla vista:</span><span class="sxs-lookup"><span data-stu-id="58c30-589">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="58c30-590">Al momento della creazione del controller di film, lo scaffolding ha incluso automaticamente l'istruzione `@model` all'inizio del file *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="58c30-590">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="58c30-591">La direttiva `@model` consente di accedere all'elenco di film che il controller ha passato alla vista usando un oggetto `Model` fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="58c30-591">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="58c30-592">Ad esempio, nella vista *Index.cshtml* il codice scorre i film con un'istruzione `foreach` sull'oggetto fortemente tipizzato `Model`:</span><span class="sxs-lookup"><span data-stu-id="58c30-592">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="58c30-593">Poiché l'oggetto `Model` è fortemente tipizzato (come un oggetto `IEnumerable<Movie>`), ogni elemento nel ciclo viene tipizzato come `Movie`.</span><span class="sxs-lookup"><span data-stu-id="58c30-593">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="58c30-594">Tra gli altri vantaggi, si ottiene un controllo del codice in fase di compilazione:</span><span class="sxs-lookup"><span data-stu-id="58c30-594">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="58c30-595">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="58c30-595">Additional resources</span></span>

* [<span data-ttu-id="58c30-596">Helper per i tag</span><span class="sxs-lookup"><span data-stu-id="58c30-596">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="58c30-597">Globalizzazione e localizzazione</span><span class="sxs-lookup"><span data-stu-id="58c30-597">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="58c30-598">[Precedente aggiunta di una vista](adding-view.md) 
>  [Successiva utilizzo di un database](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="58c30-598">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
