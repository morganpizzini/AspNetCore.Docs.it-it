---
title: Parte 2, aggiungere un modello
author: rick-anderson
description: Parte 2 della serie di esercitazioni sulle Razor pagine. In questa sezione vengono aggiunte le classi del modello.
ms.author: riande
ms.date: 11/11/2020
ms.custom: contperf-fy21q2
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 7ea28e0ecad410335c37c603c8ec1eb5e6e41d33
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "97485992"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="62a7f-104">Parte 2, aggiungere un modello a un' Razor app di pagine in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="62a7f-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="62a7f-105">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="62a7f-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="62a7f-106">In questa sezione si aggiungono alcune classi per la gestione di filmati in un database.</span><span class="sxs-lookup"><span data-stu-id="62a7f-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="62a7f-107">Le classi modello dell'app usano [Entity Framework Core (EF Core)](/ef/core) per lavorare con il database.</span><span class="sxs-lookup"><span data-stu-id="62a7f-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="62a7f-108">EF Core è un mapper relazionale a oggetti (O/RM) che semplifica l'accesso ai dati.</span><span class="sxs-lookup"><span data-stu-id="62a7f-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="62a7f-109">Prima di tutto si scrivono le classi del modello e EF Core crea il database.</span><span class="sxs-lookup"><span data-stu-id="62a7f-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="62a7f-110">Le classi del modello sono note come classi POCO (da "**P** Lain-**o** LD **C** LR **o** gli oggetti") perché non hanno una dipendenza da EF core.</span><span class="sxs-lookup"><span data-stu-id="62a7f-110">The model classes are known as POCO classes (from "**P** lain-**O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="62a7f-111">Definiscono le proprietà dei dati archiviati nel database.</span><span class="sxs-lookup"><span data-stu-id="62a7f-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="62a7f-112">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="62a7f-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="62a7f-113">Aggiungere un modello di dati</span><span class="sxs-lookup"><span data-stu-id="62a7f-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="62a7f-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="62a7f-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="62a7f-115">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto *Razor PagesMovie* > **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-115">In **Solution Explorer**, right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="62a7f-116">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="62a7f-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="62a7f-117">Fare clic con il pulsante destro del mouse sulla cartella *modelli* .</span><span class="sxs-lookup"><span data-stu-id="62a7f-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="62a7f-118">Selezionare **Aggiungi**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="62a7f-119">Denominare la classe *Movie*.</span><span class="sxs-lookup"><span data-stu-id="62a7f-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="62a7f-120">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="62a7f-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="62a7f-121">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="62a7f-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="62a7f-122">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="62a7f-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="62a7f-123">`[DataType(DataType.Date)]`: L'attributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="62a7f-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="62a7f-124">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="62a7f-124">With this attribute:</span></span>

  * <span data-ttu-id="62a7f-125">L'utente non deve immettere le informazioni sull'ora nel campo Data.</span><span class="sxs-lookup"><span data-stu-id="62a7f-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="62a7f-126">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="62a7f-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="62a7f-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="62a7f-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="62a7f-128">Aggiungere una cartella denominata *Modelli*.</span><span class="sxs-lookup"><span data-stu-id="62a7f-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="62a7f-129">Aggiungere una classe alla cartella *Modello* denominata *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="62a7f-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="62a7f-130">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="62a7f-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="62a7f-131">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="62a7f-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="62a7f-132">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="62a7f-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="62a7f-133">`[DataType(DataType.Date)]`: L'attributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="62a7f-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="62a7f-134">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="62a7f-134">With this attribute:</span></span>

  * <span data-ttu-id="62a7f-135">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="62a7f-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="62a7f-136">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="62a7f-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="62a7f-137">Aggiungere i pacchetti NuGet e gli strumenti EF</span><span class="sxs-lookup"><span data-stu-id="62a7f-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="62a7f-138">Aggiungere una classe di contesto dei dati</span><span class="sxs-lookup"><span data-stu-id="62a7f-138">Add a database context class</span></span>

1. <span data-ttu-id="62a7f-139">Nel progetto *Razor PagesMovie* creare una cartella denominata *Data*.</span><span class="sxs-lookup"><span data-stu-id="62a7f-139">In the *RazorPagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="62a7f-140">Nella cartella *dati* aggiungere un file denominato *Razor PagesMovieContext.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="62a7f-140">In the *Data* folder, add a file named *RazorPagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   <span data-ttu-id="62a7f-141">Il codice precedente crea una proprietà `DbSet` per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="62a7f-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="62a7f-142">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database e un'entità corrisponde a una riga della tabella.</span><span class="sxs-lookup"><span data-stu-id="62a7f-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="62a7f-143">Il codice non verrà compilato fino a quando le dipendenze non verranno aggiunte in un passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="62a7f-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="62a7f-144">Aggiungere una stringa di connessione del database</span><span class="sxs-lookup"><span data-stu-id="62a7f-144">Add a database connection string</span></span>

<span data-ttu-id="62a7f-145">Aggiungere una stringa di connessione al *appsettings.json* file come illustrato nel codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="62a7f-145">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="62a7f-146">Registrare il contesto del database</span><span class="sxs-lookup"><span data-stu-id="62a7f-146">Register the database context</span></span>

1. <span data-ttu-id="62a7f-147">Aggiungere le istruzioni `using` seguenti all'inizio di *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="62a7f-147">Add the following `using` statements at the top of *Startup.cs*:</span></span>

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="62a7f-148">Registrare il contesto del database con il contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="62a7f-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="62a7f-149">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="62a7f-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="62a7f-150">Nella **finestra degli strumenti della soluzione**, fare clic sul progetto *Razor PagesMovie* e quindi selezionare **Aggiungi** > **nuova cartella.** Assegnare un nome ai *modelli* di cartella.</span><span class="sxs-lookup"><span data-stu-id="62a7f-150">In the **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="62a7f-151">CTRL-fare clic sulla cartella *modelli* , quindi selezionare **Aggiungi** > **nuovo file.**...</span><span class="sxs-lookup"><span data-stu-id="62a7f-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="62a7f-152">Nel finestra di dialogo **Nuovo file**:</span><span class="sxs-lookup"><span data-stu-id="62a7f-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="62a7f-153">Selezionare **Generale** nel riquadro a sinistra.</span><span class="sxs-lookup"><span data-stu-id="62a7f-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="62a7f-154">Selezionare **Classe vuota** nel riquadro centrale.</span><span class="sxs-lookup"><span data-stu-id="62a7f-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="62a7f-155">Denominare la classe **Filmato** e selezionare **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="62a7f-156">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="62a7f-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="62a7f-157">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="62a7f-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="62a7f-158">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="62a7f-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="62a7f-159">`[DataType(DataType.Date)]`: L'attributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="62a7f-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="62a7f-160">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="62a7f-160">With this attribute:</span></span>

  * <span data-ttu-id="62a7f-161">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="62a7f-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="62a7f-162">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="62a7f-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="62a7f-163">L'attributo [DataAnnotations](xref:System.ComponentModel.DataAnnotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="62a7f-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="62a7f-164">Compilare il progetto per verificare che non siano presenti errori di compilazione.</span><span class="sxs-lookup"><span data-stu-id="62a7f-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="62a7f-165">Eseguire lo scaffolding del modello di filmato</span><span class="sxs-lookup"><span data-stu-id="62a7f-165">Scaffold the movie model</span></span>

<span data-ttu-id="62a7f-166">In questa sezione viene eseguito lo scaffolding del modello *Movie*.</span><span class="sxs-lookup"><span data-stu-id="62a7f-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="62a7f-167">Lo strumento di scaffolding crea quindi le pagine per le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) per il modello *Movie*.</span><span class="sxs-lookup"><span data-stu-id="62a7f-167">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="62a7f-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="62a7f-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="62a7f-169">Creare una cartella *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="62a7f-169">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="62a7f-170">Fare clic con il pulsante destro del mouse sulla cartella *pagine* > **Aggiungi** > **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="62a7f-171">Denominare la cartella *Movies*.</span><span class="sxs-lookup"><span data-stu-id="62a7f-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="62a7f-172">Fare clic con il pulsante destro del mouse sulla cartella *pages/Movies* > **Aggiungi** > **nuovo elemento con impalcatura**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![Immagine relativa alle istruzioni precedenti.](model/_static/5/sca.png)

1. <span data-ttu-id="62a7f-174">Nella finestra di dialogo **Aggiungi impalcatura** selezionare **Razor pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-174">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffold.png)

1. <span data-ttu-id="62a7f-176">Completare la finestra di dialogo **Aggiungi Razor pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="62a7f-176">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="62a7f-177">Nell'elenco a discesa **classe modello** selezionare **Movie ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-177">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="62a7f-178">Nella riga **Classe contesto di dati** selezionare il segno più **+**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="62a7f-179">Nella finestra di dialogo **Aggiungi contesto dati** il nome della classe *Razor PagesMovie. Data. Razor* Viene generato PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="62a7f-179">In the **Add Data Context** dialog, the class name *RazorPagesMovie.Data.RazorPagesMovieContext* is generated.</span></span>
   1. <span data-ttu-id="62a7f-180">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-180">Select **Add**.</span></span>

   ![Immagine relativa alle istruzioni precedenti.](model/_static/3/arp.png)

<span data-ttu-id="62a7f-182">Il *appsettings.json* file viene aggiornato con la stringa di connessione utilizzata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="62a7f-182">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="62a7f-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="62a7f-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="62a7f-184">Aprire una shell dei comandi nella directory del progetto che contiene i file *Program.cs*, *Startup.cs* e *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="62a7f-184">Open a command shell to the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="62a7f-185">**Per Windows**: eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="62a7f-185">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="62a7f-186">**Per MacOS e Linux**: eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="62a7f-186">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="62a7f-187">La tabella seguente illustra in dettaglio le opzioni del generatore di codice ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="62a7f-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="62a7f-188">Opzione</span><span class="sxs-lookup"><span data-stu-id="62a7f-188">Option</span></span>               | <span data-ttu-id="62a7f-189">Descrizione</span><span class="sxs-lookup"><span data-stu-id="62a7f-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="62a7f-190">Nome del modello.</span><span class="sxs-lookup"><span data-stu-id="62a7f-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="62a7f-191">Classe `DbContext` da usare.</span><span class="sxs-lookup"><span data-stu-id="62a7f-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="62a7f-192">Uso del layout predefinito.</span><span class="sxs-lookup"><span data-stu-id="62a7f-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="62a7f-193">Percorso relativo della cartella di output per creare le viste.</span><span class="sxs-lookup"><span data-stu-id="62a7f-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="62a7f-194">Aggiunge `_ValidationScriptsPartial` per modificare e creare pagine</span><span class="sxs-lookup"><span data-stu-id="62a7f-194">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="62a7f-195">Usare l' `-h` opzione per ottenere informazioni sul `aspnet-codegenerator razorpage` comando:</span><span class="sxs-lookup"><span data-stu-id="62a7f-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="62a7f-196">Per ulteriori informazioni, vedere [DotNet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="62a7f-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="62a7f-197">Usare SQLite per lo sviluppo, SQL Server per la produzione</span><span class="sxs-lookup"><span data-stu-id="62a7f-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="62a7f-198">Quando si seleziona SQLite, il codice generato dal modello è pronto per lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="62a7f-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="62a7f-199">Nel codice seguente viene illustrato come inserire <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in `Startup` .</span><span class="sxs-lookup"><span data-stu-id="62a7f-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="62a7f-200">`IWebHostEnvironment` viene inserito in modo che l'app possa usare SQLite in fase di sviluppo e SQL Server nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="62a7f-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="62a7f-201">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="62a7f-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="62a7f-202">Creare una cartella *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="62a7f-202">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="62a7f-203">CTRL: fare clic sulla cartella *pagine* > **Aggiungi** > **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="62a7f-204">Denominare la cartella *Movies*.</span><span class="sxs-lookup"><span data-stu-id="62a7f-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="62a7f-205">CTRL: fare clic sulla cartella *pagine/filmati* > **Aggiungi** > **Nuova impalcatura...**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![Immagine relativa alle istruzioni precedenti.](model/_static/scaMac.png)

1. <span data-ttu-id="62a7f-207">Nella finestra di dialogo **Nuova impalcatura** selezionare **Razor pagine con Entity Framework (CRUD)** > **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-207">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="62a7f-209">Completare la finestra di dialogo **Aggiungi Razor pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="62a7f-209">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="62a7f-210">Nella **classe DbContext da usare:** Row, denominare la classe *Razor PagesMovie. Data. Razor PagesMovieContext*.</span><span class="sxs-lookup"><span data-stu-id="62a7f-210">In the **DbContext Class to use:** row, name the class *RazorPagesMovie.Data.RazorPagesMovieContext*.</span></span>
   1. <span data-ttu-id="62a7f-211">Selezionare **Fine**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-211">Select **Finish**.</span></span>

   ![Immagine relativa alle istruzioni precedenti.](model/_static/5/arpMac.png)

<span data-ttu-id="62a7f-213">Il *appsettings.json* file viene aggiornato con la stringa di connessione utilizzata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="62a7f-213">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="62a7f-214">Usare SQLite per lo sviluppo, SQL Server per la produzione</span><span class="sxs-lookup"><span data-stu-id="62a7f-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="62a7f-215">Quando si seleziona SQLite, il codice generato dal modello è pronto per lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="62a7f-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="62a7f-216">Nel codice seguente viene illustrato come inserire <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in `Startup` .</span><span class="sxs-lookup"><span data-stu-id="62a7f-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="62a7f-217">`IWebHostEnvironment` viene inserito in modo che l'app possa usare SQLite in fase di sviluppo e SQL Server nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="62a7f-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="62a7f-218">File creati</span><span class="sxs-lookup"><span data-stu-id="62a7f-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="62a7f-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="62a7f-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="62a7f-220">Il processo di scaffolding crea e aggiorna i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="62a7f-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="62a7f-221">*Pagine/filmati*: creare, eliminare, Details, Edit e Index .</span><span class="sxs-lookup"><span data-stu-id="62a7f-221">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="62a7f-222">*Data/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="62a7f-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="62a7f-223">Aggiornato</span><span class="sxs-lookup"><span data-stu-id="62a7f-223">Updated</span></span>

* <span data-ttu-id="62a7f-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="62a7f-224">*Startup.cs*</span></span>

<span data-ttu-id="62a7f-225">I file creati e aggiornati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="62a7f-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="62a7f-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="62a7f-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="62a7f-227">Il processo di scaffolding crea i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="62a7f-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="62a7f-228">*Pagine/filmati*: creare, eliminare, Details, Edit e Index .</span><span class="sxs-lookup"><span data-stu-id="62a7f-228">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="62a7f-229">I file creati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="62a7f-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="62a7f-230">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="62a7f-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="62a7f-231">Il processo di scaffolding crea e aggiorna i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="62a7f-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="62a7f-232">*Pagine/filmati*: creare, eliminare, Details, Edit e Index .</span><span class="sxs-lookup"><span data-stu-id="62a7f-232">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="62a7f-233">*Data/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="62a7f-233">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="62a7f-234">Aggiornato</span><span class="sxs-lookup"><span data-stu-id="62a7f-234">Updated</span></span>

* <span data-ttu-id="62a7f-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="62a7f-235">*Startup.cs*</span></span>

<span data-ttu-id="62a7f-236">I file creati e aggiornati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="62a7f-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="62a7f-237">Creare lo schema del database iniziale usando la funzionalità di migrazione di EF</span><span class="sxs-lookup"><span data-stu-id="62a7f-237">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="62a7f-238">La funzionalità migrazioni di Entity Framework Core fornisce un modo per:</span><span class="sxs-lookup"><span data-stu-id="62a7f-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="62a7f-239">Creare lo schema del database iniziale.</span><span class="sxs-lookup"><span data-stu-id="62a7f-239">Create the initial database schema.</span></span>
* <span data-ttu-id="62a7f-240">Aggiornare in modo incrementale lo schema del database per mantenerlo sincronizzato con il modello di dati dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="62a7f-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="62a7f-241">Vengono conservati i dati esistenti nel database.</span><span class="sxs-lookup"><span data-stu-id="62a7f-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="62a7f-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="62a7f-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="62a7f-243">In questa sezione viene usata la finestra **console di gestione pacchetti** (PMC) per:</span><span class="sxs-lookup"><span data-stu-id="62a7f-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="62a7f-244">Aggiungere una migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="62a7f-244">Add an initial migration.</span></span>
* <span data-ttu-id="62a7f-245">Aggiornare il database con la migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="62a7f-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="62a7f-246">Nel menu **Strumenti** selezionare **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![Menu della Console di Gestione pacchetti](model/_static/5/pmc.png)

1. <span data-ttu-id="62a7f-248">Nella Console di Gestione pacchetti immettere i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="62a7f-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="62a7f-249">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="62a7f-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="62a7f-250">Eseguire i seguenti comandi dell'interfaccia della riga di comando .NET:</span><span class="sxs-lookup"><span data-stu-id="62a7f-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="62a7f-251">I comandi precedenti generano l'avviso seguente: "nessun tipo specificato per la colonna decimale ' Price ' nel tipo di entità' Movie '.</span><span class="sxs-lookup"><span data-stu-id="62a7f-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="62a7f-252">This will cause values to be silently truncated if they do not fit in the default precision and scale. (I valori saranno quindi automaticamente troncati se non rispettano la precisione e la scala predefinite).</span><span class="sxs-lookup"><span data-stu-id="62a7f-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="62a7f-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Specificare in modo esplicito il tipo di colonna di SQL Server che può supportare tutti i valori usando 'HasColumnType()')"</span><span class="sxs-lookup"><span data-stu-id="62a7f-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="62a7f-254">Ignorare l'avviso poiché verrà risolto in un passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="62a7f-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="62a7f-255">Il comando `migrations` genera un codice per creare lo schema del database iniziale.</span><span class="sxs-lookup"><span data-stu-id="62a7f-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="62a7f-256">Lo schema è basato sul modello specificato in `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="62a7f-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="62a7f-257">L'argomento `InitialCreate` viene usato per denominare le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="62a7f-257">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="62a7f-258">È possibile usare qualsiasi nome, ma per convenzione viene selezionato un nome che descrive la migrazione.</span><span class="sxs-lookup"><span data-stu-id="62a7f-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="62a7f-259">Il `update` comando esegue il `Up` Metodo nelle migrazioni che non sono state applicate.</span><span class="sxs-lookup"><span data-stu-id="62a7f-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="62a7f-260">In tal caso, `update` esegue il `Up` metodo nel file *migrations/ \<time-stamp> _InitialCreate. cs* , che crea il database.</span><span class="sxs-lookup"><span data-stu-id="62a7f-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="62a7f-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="62a7f-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="62a7f-262">Esaminare il contesto registrato con l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="62a7f-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="62a7f-263">ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="62a7f-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="62a7f-264">I servizi, ad esempio il contesto di database EF Core, vengono registrati con l'inserimento delle dipendenze durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="62a7f-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="62a7f-265">I componenti che richiedono questi servizi, ad esempio Razor le pagine, vengono forniti tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="62a7f-265">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="62a7f-266">Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="62a7f-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="62a7f-267">Lo strumento di ponteggi crea automaticamente un contesto di database e lo registra con il contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="62a7f-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="62a7f-268">Esaminare il metodo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="62a7f-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="62a7f-269">La riga evidenziata è stata aggiunta dallo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="62a7f-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="62a7f-270">Le `RazorPagesMovieContext` coordinate EF Core funzionalità, ad esempio creazione, lettura, aggiornamento ed eliminazione, per il `Movie` modello.</span><span class="sxs-lookup"><span data-stu-id="62a7f-270">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="62a7f-271">Il contesto dei dati (`RazorPagesMovieContext`) è derivato da [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="62a7f-271">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="62a7f-272">Il contesto dei dati specifica le entità incluse nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="62a7f-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="62a7f-273">Il codice precedente crea una [proprietà \<Movie> DbSet](xref:Microsoft.EntityFrameworkCore.DbSet%601) per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="62a7f-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="62a7f-274">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="62a7f-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="62a7f-275">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="62a7f-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="62a7f-276">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="62a7f-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="62a7f-277">Per lo sviluppo locale, il [sistema di configurazione](xref:fundamentals/configuration/index) legge la stringa di connessione dal *appsettings.json* file.</span><span class="sxs-lookup"><span data-stu-id="62a7f-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="62a7f-278">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="62a7f-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="62a7f-279">Esaminare il metodo `Up`.</span><span class="sxs-lookup"><span data-stu-id="62a7f-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="62a7f-280">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="62a7f-280">Test the app</span></span>

1. <span data-ttu-id="62a7f-281">Eseguire l'app e accodare `/Movies` all'URL nel browser (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="62a7f-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="62a7f-282">Se si riceve un messaggio di errore che indica che</span><span class="sxs-lookup"><span data-stu-id="62a7f-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="62a7f-283">Non è stato eseguita la [migrazione](#pmc).</span><span class="sxs-lookup"><span data-stu-id="62a7f-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="62a7f-284">Eseguire il test del collegamento **Crea**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-284">Test the **Create** link.</span></span>

   ![Pagina di creazione](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="62a7f-286">Potrebbe non essere possibile immettere virgole decimali nel campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="62a7f-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="62a7f-287">Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese (Stati Uniti), è necessario localizzare l'app.</span><span class="sxs-lookup"><span data-stu-id="62a7f-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="62a7f-288">Per istruzioni sulla globalizzazione, vedere [questo problema su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="62a7f-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="62a7f-289">Testare i collegamenti **Modifica**, **Dettagli** ed **Elimina**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-289">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="62a7f-290">L'esercitazione successiva illustra i file creati tramite scaffolding.</span><span class="sxs-lookup"><span data-stu-id="62a7f-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="62a7f-291">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="62a7f-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="62a7f-292">[Precedente: introduzione](xref:tutorials/razor-pages/razor-pages-start) 
>  Passaggio [successivo: impalcatura Razor Pagine](xref:tutorials/razor-pages/page) di</span><span class="sxs-lookup"><span data-stu-id="62a7f-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="62a7f-293">In questa sezione vengono aggiunte le classi per la gestione dei filmati.</span><span class="sxs-lookup"><span data-stu-id="62a7f-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="62a7f-294">Le classi modello dell'app usano [Entity Framework Core (EF Core)](/ef/core) per lavorare con il database.</span><span class="sxs-lookup"><span data-stu-id="62a7f-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="62a7f-295">EF Core è un mapper relazionale a oggetti (O/RM) che semplifica l'accesso ai dati.</span><span class="sxs-lookup"><span data-stu-id="62a7f-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="62a7f-296">Le classi di modello sono dette classi POCO (da "plain-old CLR objects") perché non hanno alcuna dipendenza in EF Core.</span><span class="sxs-lookup"><span data-stu-id="62a7f-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="62a7f-297">Definiscono le proprietà dei dati archiviati nel database.</span><span class="sxs-lookup"><span data-stu-id="62a7f-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="62a7f-298">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="62a7f-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="62a7f-299">Aggiungere un modello di dati</span><span class="sxs-lookup"><span data-stu-id="62a7f-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="62a7f-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="62a7f-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="62a7f-301">Fare clic con il pulsante destro del mouse sul progetto **Razor PagesMovie** > **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-301">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="62a7f-302">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="62a7f-302">Name the folder *Models*.</span></span>

<span data-ttu-id="62a7f-303">Fare clic con il pulsante destro del mouse sulla cartella *modelli* .</span><span class="sxs-lookup"><span data-stu-id="62a7f-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="62a7f-304">Selezionare **Aggiungi**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="62a7f-305">Denominare la classe **Movie**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-305">Name the class **Movie**.</span></span>

<span data-ttu-id="62a7f-306">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="62a7f-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="62a7f-307">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="62a7f-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="62a7f-308">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="62a7f-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="62a7f-309">`[DataType(DataType.Date)]`: L'attributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="62a7f-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="62a7f-310">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="62a7f-310">With this attribute:</span></span>

  * <span data-ttu-id="62a7f-311">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="62a7f-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="62a7f-312">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="62a7f-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="62a7f-313">L'attributo [DataAnnotations](xref:System.ComponentModel.DataAnnotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="62a7f-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="62a7f-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="62a7f-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="62a7f-315">Aggiungere una cartella denominata *Modelli*.</span><span class="sxs-lookup"><span data-stu-id="62a7f-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="62a7f-316">Aggiungere una classe alla cartella *Modello* denominata *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="62a7f-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="62a7f-317">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="62a7f-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="62a7f-318">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="62a7f-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="62a7f-319">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="62a7f-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="62a7f-320">`[DataType(DataType.Date)]`: L'attributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="62a7f-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="62a7f-321">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="62a7f-321">With this attribute:</span></span>

  * <span data-ttu-id="62a7f-322">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="62a7f-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="62a7f-323">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="62a7f-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="62a7f-324">L'attributo [DataAnnotations](xref:System.ComponentModel.DataAnnotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="62a7f-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="62a7f-325">Aggiungere i pacchetti NuGet e gli strumenti EF</span><span class="sxs-lookup"><span data-stu-id="62a7f-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="62a7f-326">Aggiungere una classe di contesto dei dati</span><span class="sxs-lookup"><span data-stu-id="62a7f-326">Add a database context class</span></span>

* <span data-ttu-id="62a7f-327">Nel progetto *Razor PagesMovie* creare una nuova cartella denominata *Data*.</span><span class="sxs-lookup"><span data-stu-id="62a7f-327">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="62a7f-328">Aggiungere la classe `RazorPagesMovieContext` seguente alla cartella *Data*:</span><span class="sxs-lookup"><span data-stu-id="62a7f-328">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="62a7f-329">Il codice precedente crea una proprietà `DbSet` per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="62a7f-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="62a7f-330">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database e un'entità corrisponde a una riga della tabella.</span><span class="sxs-lookup"><span data-stu-id="62a7f-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="62a7f-331">Il codice non verrà compilato fino a quando le dipendenze non verranno aggiunte in un passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="62a7f-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="62a7f-332">Aggiungere una stringa di connessione del database</span><span class="sxs-lookup"><span data-stu-id="62a7f-332">Add a database connection string</span></span>

<span data-ttu-id="62a7f-333">Aggiungere una stringa di connessione al *appsettings.json* file come illustrato nel codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="62a7f-333">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="62a7f-334">Registrare il contesto del database</span><span class="sxs-lookup"><span data-stu-id="62a7f-334">Register the database context</span></span>

<span data-ttu-id="62a7f-335">Aggiungere le istruzioni `using` seguenti all'inizio di *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="62a7f-335">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="62a7f-336">Registrare il contesto del database con il contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="62a7f-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="62a7f-337">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="62a7f-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="62a7f-338">Nella **finestra degli strumenti della soluzione**, fare clic sul progetto **Razor PagesMovie** e quindi selezionare **Aggiungi** > **nuova cartella.** Assegnare un nome ai *modelli* di cartella.</span><span class="sxs-lookup"><span data-stu-id="62a7f-338">In the **Solution Tool Window**, control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="62a7f-339">Fare clic con il pulsante destro del mouse sulla cartella *Models* , quindi scegliere **Aggiungi** > **nuovo file**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="62a7f-340">Nel finestra di dialogo **Nuovo file**:</span><span class="sxs-lookup"><span data-stu-id="62a7f-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="62a7f-341">Selezionare **Generale** nel riquadro a sinistra.</span><span class="sxs-lookup"><span data-stu-id="62a7f-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="62a7f-342">Selezionare **Classe vuota** nel riquadro centrale.</span><span class="sxs-lookup"><span data-stu-id="62a7f-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="62a7f-343">Denominare la classe **Filmato** e selezionare **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="62a7f-344">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="62a7f-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="62a7f-345">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="62a7f-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="62a7f-346">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="62a7f-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="62a7f-347">`[DataType(DataType.Date)]`: L'attributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="62a7f-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="62a7f-348">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="62a7f-348">With this attribute:</span></span>

  * <span data-ttu-id="62a7f-349">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="62a7f-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="62a7f-350">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="62a7f-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="62a7f-351">L'attributo [DataAnnotations](xref:System.ComponentModel.DataAnnotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="62a7f-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="62a7f-352">Compilare il progetto per verificare che non siano presenti errori di compilazione.</span><span class="sxs-lookup"><span data-stu-id="62a7f-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="62a7f-353">Eseguire lo scaffolding del modello di filmato</span><span class="sxs-lookup"><span data-stu-id="62a7f-353">Scaffold the movie model</span></span>

<span data-ttu-id="62a7f-354">In questa sezione viene eseguito lo scaffolding del modello *Movie*.</span><span class="sxs-lookup"><span data-stu-id="62a7f-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="62a7f-355">Lo strumento di scaffolding crea quindi le pagine per le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) per il modello *Movie*.</span><span class="sxs-lookup"><span data-stu-id="62a7f-355">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="62a7f-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="62a7f-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="62a7f-357">Creare una cartella *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="62a7f-357">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="62a7f-358">Fare clic con il pulsante destro del mouse sulla cartella *pagine* > **Aggiungi** > **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="62a7f-359">Denominare la cartella *Movies*.</span><span class="sxs-lookup"><span data-stu-id="62a7f-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="62a7f-360">Fare clic con il pulsante destro del mouse sulla cartella *pages/Movies* > **Aggiungi** > **nuovo elemento con impalcatura**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/sca.png)

<span data-ttu-id="62a7f-362">Nella finestra di dialogo **Aggiungi impalcatura** selezionare **Razor pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-362">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffold.png)

<span data-ttu-id="62a7f-364">Completare la finestra di dialogo **Aggiungi Razor pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="62a7f-364">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="62a7f-365">Nell'elenco a discesa **classe modello** selezionare **Movie ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-365">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="62a7f-366">Nella riga della **classe del contesto dati** selezionare il **+** segno (segno più) e modificare il nome generato da Razor PagesMovie.**Modelli**. Razor Da PagesMovieContext a Razor PagesMovie.**Dati**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="62a7f-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="62a7f-367">[Questa modifica](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) non è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="62a7f-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="62a7f-368">Crea la classe del contesto di database con lo spazio dei nomi corretto.</span><span class="sxs-lookup"><span data-stu-id="62a7f-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="62a7f-369">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-369">Select **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/3/arp.png)

<span data-ttu-id="62a7f-371">Il *appsettings.json* file viene aggiornato con la stringa di connessione utilizzata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="62a7f-371">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="62a7f-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="62a7f-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="62a7f-373">Aprire una finestra di comando nella directory del progetto che contiene i file *Program.cs*, *Startup.cs* e *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="62a7f-373">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="62a7f-374">**Per Windows**: eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="62a7f-374">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="62a7f-375">**Per MacOS e Linux**: eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="62a7f-375">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="62a7f-376">La tabella seguente illustra in dettaglio le opzioni del generatore di codice ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="62a7f-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="62a7f-377">Opzione</span><span class="sxs-lookup"><span data-stu-id="62a7f-377">Option</span></span>               | <span data-ttu-id="62a7f-378">Descrizione</span><span class="sxs-lookup"><span data-stu-id="62a7f-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="62a7f-379">Nome del modello.</span><span class="sxs-lookup"><span data-stu-id="62a7f-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="62a7f-380">Classe `DbContext` da usare.</span><span class="sxs-lookup"><span data-stu-id="62a7f-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="62a7f-381">Uso del layout predefinito.</span><span class="sxs-lookup"><span data-stu-id="62a7f-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="62a7f-382">Percorso relativo della cartella di output per creare le viste.</span><span class="sxs-lookup"><span data-stu-id="62a7f-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="62a7f-383">Aggiunge `_ValidationScriptsPartial` per modificare e creare pagine</span><span class="sxs-lookup"><span data-stu-id="62a7f-383">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="62a7f-384">Usare l' `-h` opzione per ottenere informazioni sul `aspnet-codegenerator razorpage` comando:</span><span class="sxs-lookup"><span data-stu-id="62a7f-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="62a7f-385">Per ulteriori informazioni, vedere [DotNet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="62a7f-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="62a7f-386">Usare SQLite per lo sviluppo, SQL Server per la produzione</span><span class="sxs-lookup"><span data-stu-id="62a7f-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="62a7f-387">Quando si seleziona SQLite, il codice generato dal modello è pronto per lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="62a7f-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="62a7f-388">Il codice seguente illustra come inserire all' <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> avvio.</span><span class="sxs-lookup"><span data-stu-id="62a7f-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="62a7f-389">`IWebHostEnvironment` viene inserito in modo `ConfigureServices` che possa usare SQLite per lo sviluppo e la SQL Server nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="62a7f-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="62a7f-390">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="62a7f-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="62a7f-391">Creare una cartella *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="62a7f-391">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="62a7f-392">Fare clic con il pulsante destro del mouse sulla cartella *pagine* > **Aggiungi** > **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="62a7f-393">Denominare la cartella *Movies*.</span><span class="sxs-lookup"><span data-stu-id="62a7f-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="62a7f-394">Fare clic con il pulsante destro del mouse sulla cartella *pages/Movies* > **Aggiungi** > **Nuova impalcatura...**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/scaMac.png)

<span data-ttu-id="62a7f-396">Nella finestra di dialogo **Nuova impalcatura** selezionare **Razor pagine con Entity Framework (CRUD)** > **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-396">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="62a7f-398">Completare la finestra di dialogo **Aggiungi Razor pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="62a7f-398">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="62a7f-399">Nell'elenco a discesa **classe modello** selezionare o digitare **Movie ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-399">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="62a7f-400">Nella riga della **classe del contesto dati** Digitare il nome della nuova classe, Razor PagesMovie.**Dati**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="62a7f-400">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="62a7f-401">[Questa modifica](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) non è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="62a7f-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="62a7f-402">Crea la classe del contesto di database con lo spazio dei nomi corretto.</span><span class="sxs-lookup"><span data-stu-id="62a7f-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="62a7f-403">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-403">Select **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/arpMac.png)

<span data-ttu-id="62a7f-405">Il *appsettings.json* file viene aggiornato con la stringa di connessione utilizzata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="62a7f-405">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="62a7f-406">Aggiungi strumenti EF</span><span class="sxs-lookup"><span data-stu-id="62a7f-406">Add EF tools</span></span>

<span data-ttu-id="62a7f-407">Eseguire il comando interfaccia della riga di comando di .NET Core seguente:</span><span class="sxs-lookup"><span data-stu-id="62a7f-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="62a7f-408">Il comando precedente aggiunge gli strumenti Entity Framework Core per l'interfaccia della riga di comando di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="62a7f-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="62a7f-409">Per ulteriori informazioni, vedere [riferimento agli strumenti Entity Framework Core-interfaccia della riga di comando di .NET Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="62a7f-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="62a7f-410">Usare SQLite per lo sviluppo, SQL Server per la produzione</span><span class="sxs-lookup"><span data-stu-id="62a7f-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="62a7f-411">Quando si seleziona SQLite, il codice generato dal modello è pronto per lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="62a7f-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="62a7f-412">Il codice seguente illustra come inserire all' <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> avvio.</span><span class="sxs-lookup"><span data-stu-id="62a7f-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="62a7f-413">`IWebHostEnvironment` viene inserito in modo `ConfigureServices` che possa usare SQLite per lo sviluppo e la SQL Server nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="62a7f-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="62a7f-414">File creati</span><span class="sxs-lookup"><span data-stu-id="62a7f-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="62a7f-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="62a7f-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="62a7f-416">Il processo di scaffolding crea e aggiorna i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="62a7f-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="62a7f-417">*Pagine/filmati*: creare, eliminare, Details, Edit e Index .</span><span class="sxs-lookup"><span data-stu-id="62a7f-417">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="62a7f-418">*Data/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="62a7f-418">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="62a7f-419">Aggiornato</span><span class="sxs-lookup"><span data-stu-id="62a7f-419">Updated</span></span>

* <span data-ttu-id="62a7f-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="62a7f-420">*Startup.cs*</span></span>

<span data-ttu-id="62a7f-421">I file creati e aggiornati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="62a7f-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="62a7f-422">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="62a7f-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="62a7f-423">Il processo di scaffolding crea e aggiorna i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="62a7f-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="62a7f-424">*Pagine/filmati*: creare, eliminare, Details, Edit e Index .</span><span class="sxs-lookup"><span data-stu-id="62a7f-424">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="62a7f-425">*Data/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="62a7f-425">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="62a7f-426">Aggiornato</span><span class="sxs-lookup"><span data-stu-id="62a7f-426">Updated</span></span>

* <span data-ttu-id="62a7f-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="62a7f-427">*Startup.cs*</span></span>

<span data-ttu-id="62a7f-428">I file creati e aggiornati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="62a7f-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="62a7f-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="62a7f-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="62a7f-430">Il processo di scaffolding crea i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="62a7f-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="62a7f-431">*Pagine/filmati*: creare, eliminare, Details, Edit e Index .</span><span class="sxs-lookup"><span data-stu-id="62a7f-431">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="62a7f-432">I file creati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="62a7f-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="62a7f-433">Migrazione iniziale</span><span class="sxs-lookup"><span data-stu-id="62a7f-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="62a7f-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="62a7f-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="62a7f-435">In questa sezione viene usata la Console di Gestione pacchetti (PMC) per:</span><span class="sxs-lookup"><span data-stu-id="62a7f-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="62a7f-436">Aggiungere una migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="62a7f-436">Add an initial migration.</span></span>
* <span data-ttu-id="62a7f-437">Aggiornare il database con la migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="62a7f-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="62a7f-438">Nel menu **Strumenti** selezionare **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu della Console di Gestione pacchetti](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="62a7f-440">Nella Console di Gestione pacchetti immettere i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="62a7f-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="62a7f-441">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="62a7f-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="62a7f-442">Eseguire i seguenti comandi dell'interfaccia della riga di comando di .NET Core:</span><span class="sxs-lookup"><span data-stu-id="62a7f-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="62a7f-443">I comandi precedenti generano l'avviso seguente: "nessun tipo specificato per la colonna decimale ' Price ' nel tipo di entità' Movie '.</span><span class="sxs-lookup"><span data-stu-id="62a7f-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="62a7f-444">This will cause values to be silently truncated if they do not fit in the default precision and scale. (I valori saranno quindi automaticamente troncati se non rispettano la precisione e la scala predefinite).</span><span class="sxs-lookup"><span data-stu-id="62a7f-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="62a7f-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Specificare in modo esplicito il tipo di colonna di SQL Server che può supportare tutti i valori usando 'HasColumnType()')"</span><span class="sxs-lookup"><span data-stu-id="62a7f-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="62a7f-446">Ignorare l'avviso poiché verrà risolto in un passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="62a7f-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="62a7f-447">Il comando Migrations genera il codice per creare lo schema del database iniziale.</span><span class="sxs-lookup"><span data-stu-id="62a7f-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="62a7f-448">Lo schema è basato sul modello specificato in `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="62a7f-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="62a7f-449">L'argomento `InitialCreate` viene usato per denominare le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="62a7f-449">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="62a7f-450">È possibile usare qualsiasi nome, ma per convenzione viene selezionato un nome che descrive la migrazione.</span><span class="sxs-lookup"><span data-stu-id="62a7f-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="62a7f-451">Il `update` comando esegue il `Up` Metodo nelle migrazioni che non sono state applicate.</span><span class="sxs-lookup"><span data-stu-id="62a7f-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="62a7f-452">In tal caso, `update` esegue il `Up` metodo nel file  *migrations/ \<time-stamp> _InitialCreate. cs* , che crea il database.</span><span class="sxs-lookup"><span data-stu-id="62a7f-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="62a7f-453">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="62a7f-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="62a7f-454">Esaminare il contesto registrato con l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="62a7f-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="62a7f-455">ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="62a7f-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="62a7f-456">I servizi, ad esempio il contesto del contesto del database EF Core, vengono registrati con l'inserimento delle dipendenze durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="62a7f-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="62a7f-457">I componenti che richiedono questi servizi, ad esempio Razor le pagine, vengono forniti tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="62a7f-457">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="62a7f-458">Il codice del costruttore che ottiene un'istanza del contesto del contesto del database viene illustrato più avanti nell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="62a7f-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="62a7f-459">Lo strumento di ponteggi crea automaticamente un contesto del contesto del database e lo registra con il contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="62a7f-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="62a7f-460">Esaminare il metodo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="62a7f-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="62a7f-461">La riga evidenziata è stata aggiunta dallo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="62a7f-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="62a7f-462">Le `RazorPagesMovieContext` coordinate EF Core funzionalità, ad esempio creazione, lettura, aggiornamento ed eliminazione, per il `Movie` modello.</span><span class="sxs-lookup"><span data-stu-id="62a7f-462">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="62a7f-463">Il contesto dei dati (`RazorPagesMovieContext`) è derivato da [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="62a7f-463">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="62a7f-464">Il contesto dei dati specifica le entità incluse nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="62a7f-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="62a7f-465">Il codice precedente crea una [proprietà \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="62a7f-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="62a7f-466">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="62a7f-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="62a7f-467">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="62a7f-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="62a7f-468">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="62a7f-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="62a7f-469">Per lo sviluppo locale, il [sistema di configurazione](xref:fundamentals/configuration/index) legge la stringa di connessione dal *appsettings.json* file.</span><span class="sxs-lookup"><span data-stu-id="62a7f-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="62a7f-470">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="62a7f-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="62a7f-471">Esaminare il metodo `Up`.</span><span class="sxs-lookup"><span data-stu-id="62a7f-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="62a7f-472">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="62a7f-472">Test the app</span></span>

* <span data-ttu-id="62a7f-473">Eseguire l'app e accodare `/Movies` all'URL nel browser (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="62a7f-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="62a7f-474">Se viene visualizzato l'errore:</span><span class="sxs-lookup"><span data-stu-id="62a7f-474">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="62a7f-475">Non è stato eseguita la [migrazione](#pmc).</span><span class="sxs-lookup"><span data-stu-id="62a7f-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="62a7f-476">Eseguire il test del collegamento **Crea**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-476">Test the **Create** link.</span></span>

  ![Pagina di creazione](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="62a7f-478">Potrebbe non essere possibile immettere virgole decimali nel campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="62a7f-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="62a7f-479">Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese (Stati Uniti), è necessario localizzare l'app.</span><span class="sxs-lookup"><span data-stu-id="62a7f-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="62a7f-480">Per istruzioni sulla globalizzazione, vedere [questo problema su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="62a7f-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="62a7f-481">Testare i collegamenti **Modifica**, **Dettagli** ed **Elimina**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-481">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="62a7f-482">L'esercitazione successiva illustra i file creati tramite scaffolding.</span><span class="sxs-lookup"><span data-stu-id="62a7f-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="62a7f-483">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="62a7f-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="62a7f-484">[Precedente: introduzione](xref:tutorials/razor-pages/razor-pages-start) 
>  Passaggio [successivo: impalcatura Razor Pagine](xref:tutorials/razor-pages/page) di</span><span class="sxs-lookup"><span data-stu-id="62a7f-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="62a7f-485">In questa sezione vengono aggiunte le classi per la gestione dei film in un [database SQLite](https://www.sqlite.org/index.html)multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="62a7f-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="62a7f-486">Le app create da un modello di ASP.NET Core usano un database SQLite.</span><span class="sxs-lookup"><span data-stu-id="62a7f-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="62a7f-487">Le classi modello dell'app vengono usate con [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core provider di database](/ef/core/providers/sqlite)) per lavorare con il database.</span><span class="sxs-lookup"><span data-stu-id="62a7f-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="62a7f-488">EF Core è un framework ORM (Object-Relational Mapping) che semplifica l'accesso ai dati.</span><span class="sxs-lookup"><span data-stu-id="62a7f-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="62a7f-489">Le classi di modello sono dette classi POCO (da "plain-old CLR objects") perché non hanno alcuna dipendenza in EF Core.</span><span class="sxs-lookup"><span data-stu-id="62a7f-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="62a7f-490">Definiscono le proprietà dei dati archiviati nel database.</span><span class="sxs-lookup"><span data-stu-id="62a7f-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="62a7f-491">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="62a7f-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="62a7f-492">Aggiungere un modello di dati</span><span class="sxs-lookup"><span data-stu-id="62a7f-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="62a7f-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="62a7f-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="62a7f-494">Fare clic con il pulsante destro del mouse sul progetto **Razor PagesMovie** > **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-494">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="62a7f-495">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="62a7f-495">Name the folder *Models*.</span></span>

<span data-ttu-id="62a7f-496">Fare clic con il pulsante destro del mouse sulla cartella *modelli* .</span><span class="sxs-lookup"><span data-stu-id="62a7f-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="62a7f-497">Selezionare **Aggiungi**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="62a7f-498">Denominare la classe **Movie**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-498">Name the class **Movie**.</span></span>

<span data-ttu-id="62a7f-499">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="62a7f-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="62a7f-500">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="62a7f-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="62a7f-501">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="62a7f-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="62a7f-502">`[DataType(DataType.Date)]`: L'attributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="62a7f-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="62a7f-503">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="62a7f-503">With this attribute:</span></span>

  * <span data-ttu-id="62a7f-504">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="62a7f-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="62a7f-505">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="62a7f-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="62a7f-506">L'attributo [DataAnnotations](xref:System.ComponentModel.DataAnnotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="62a7f-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="62a7f-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="62a7f-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="62a7f-508">Aggiungere una cartella denominata *Modelli*.</span><span class="sxs-lookup"><span data-stu-id="62a7f-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="62a7f-509">Aggiungere una classe alla cartella *Modello* denominata *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="62a7f-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="62a7f-510">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="62a7f-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="62a7f-511">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="62a7f-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="62a7f-512">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="62a7f-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="62a7f-513">`[DataType(DataType.Date)]`: L'attributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="62a7f-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="62a7f-514">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="62a7f-514">With this attribute:</span></span>

  * <span data-ttu-id="62a7f-515">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="62a7f-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="62a7f-516">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="62a7f-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="62a7f-517">L'attributo [DataAnnotations](xref:System.ComponentModel.DataAnnotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="62a7f-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="62a7f-518">Aggiungere i pacchetti NuGet e gli strumenti EF</span><span class="sxs-lookup"><span data-stu-id="62a7f-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="62a7f-519">Aggiungere una classe di contesto dei dati</span><span class="sxs-lookup"><span data-stu-id="62a7f-519">Add a database context class</span></span>

<span data-ttu-id="62a7f-520">Nel Razor progetto PagesMovie creare una nuova cartella denominata *Data*.</span><span class="sxs-lookup"><span data-stu-id="62a7f-520">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="62a7f-521">Aggiungere la classe `RazorPagesMovieContext` seguente alla cartella *Data*:</span><span class="sxs-lookup"><span data-stu-id="62a7f-521">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="62a7f-522">Il codice precedente crea una proprietà `DbSet` per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="62a7f-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="62a7f-523">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database e un'entità corrisponde a una riga della tabella.</span><span class="sxs-lookup"><span data-stu-id="62a7f-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="62a7f-524">Il codice non verrà compilato fino a quando le dipendenze non verranno aggiunte in un passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="62a7f-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="62a7f-525">Aggiungere una stringa di connessione del database</span><span class="sxs-lookup"><span data-stu-id="62a7f-525">Add a database connection string</span></span>

<span data-ttu-id="62a7f-526">Aggiungere una stringa di connessione al *appsettings.json* file come illustrato nel codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="62a7f-526">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="62a7f-527">Aggiungere i pacchetti NuGet necessari</span><span class="sxs-lookup"><span data-stu-id="62a7f-527">Add required NuGet packages</span></span>

<span data-ttu-id="62a7f-528">Eseguire il comando interfaccia della riga di comando di .NET Core seguente per aggiungere SQLite e CodeGeneration. Design al progetto:</span><span class="sxs-lookup"><span data-stu-id="62a7f-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="62a7f-529">Il pacchetto `Microsoft.VisualStudio.Web.CodeGeneration.Design` è necessario per lo scaffolding.</span><span class="sxs-lookup"><span data-stu-id="62a7f-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="62a7f-530">Registrare il contesto del database</span><span class="sxs-lookup"><span data-stu-id="62a7f-530">Register the database context</span></span>

<span data-ttu-id="62a7f-531">Aggiungere le istruzioni `using` seguenti all'inizio di *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="62a7f-531">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="62a7f-532">Registrare il contesto del database con il contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="62a7f-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="62a7f-533">Compilare il progetto per il controllo di eventuali errori.</span><span class="sxs-lookup"><span data-stu-id="62a7f-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="62a7f-534">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="62a7f-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="62a7f-535">Nella **finestra degli strumenti della soluzione**, fare clic sul progetto *Razor PagesMovie* e quindi selezionare **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-535">In **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="62a7f-536">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="62a7f-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="62a7f-537">CTRL: fare clic sulla cartella *modelli* , quindi selezionare **Aggiungi** > **nuovo file**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="62a7f-538">Nel finestra di dialogo **Nuovo file**:</span><span class="sxs-lookup"><span data-stu-id="62a7f-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="62a7f-539">Selezionare **Generale** nel riquadro a sinistra.</span><span class="sxs-lookup"><span data-stu-id="62a7f-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="62a7f-540">Selezionare **Classe vuota** nel riquadro centrale.</span><span class="sxs-lookup"><span data-stu-id="62a7f-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="62a7f-541">Denominare la classe **Filmato** e selezionare **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="62a7f-542">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="62a7f-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="62a7f-543">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="62a7f-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="62a7f-544">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="62a7f-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="62a7f-545">`[DataType(DataType.Date)]`: L'attributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="62a7f-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="62a7f-546">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="62a7f-546">With this attribute:</span></span>

  * <span data-ttu-id="62a7f-547">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="62a7f-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="62a7f-548">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="62a7f-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="62a7f-549">L'attributo [DataAnnotations](xref:System.ComponentModel.DataAnnotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="62a7f-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="62a7f-550">Compilare il progetto per verificare che non siano presenti errori di compilazione.</span><span class="sxs-lookup"><span data-stu-id="62a7f-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="62a7f-551">Eseguire lo scaffolding del modello di filmato</span><span class="sxs-lookup"><span data-stu-id="62a7f-551">Scaffold the movie model</span></span>

<span data-ttu-id="62a7f-552">In questa sezione viene eseguito lo scaffolding del modello *Movie*.</span><span class="sxs-lookup"><span data-stu-id="62a7f-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="62a7f-553">Lo strumento di scaffolding crea quindi le pagine per le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) per il modello *Movie*.</span><span class="sxs-lookup"><span data-stu-id="62a7f-553">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="62a7f-554">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="62a7f-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="62a7f-555">Creare una cartella *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="62a7f-555">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="62a7f-556">Fare clic con il pulsante destro del mouse sulla cartella *pagine* > **Aggiungi** > **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="62a7f-557">Denominare la cartella *Movies*.</span><span class="sxs-lookup"><span data-stu-id="62a7f-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="62a7f-558">Fare clic con il pulsante destro del mouse sulla cartella *pages/Movies* > **Aggiungi** > **nuovo elemento con impalcatura**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/sca.png)

<span data-ttu-id="62a7f-560">Nella finestra di dialogo **Aggiungi impalcatura** selezionare **Razor pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-560">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffold.png)

<span data-ttu-id="62a7f-562">Completare la finestra di dialogo **Aggiungi Razor pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="62a7f-562">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="62a7f-563">Nell'elenco a discesa **classe modello** selezionare **Movie ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-563">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="62a7f-564">Nella riga della **classe del contesto dati** selezionare il **+** segno (più) e accettare il nome generato **Razor PagesMovie. Models. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="62a7f-565">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-565">Select **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/arp.png)

<span data-ttu-id="62a7f-567">Il *appsettings.json* file viene aggiornato con la stringa di connessione utilizzata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="62a7f-567">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="62a7f-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="62a7f-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="62a7f-569">Aprire una finestra di comando nella directory del progetto che contiene i file *Program.cs*, *Startup.cs* e *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="62a7f-569">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="62a7f-570">**Per Windows**: eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="62a7f-570">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="62a7f-571">**Per MacOS e Linux**: eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="62a7f-571">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="62a7f-572">La tabella seguente illustra in dettaglio le opzioni del generatore di codice ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="62a7f-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="62a7f-573">Opzione</span><span class="sxs-lookup"><span data-stu-id="62a7f-573">Option</span></span>               | <span data-ttu-id="62a7f-574">Descrizione</span><span class="sxs-lookup"><span data-stu-id="62a7f-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="62a7f-575">Nome del modello.</span><span class="sxs-lookup"><span data-stu-id="62a7f-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="62a7f-576">Classe `DbContext` da usare.</span><span class="sxs-lookup"><span data-stu-id="62a7f-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="62a7f-577">Uso del layout predefinito.</span><span class="sxs-lookup"><span data-stu-id="62a7f-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="62a7f-578">Percorso relativo della cartella di output per creare le viste.</span><span class="sxs-lookup"><span data-stu-id="62a7f-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="62a7f-579">Aggiunge `_ValidationScriptsPartial` per modificare e creare pagine</span><span class="sxs-lookup"><span data-stu-id="62a7f-579">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="62a7f-580">Usare l' `-h` opzione per ottenere informazioni sul `aspnet-codegenerator razorpage` comando:</span><span class="sxs-lookup"><span data-stu-id="62a7f-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="62a7f-581">Per ulteriori informazioni, vedere [DotNet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="62a7f-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="62a7f-582">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="62a7f-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="62a7f-583">Creare una cartella *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="62a7f-583">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="62a7f-584">CTRL: fare clic sulla cartella *pagine* > **Aggiungi** > **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="62a7f-585">Denominare la cartella *Movies*.</span><span class="sxs-lookup"><span data-stu-id="62a7f-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="62a7f-586">Control: fare clic sulla cartella *pages/Movies* > **aggiungere** un > **nuovo elemento con impalcatura**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/scaMac.png)

<span data-ttu-id="62a7f-588">Nella finestra di dialogo **Aggiungi nuova impalcatura** selezionare **Razor pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-588">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="62a7f-590">Completare la finestra di dialogo **Aggiungi Razor pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="62a7f-590">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="62a7f-591">Nell'elenco a discesa **classe modello** selezionare o digitare **Movie**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="62a7f-592">Nella riga della **classe del contesto dati** Digitare select the **Razor PagesMovieContext** . verrà creata una nuova classe del contesto di database con lo spazio dei nomi corretto.</span><span class="sxs-lookup"><span data-stu-id="62a7f-592">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="62a7f-593">In questo caso sarà **Razor PagesMovie. Models. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-593">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="62a7f-594">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-594">Select **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/arpMac.png)

<span data-ttu-id="62a7f-596">Il *appsettings.json* file viene aggiornato con la stringa di connessione utilizzata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="62a7f-596">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="62a7f-597">Il processo di scaffolding crea e aggiorna i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="62a7f-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="62a7f-598">File creati</span><span class="sxs-lookup"><span data-stu-id="62a7f-598">Files created</span></span>

* <span data-ttu-id="62a7f-599">*Pagine/filmati*: creare, eliminare, Details, Edit e Index .</span><span class="sxs-lookup"><span data-stu-id="62a7f-599">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="62a7f-600">*Data/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="62a7f-600">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="62a7f-601">File aggiornato</span><span class="sxs-lookup"><span data-stu-id="62a7f-601">File updated</span></span>

* <span data-ttu-id="62a7f-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="62a7f-602">*Startup.cs*</span></span>

<span data-ttu-id="62a7f-603">I file creati e aggiornati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="62a7f-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="62a7f-604">Migrazione iniziale</span><span class="sxs-lookup"><span data-stu-id="62a7f-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="62a7f-605">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="62a7f-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="62a7f-606">In questa sezione viene usata la Console di Gestione pacchetti (PMC) per:</span><span class="sxs-lookup"><span data-stu-id="62a7f-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="62a7f-607">Aggiungere una migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="62a7f-607">Add an initial migration.</span></span>
* <span data-ttu-id="62a7f-608">Aggiornare il database con la migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="62a7f-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="62a7f-609">Nel menu **Strumenti** selezionare **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu della Console di Gestione pacchetti](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="62a7f-611">Nella Console di Gestione pacchetti immettere i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="62a7f-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="62a7f-612">Il comando `Add-Migration` genera un codice per creare lo schema del database iniziale.</span><span class="sxs-lookup"><span data-stu-id="62a7f-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="62a7f-613">Lo schema è basato sul modello specificato in `DbContext` , nel file *Razor PagesMovieContext.cs* .</span><span class="sxs-lookup"><span data-stu-id="62a7f-613">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="62a7f-614">L' `InitialCreate` argomento viene usato per assegnare un nome alla migrazione.</span><span class="sxs-lookup"><span data-stu-id="62a7f-614">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="62a7f-615">È possibile usare qualsiasi nome, ma per convenzione viene usato un nome che descrive la migrazione.</span><span class="sxs-lookup"><span data-stu-id="62a7f-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="62a7f-616">Per altre informazioni, vedere <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="62a7f-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="62a7f-617">Il `Update-Database` comando esegue il `Up` metodo nel file *migrations/ \<time-stamp> _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="62a7f-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="62a7f-618">Il metodo `Up` crea il database.</span><span class="sxs-lookup"><span data-stu-id="62a7f-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="62a7f-619">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="62a7f-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="62a7f-620">Eseguire i seguenti comandi dell'interfaccia della riga di comando di .NET Core:</span><span class="sxs-lookup"><span data-stu-id="62a7f-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="62a7f-621">I comandi precedenti generano l'avviso seguente: "nessun tipo specificato per la colonna decimale ' Price ' nel tipo di entità' Movie '.</span><span class="sxs-lookup"><span data-stu-id="62a7f-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="62a7f-622">This will cause values to be silently truncated if they do not fit in the default precision and scale. (I valori saranno quindi automaticamente troncati se non rispettano la precisione e la scala predefinite).</span><span class="sxs-lookup"><span data-stu-id="62a7f-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="62a7f-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Specificare in modo esplicito il tipo di colonna di SQL Server che può supportare tutti i valori usando 'HasColumnType()')"</span><span class="sxs-lookup"><span data-stu-id="62a7f-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="62a7f-624">Ignorare l'avviso poiché verrà risolto in un passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="62a7f-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="62a7f-625">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="62a7f-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="62a7f-626">Esaminare il contesto registrato con l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="62a7f-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="62a7f-627">ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="62a7f-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="62a7f-628">I servizi (ad esempio il contesto del contesto del database EF Core) vengono registrati con l'inserimento delle dipendenze durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="62a7f-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="62a7f-629">I componenti che richiedono questi servizi, ad esempio Razor le pagine, vengono forniti tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="62a7f-629">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="62a7f-630">Il codice del costruttore che ottiene un'istanza del contesto contextB del contesto di database viene illustrato più avanti nell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="62a7f-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="62a7f-631">Lo strumento di ponteggi crea automaticamente un contesto del contesto del database e lo registra con il contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="62a7f-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="62a7f-632">Esaminare il metodo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="62a7f-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="62a7f-633">La riga evidenziata è stata aggiunta dallo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="62a7f-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="62a7f-634">Le `RazorPagesMovieContext` coordinate EF Core funzionalità, ad esempio creazione, lettura, aggiornamento ed eliminazione, per il `Movie` modello.</span><span class="sxs-lookup"><span data-stu-id="62a7f-634">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="62a7f-635">Il contesto dei dati (`RazorPagesMovieContext`) è derivato da [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="62a7f-635">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="62a7f-636">Il contesto dei dati specifica le entità incluse nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="62a7f-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="62a7f-637">Il codice precedente crea una [proprietà \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="62a7f-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="62a7f-638">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="62a7f-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="62a7f-639">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="62a7f-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="62a7f-640">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="62a7f-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="62a7f-641">Per lo sviluppo locale, il [sistema di configurazione](xref:fundamentals/configuration/index) legge la stringa di connessione dal *appsettings.json* file.</span><span class="sxs-lookup"><span data-stu-id="62a7f-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="62a7f-642">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="62a7f-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="62a7f-643">Esaminare il metodo `Up`.</span><span class="sxs-lookup"><span data-stu-id="62a7f-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="62a7f-644">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="62a7f-644">Test the app</span></span>

* <span data-ttu-id="62a7f-645">Eseguire l'app e accodare `/Movies` all'URL nel browser (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="62a7f-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="62a7f-646">Se viene visualizzato l'errore:</span><span class="sxs-lookup"><span data-stu-id="62a7f-646">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="62a7f-647">Non è stato eseguita la [migrazione](#pmc).</span><span class="sxs-lookup"><span data-stu-id="62a7f-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="62a7f-648">Eseguire il test del collegamento **Crea**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-648">Test the **Create** link.</span></span>

  ![Pagina di creazione](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="62a7f-650">Potrebbe non essere possibile immettere virgole decimali nel campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="62a7f-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="62a7f-651">Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese (Stati Uniti), è necessario localizzare l'app.</span><span class="sxs-lookup"><span data-stu-id="62a7f-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="62a7f-652">Per istruzioni sulla globalizzazione, vedere [questo problema su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="62a7f-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="62a7f-653">Testare i collegamenti **Modifica**, **Dettagli** ed **Elimina**.</span><span class="sxs-lookup"><span data-stu-id="62a7f-653">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="62a7f-654">L'esercitazione successiva illustra i file creati tramite scaffolding.</span><span class="sxs-lookup"><span data-stu-id="62a7f-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="62a7f-655">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="62a7f-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="62a7f-656">[Precedente: introduzione](xref:tutorials/razor-pages/razor-pages-start) 
>  Passaggio [successivo: impalcatura Razor Pagine](xref:tutorials/razor-pages/page) di</span><span class="sxs-lookup"><span data-stu-id="62a7f-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
