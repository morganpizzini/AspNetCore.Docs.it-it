---
title: Parte 2, aggiungere un modello
author: rick-anderson
description: 'Parte 2 della serie di esercitazioni sulle Razor pagine. In questa sezione vengono aggiunte le classi del modello.'
ms.author: riande
ms.date: 09/30/2020
no-loc:
- 'Index'
- 'Create'
- 'Delete'
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: ee1b7d77d8d209a11669ba29c8a951c59368180e
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94361080"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="454fa-104">Parte 2, aggiungere un modello a un' Razor app di pagine in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="454fa-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="454fa-105">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="454fa-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="454fa-106">In questa sezione si aggiungono alcune classi per la gestione di filmati in un database.</span><span class="sxs-lookup"><span data-stu-id="454fa-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="454fa-107">Le classi modello dell'app usano [Entity Framework Core (EF Core)](/ef/core) per lavorare con il database.</span><span class="sxs-lookup"><span data-stu-id="454fa-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="454fa-108">EF Core è un mapper relazionale a oggetti (O/RM) che semplifica l'accesso ai dati.</span><span class="sxs-lookup"><span data-stu-id="454fa-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="454fa-109">Prima di tutto si scrivono le classi del modello e EF Core crea il database.</span><span class="sxs-lookup"><span data-stu-id="454fa-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="454fa-110">Le classi del modello sono note come classi POCO (da " **P** Lain- **o** LD **C** LR **o** gli oggetti") perché non hanno una dipendenza da EF core.</span><span class="sxs-lookup"><span data-stu-id="454fa-110">The model classes are known as POCO classes (from " **P** lain- **O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="454fa-111">Definiscono le proprietà dei dati archiviati nel database.</span><span class="sxs-lookup"><span data-stu-id="454fa-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="454fa-112">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="454fa-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="454fa-113">Aggiungere un modello di dati</span><span class="sxs-lookup"><span data-stu-id="454fa-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="454fa-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="454fa-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="454fa-115">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto *Razor PagesMovie* > **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="454fa-115">In **Solution Explorer** , right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="454fa-116">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="454fa-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="454fa-117">Fare clic con il pulsante destro del mouse sulla cartella *modelli* .</span><span class="sxs-lookup"><span data-stu-id="454fa-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="454fa-118">Selezionare **Aggiungi**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="454fa-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="454fa-119">Denominare la classe *Movie*.</span><span class="sxs-lookup"><span data-stu-id="454fa-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="454fa-120">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="454fa-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="454fa-121">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="454fa-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="454fa-122">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="454fa-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="454fa-123">`[DataType(DataType.Date)]`: L'attributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="454fa-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="454fa-124">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="454fa-124">With this attribute:</span></span>

  * <span data-ttu-id="454fa-125">L'utente non deve immettere le informazioni sull'ora nel campo Data.</span><span class="sxs-lookup"><span data-stu-id="454fa-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="454fa-126">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="454fa-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="454fa-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="454fa-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="454fa-128">Aggiungere una cartella denominata *Modelli*.</span><span class="sxs-lookup"><span data-stu-id="454fa-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="454fa-129">Aggiungere una classe alla cartella *Modello* denominata *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="454fa-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="454fa-130">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="454fa-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="454fa-131">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="454fa-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="454fa-132">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="454fa-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="454fa-133">`[DataType(DataType.Date)]`: L'attributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="454fa-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="454fa-134">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="454fa-134">With this attribute:</span></span>

  * <span data-ttu-id="454fa-135">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="454fa-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="454fa-136">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="454fa-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="454fa-137">Aggiungere i pacchetti NuGet e gli strumenti EF</span><span class="sxs-lookup"><span data-stu-id="454fa-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="454fa-138">Aggiungere una classe di contesto dei dati</span><span class="sxs-lookup"><span data-stu-id="454fa-138">Add a database context class</span></span>

1. <span data-ttu-id="454fa-139">Nel progetto *Razor PagesMovie* creare una cartella denominata *Data*.</span><span class="sxs-lookup"><span data-stu-id="454fa-139">In the *RazorPagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="454fa-140">Nella cartella *dati* aggiungere un file denominato *Razor PagesMovieContext.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="454fa-140">In the *Data* folder, add a file named *RazorPagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   <span data-ttu-id="454fa-141">Il codice precedente crea una proprietà `DbSet` per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="454fa-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="454fa-142">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database e un'entità corrisponde a una riga della tabella.</span><span class="sxs-lookup"><span data-stu-id="454fa-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="454fa-143">Il codice non verrà compilato fino a quando le dipendenze non verranno aggiunte in un passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="454fa-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="454fa-144">Aggiungere una stringa di connessione del database</span><span class="sxs-lookup"><span data-stu-id="454fa-144">Add a database connection string</span></span>

<span data-ttu-id="454fa-145">Aggiungere una stringa di connessione al *appsettings.json* file come illustrato nel codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="454fa-145">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="454fa-146">Registrare il contesto del database</span><span class="sxs-lookup"><span data-stu-id="454fa-146">Register the database context</span></span>

1. <span data-ttu-id="454fa-147">Aggiungere le istruzioni `using` seguenti all'inizio di *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="454fa-147">Add the following `using` statements at the top of *Startup.cs* :</span></span>

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="454fa-148">Registrare il contesto del database con il contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="454fa-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="454fa-149">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="454fa-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="454fa-150">Nella **finestra degli strumenti della soluzione** , fare clic sul progetto *Razor PagesMovie* e quindi selezionare **Aggiungi** > **nuova cartella.** Assegnare un nome ai *modelli* di cartella.</span><span class="sxs-lookup"><span data-stu-id="454fa-150">In the **Solution Tool Window** , control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="454fa-151">CTRL-fare clic sulla cartella *modelli* , quindi selezionare **Aggiungi** > **nuovo file.**...</span><span class="sxs-lookup"><span data-stu-id="454fa-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="454fa-152">Nel finestra di dialogo **Nuovo file** :</span><span class="sxs-lookup"><span data-stu-id="454fa-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="454fa-153">Selezionare **Generale** nel riquadro a sinistra.</span><span class="sxs-lookup"><span data-stu-id="454fa-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="454fa-154">Selezionare **Classe vuota** nel riquadro centrale.</span><span class="sxs-lookup"><span data-stu-id="454fa-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="454fa-155">Denominare la classe **Filmato** e selezionare **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="454fa-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="454fa-156">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="454fa-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="454fa-157">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="454fa-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="454fa-158">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="454fa-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="454fa-159">`[DataType(DataType.Date)]`: L'attributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="454fa-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="454fa-160">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="454fa-160">With this attribute:</span></span>

  * <span data-ttu-id="454fa-161">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="454fa-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="454fa-162">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="454fa-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="454fa-163">L'attributo [DataAnnotations](xref:System.ComponentModel.DataAnnotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="454fa-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="454fa-164">Compilare il progetto per verificare che non siano presenti errori di compilazione.</span><span class="sxs-lookup"><span data-stu-id="454fa-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="454fa-165">Eseguire lo scaffolding del modello di filmato</span><span class="sxs-lookup"><span data-stu-id="454fa-165">Scaffold the movie model</span></span>

<span data-ttu-id="454fa-166">In questa sezione viene eseguito lo scaffolding del modello *Movie*.</span><span class="sxs-lookup"><span data-stu-id="454fa-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="454fa-167">Ovvero, lo strumento di impalcatura produce pagine per Create le operazioni di lettura, aggiornamento e Delete (CRUD) per il modello di film.</span><span class="sxs-lookup"><span data-stu-id="454fa-167">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="454fa-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="454fa-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="454fa-169">Create una cartella *pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="454fa-169">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="454fa-170">Fare clic con il pulsante destro del mouse sulla cartella *pagine* > **Aggiungi** > **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="454fa-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="454fa-171">Denominare la cartella *Movies*.</span><span class="sxs-lookup"><span data-stu-id="454fa-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="454fa-172">Fare clic con il pulsante destro del mouse sulla cartella *pages/Movies* > **Aggiungi** > **nuovo elemento con impalcatura**.</span><span class="sxs-lookup"><span data-stu-id="454fa-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![Immagine relativa alle istruzioni precedenti.](model/_static/5/sca.png)

1. <span data-ttu-id="454fa-174">Nella finestra di dialogo **Aggiungi impalcatura** selezionare **Razor pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="454fa-174">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffold.png)

1. <span data-ttu-id="454fa-176">Completare la finestra di dialogo **Aggiungi Razor pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="454fa-176">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="454fa-177">Nell'elenco a discesa **classe modello** selezionare **Movie ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="454fa-177">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="454fa-178">Nella riga **Classe contesto di dati** selezionare il segno più **+**.</span><span class="sxs-lookup"><span data-stu-id="454fa-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="454fa-179">Nella finestra di dialogo **Aggiungi contesto dati** il nome della classe *Razor PagesMovie. Data. Razor* Viene generato PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="454fa-179">In the **Add Data Context** dialog, the class name *RazorPagesMovie.Data.RazorPagesMovieContext* is generated.</span></span>
   1. <span data-ttu-id="454fa-180">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="454fa-180">Select **Add**.</span></span>

   ![Immagine relativa alle istruzioni precedenti.](model/_static/3/arp.png)

<span data-ttu-id="454fa-182">Il *appsettings.json* file viene aggiornato con la stringa di connessione utilizzata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="454fa-182">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="454fa-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="454fa-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="454fa-184">Aprire una shell dei comandi nella directory del progetto che contiene i file *Program.cs* , *Startup.cs* e *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="454fa-184">Open a command shell to the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="454fa-185">**Per Windows** : eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="454fa-185">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="454fa-186">**Per MacOS e Linux** : eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="454fa-186">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="454fa-187">La tabella seguente illustra in dettaglio le opzioni del generatore di codice ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="454fa-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="454fa-188">Opzione</span><span class="sxs-lookup"><span data-stu-id="454fa-188">Option</span></span>               | <span data-ttu-id="454fa-189">Descrizione</span><span class="sxs-lookup"><span data-stu-id="454fa-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="454fa-190">Nome del modello.</span><span class="sxs-lookup"><span data-stu-id="454fa-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="454fa-191">Classe `DbContext` da usare.</span><span class="sxs-lookup"><span data-stu-id="454fa-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="454fa-192">Uso del layout predefinito.</span><span class="sxs-lookup"><span data-stu-id="454fa-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="454fa-193">Percorso relativo della cartella di output per creare le viste.</span><span class="sxs-lookup"><span data-stu-id="454fa-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="454fa-194">Aggiunge `_ValidationScriptsPartial` a Edit e Create pages</span><span class="sxs-lookup"><span data-stu-id="454fa-194">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="454fa-195">Usare l' `-h` opzione per ottenere informazioni sul `aspnet-codegenerator razorpage` comando:</span><span class="sxs-lookup"><span data-stu-id="454fa-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="454fa-196">Per ulteriori informazioni, vedere [DotNet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="454fa-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="454fa-197">Usare SQLite per lo sviluppo, SQL Server per la produzione</span><span class="sxs-lookup"><span data-stu-id="454fa-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="454fa-198">Quando si seleziona SQLite, il codice generato dal modello è pronto per lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="454fa-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="454fa-199">Nel codice seguente viene illustrato come inserire <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in `Startup` .</span><span class="sxs-lookup"><span data-stu-id="454fa-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="454fa-200">`IWebHostEnvironment` viene inserito in modo che l'app possa usare SQLite in fase di sviluppo e SQL Server nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="454fa-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="454fa-201">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="454fa-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="454fa-202">Create una cartella *pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="454fa-202">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="454fa-203">CTRL: fare clic sulla cartella *pagine* > **Aggiungi** > **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="454fa-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="454fa-204">Denominare la cartella *Movies*.</span><span class="sxs-lookup"><span data-stu-id="454fa-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="454fa-205">CTRL: fare clic sulla cartella *pagine/filmati* > **Aggiungi** > **Nuova impalcatura...**.</span><span class="sxs-lookup"><span data-stu-id="454fa-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![Immagine relativa alle istruzioni precedenti.](model/_static/scaMac.png)

1. <span data-ttu-id="454fa-207">Nella finestra di dialogo **Nuova impalcatura** selezionare **Razor pagine con Entity Framework (CRUD)** > **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="454fa-207">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="454fa-209">Completare la finestra di dialogo **Aggiungi Razor pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="454fa-209">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="454fa-210">Nella **classe DbContext da usare:** Row, denominare la classe *Razor PagesMovie. Data. Razor PagesMovieContext*.</span><span class="sxs-lookup"><span data-stu-id="454fa-210">In the **DbContext Class to use:** row, name the class *RazorPagesMovie.Data.RazorPagesMovieContext*.</span></span>
   1. <span data-ttu-id="454fa-211">Selezionare **Fine**.</span><span class="sxs-lookup"><span data-stu-id="454fa-211">Select **Finish**.</span></span>

   ![Immagine relativa alle istruzioni precedenti.](model/_static/5/arpMac.png)

<span data-ttu-id="454fa-213">Il *appsettings.json* file viene aggiornato con la stringa di connessione utilizzata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="454fa-213">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="454fa-214">Usare SQLite per lo sviluppo, SQL Server per la produzione</span><span class="sxs-lookup"><span data-stu-id="454fa-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="454fa-215">Quando si seleziona SQLite, il codice generato dal modello è pronto per lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="454fa-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="454fa-216">Nel codice seguente viene illustrato come inserire <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in `Startup` .</span><span class="sxs-lookup"><span data-stu-id="454fa-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="454fa-217">`IWebHostEnvironment` viene inserito in modo che l'app possa usare SQLite in fase di sviluppo e SQL Server nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="454fa-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="454fa-218">File creati</span><span class="sxs-lookup"><span data-stu-id="454fa-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="454fa-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="454fa-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="454fa-220">Il processo di scaffolding crea e aggiorna i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="454fa-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="454fa-221">*Pagine/film* : Create , Delete , Details, Edit e Index .</span><span class="sxs-lookup"><span data-stu-id="454fa-221">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="454fa-222">*Data/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="454fa-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="454fa-223">Aggiornato</span><span class="sxs-lookup"><span data-stu-id="454fa-223">Updated</span></span>

* <span data-ttu-id="454fa-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="454fa-224">*Startup.cs*</span></span>

<span data-ttu-id="454fa-225">I file creati e aggiornati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="454fa-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="454fa-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="454fa-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="454fa-227">Il processo di scaffolding crea i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="454fa-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="454fa-228">*Pagine/film* : Create , Delete , Details, Edit e Index .</span><span class="sxs-lookup"><span data-stu-id="454fa-228">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="454fa-229">I file creati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="454fa-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="454fa-230">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="454fa-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="454fa-231">Il processo di scaffolding crea e aggiorna i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="454fa-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="454fa-232">*Pagine/film* : Create , Delete , Details, Edit e Index .</span><span class="sxs-lookup"><span data-stu-id="454fa-232">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="454fa-233">*Data/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="454fa-233">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="454fa-234">Aggiornato</span><span class="sxs-lookup"><span data-stu-id="454fa-234">Updated</span></span>

* <span data-ttu-id="454fa-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="454fa-235">*Startup.cs*</span></span>

<span data-ttu-id="454fa-236">I file creati e aggiornati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="454fa-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="no-loccreate-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="454fa-237">Create schema iniziale del database con la funzionalità di migrazione di EF</span><span class="sxs-lookup"><span data-stu-id="454fa-237">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="454fa-238">La funzionalità migrazioni di Entity Framework Core fornisce un modo per:</span><span class="sxs-lookup"><span data-stu-id="454fa-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="454fa-239">Create schema iniziale del database.</span><span class="sxs-lookup"><span data-stu-id="454fa-239">Create the initial database schema.</span></span>
* <span data-ttu-id="454fa-240">Aggiornare in modo incrementale lo schema del database per mantenerlo sincronizzato con il modello di dati dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="454fa-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="454fa-241">Vengono conservati i dati esistenti nel database.</span><span class="sxs-lookup"><span data-stu-id="454fa-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="454fa-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="454fa-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="454fa-243">In questa sezione viene usata la finestra **console di gestione pacchetti** (PMC) per:</span><span class="sxs-lookup"><span data-stu-id="454fa-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="454fa-244">Aggiungere una migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="454fa-244">Add an initial migration.</span></span>
* <span data-ttu-id="454fa-245">Aggiornare il database con la migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="454fa-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="454fa-246">Nel menu **Strumenti** selezionare **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="454fa-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![Menu della Console di Gestione pacchetti](model/_static/5/pmc.png)

1. <span data-ttu-id="454fa-248">Nella Console di Gestione pacchetti immettere i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="454fa-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="454fa-249">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="454fa-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="454fa-250">Eseguire i seguenti comandi dell'interfaccia della riga di comando .NET:</span><span class="sxs-lookup"><span data-stu-id="454fa-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="454fa-251">I comandi precedenti generano l'avviso seguente: "nessun tipo specificato per la colonna decimale ' Price ' nel tipo di entità' Movie '.</span><span class="sxs-lookup"><span data-stu-id="454fa-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="454fa-252">This will cause values to be silently truncated if they do not fit in the default precision and scale. (I valori saranno quindi automaticamente troncati se non rispettano la precisione e la scala predefinite).</span><span class="sxs-lookup"><span data-stu-id="454fa-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="454fa-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Specificare in modo esplicito il tipo di colonna di SQL Server che può supportare tutti i valori usando 'HasColumnType()')"</span><span class="sxs-lookup"><span data-stu-id="454fa-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="454fa-254">Ignorare l'avviso poiché verrà risolto in un passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="454fa-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="454fa-255">Il comando `migrations` genera un codice per creare lo schema del database iniziale.</span><span class="sxs-lookup"><span data-stu-id="454fa-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="454fa-256">Lo schema è basato sul modello specificato in `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="454fa-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="454fa-257">L'argomento `InitialCreate` viene usato per denominare le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="454fa-257">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="454fa-258">È possibile usare qualsiasi nome, ma per convenzione viene selezionato un nome che descrive la migrazione.</span><span class="sxs-lookup"><span data-stu-id="454fa-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="454fa-259">Il `update` comando esegue il `Up` Metodo nelle migrazioni che non sono state applicate.</span><span class="sxs-lookup"><span data-stu-id="454fa-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="454fa-260">In tal caso, `update` esegue il `Up` metodo nel file *migrations/ \<time-stamp> _Initial Create . cs* , che crea il database.</span><span class="sxs-lookup"><span data-stu-id="454fa-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="454fa-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="454fa-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="454fa-262">Esaminare il contesto registrato con l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="454fa-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="454fa-263">ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="454fa-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="454fa-264">I servizi, ad esempio il contesto di database EF Core, vengono registrati con l'inserimento delle dipendenze durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="454fa-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="454fa-265">I componenti che richiedono questi servizi, ad esempio Razor le pagine, vengono forniti tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="454fa-265">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="454fa-266">Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="454fa-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="454fa-267">Lo strumento di ponteggi crea automaticamente un contesto di database e lo registra con il contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="454fa-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="454fa-268">Esaminare il metodo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="454fa-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="454fa-269">La riga evidenziata è stata aggiunta dallo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="454fa-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="454fa-270">Le `RazorPagesMovieContext` coordinate EF Core funzionalità, ad esempio Create , Read, Update e Delete , per il `Movie` modello.</span><span class="sxs-lookup"><span data-stu-id="454fa-270">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="454fa-271">Il contesto dei dati (`RazorPagesMovieContext`) è derivato da [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="454fa-271">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="454fa-272">Il contesto dei dati specifica le entità incluse nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="454fa-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="454fa-273">Il codice precedente crea una [proprietà \<Movie> DbSet](xref:Microsoft.EntityFrameworkCore.DbSet%601) per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="454fa-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="454fa-274">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="454fa-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="454fa-275">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="454fa-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="454fa-276">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="454fa-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="454fa-277">Per lo sviluppo locale, il [sistema di configurazione](xref:fundamentals/configuration/index) legge la stringa di connessione dal *appsettings.json* file.</span><span class="sxs-lookup"><span data-stu-id="454fa-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="454fa-278">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="454fa-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="454fa-279">Esaminare il metodo `Up`.</span><span class="sxs-lookup"><span data-stu-id="454fa-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="454fa-280">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="454fa-280">Test the app</span></span>

1. <span data-ttu-id="454fa-281">Eseguire l'app e accodare `/Movies` all'URL nel browser (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="454fa-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="454fa-282">Se si riceve un messaggio di errore che indica che</span><span class="sxs-lookup"><span data-stu-id="454fa-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="454fa-283">Non è stato eseguita la [migrazione](#pmc).</span><span class="sxs-lookup"><span data-stu-id="454fa-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="454fa-284">Testare il **Create** collegamento.</span><span class="sxs-lookup"><span data-stu-id="454fa-284">Test the **Create** link.</span></span>

   ![::: NO-LOC (Create)::: page](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="454fa-286">Potrebbe non essere possibile immettere virgole decimali nel campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="454fa-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="454fa-287">Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese (Stati Uniti), è necessario localizzare l'app.</span><span class="sxs-lookup"><span data-stu-id="454fa-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="454fa-288">Per istruzioni sulla globalizzazione, vedere [questo problema su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="454fa-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="454fa-289">Testare la **modifica** , **i dettagli** e i **Delete** collegamenti.</span><span class="sxs-lookup"><span data-stu-id="454fa-289">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="454fa-290">L'esercitazione successiva illustra i file creati tramite scaffolding.</span><span class="sxs-lookup"><span data-stu-id="454fa-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="454fa-291">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="454fa-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="454fa-292">[Precedente: introduzione](xref:tutorials/razor-pages/razor-pages-start) 
>  Passaggio [successivo: impalcatura Razor Pagine](xref:tutorials/razor-pages/page) di</span><span class="sxs-lookup"><span data-stu-id="454fa-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="454fa-293">In questa sezione vengono aggiunte le classi per la gestione dei filmati.</span><span class="sxs-lookup"><span data-stu-id="454fa-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="454fa-294">Le classi modello dell'app usano [Entity Framework Core (EF Core)](/ef/core) per lavorare con il database.</span><span class="sxs-lookup"><span data-stu-id="454fa-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="454fa-295">EF Core è un mapper relazionale a oggetti (O/RM) che semplifica l'accesso ai dati.</span><span class="sxs-lookup"><span data-stu-id="454fa-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="454fa-296">Le classi di modello sono dette classi POCO (da "plain-old CLR objects") perché non hanno alcuna dipendenza in EF Core.</span><span class="sxs-lookup"><span data-stu-id="454fa-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="454fa-297">Definiscono le proprietà dei dati archiviati nel database.</span><span class="sxs-lookup"><span data-stu-id="454fa-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="454fa-298">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="454fa-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="454fa-299">Aggiungere un modello di dati</span><span class="sxs-lookup"><span data-stu-id="454fa-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="454fa-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="454fa-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="454fa-301">Fare clic con il pulsante destro del mouse sul progetto **Razor PagesMovie** > **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="454fa-301">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="454fa-302">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="454fa-302">Name the folder *Models*.</span></span>

<span data-ttu-id="454fa-303">Fare clic con il pulsante destro del mouse sulla cartella *modelli* .</span><span class="sxs-lookup"><span data-stu-id="454fa-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="454fa-304">Selezionare **Aggiungi**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="454fa-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="454fa-305">Denominare la classe **Movie**.</span><span class="sxs-lookup"><span data-stu-id="454fa-305">Name the class **Movie**.</span></span>

<span data-ttu-id="454fa-306">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="454fa-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="454fa-307">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="454fa-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="454fa-308">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="454fa-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="454fa-309">`[DataType(DataType.Date)]`: L'attributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="454fa-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="454fa-310">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="454fa-310">With this attribute:</span></span>

  * <span data-ttu-id="454fa-311">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="454fa-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="454fa-312">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="454fa-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="454fa-313">L'attributo [DataAnnotations](xref:System.ComponentModel.DataAnnotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="454fa-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="454fa-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="454fa-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="454fa-315">Aggiungere una cartella denominata *Modelli*.</span><span class="sxs-lookup"><span data-stu-id="454fa-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="454fa-316">Aggiungere una classe alla cartella *Modello* denominata *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="454fa-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="454fa-317">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="454fa-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="454fa-318">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="454fa-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="454fa-319">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="454fa-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="454fa-320">`[DataType(DataType.Date)]`: L'attributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="454fa-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="454fa-321">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="454fa-321">With this attribute:</span></span>

  * <span data-ttu-id="454fa-322">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="454fa-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="454fa-323">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="454fa-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="454fa-324">L'attributo [DataAnnotations](xref:System.ComponentModel.DataAnnotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="454fa-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="454fa-325">Aggiungere i pacchetti NuGet e gli strumenti EF</span><span class="sxs-lookup"><span data-stu-id="454fa-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="454fa-326">Aggiungere una classe di contesto dei dati</span><span class="sxs-lookup"><span data-stu-id="454fa-326">Add a database context class</span></span>

* <span data-ttu-id="454fa-327">Nel progetto *Razor PagesMovie* creare una nuova cartella denominata *Data*.</span><span class="sxs-lookup"><span data-stu-id="454fa-327">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="454fa-328">Aggiungere la classe `RazorPagesMovieContext` seguente alla cartella *Data* :</span><span class="sxs-lookup"><span data-stu-id="454fa-328">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="454fa-329">Il codice precedente crea una proprietà `DbSet` per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="454fa-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="454fa-330">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database e un'entità corrisponde a una riga della tabella.</span><span class="sxs-lookup"><span data-stu-id="454fa-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="454fa-331">Il codice non verrà compilato fino a quando le dipendenze non verranno aggiunte in un passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="454fa-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="454fa-332">Aggiungere una stringa di connessione del database</span><span class="sxs-lookup"><span data-stu-id="454fa-332">Add a database connection string</span></span>

<span data-ttu-id="454fa-333">Aggiungere una stringa di connessione al *appsettings.json* file come illustrato nel codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="454fa-333">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="454fa-334">Registrare il contesto del database</span><span class="sxs-lookup"><span data-stu-id="454fa-334">Register the database context</span></span>

<span data-ttu-id="454fa-335">Aggiungere le istruzioni `using` seguenti all'inizio di *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="454fa-335">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="454fa-336">Registrare il contesto del database con il contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="454fa-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="454fa-337">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="454fa-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="454fa-338">Nella **finestra degli strumenti della soluzione** , fare clic sul progetto **Razor PagesMovie** e quindi selezionare **Aggiungi** > **nuova cartella.** Assegnare un nome ai *modelli* di cartella.</span><span class="sxs-lookup"><span data-stu-id="454fa-338">In the **Solution Tool Window** , control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="454fa-339">Fare clic con il pulsante destro del mouse sulla cartella *Models* , quindi scegliere **Aggiungi** > **nuovo file**.</span><span class="sxs-lookup"><span data-stu-id="454fa-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="454fa-340">Nel finestra di dialogo **Nuovo file** :</span><span class="sxs-lookup"><span data-stu-id="454fa-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="454fa-341">Selezionare **Generale** nel riquadro a sinistra.</span><span class="sxs-lookup"><span data-stu-id="454fa-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="454fa-342">Selezionare **Classe vuota** nel riquadro centrale.</span><span class="sxs-lookup"><span data-stu-id="454fa-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="454fa-343">Denominare la classe **Filmato** e selezionare **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="454fa-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="454fa-344">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="454fa-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="454fa-345">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="454fa-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="454fa-346">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="454fa-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="454fa-347">`[DataType(DataType.Date)]`: L'attributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="454fa-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="454fa-348">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="454fa-348">With this attribute:</span></span>

  * <span data-ttu-id="454fa-349">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="454fa-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="454fa-350">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="454fa-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="454fa-351">L'attributo [DataAnnotations](xref:System.ComponentModel.DataAnnotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="454fa-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="454fa-352">Compilare il progetto per verificare che non siano presenti errori di compilazione.</span><span class="sxs-lookup"><span data-stu-id="454fa-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="454fa-353">Eseguire lo scaffolding del modello di filmato</span><span class="sxs-lookup"><span data-stu-id="454fa-353">Scaffold the movie model</span></span>

<span data-ttu-id="454fa-354">In questa sezione viene eseguito lo scaffolding del modello *Movie*.</span><span class="sxs-lookup"><span data-stu-id="454fa-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="454fa-355">Ovvero, lo strumento di impalcatura produce pagine per Create le operazioni di lettura, aggiornamento e Delete (CRUD) per il modello di film.</span><span class="sxs-lookup"><span data-stu-id="454fa-355">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="454fa-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="454fa-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="454fa-357">Create una cartella *pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="454fa-357">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="454fa-358">Fare clic con il pulsante destro del mouse sulla cartella *pagine* > **Aggiungi** > **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="454fa-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="454fa-359">Denominare la cartella *Movies*.</span><span class="sxs-lookup"><span data-stu-id="454fa-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="454fa-360">Fare clic con il pulsante destro del mouse sulla cartella *pages/Movies* > **Aggiungi** > **nuovo elemento con impalcatura**.</span><span class="sxs-lookup"><span data-stu-id="454fa-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/sca.png)

<span data-ttu-id="454fa-362">Nella finestra di dialogo **Aggiungi impalcatura** selezionare **Razor pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="454fa-362">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffold.png)

<span data-ttu-id="454fa-364">Completare la finestra di dialogo **Aggiungi Razor pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="454fa-364">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="454fa-365">Nell'elenco a discesa **classe modello** selezionare **Movie ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="454fa-365">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="454fa-366">Nella riga della **classe del contesto dati** selezionare il **+** segno (segno più) e modificare il nome generato da Razor PagesMovie. **Modelli**. Razor Da PagesMovieContext a Razor PagesMovie. **Dati**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="454fa-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie. **Models**.RazorPagesMovieContext to RazorPagesMovie. **Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="454fa-367">[Questa modifica](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) non è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="454fa-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="454fa-368">Crea la classe del contesto di database con lo spazio dei nomi corretto.</span><span class="sxs-lookup"><span data-stu-id="454fa-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="454fa-369">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="454fa-369">Select **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/3/arp.png)

<span data-ttu-id="454fa-371">Il *appsettings.json* file viene aggiornato con la stringa di connessione utilizzata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="454fa-371">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="454fa-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="454fa-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="454fa-373">Aprire una finestra di comando nella directory del progetto che contiene i file *Program.cs* , *Startup.cs* e *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="454fa-373">Open a command window in the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="454fa-374">**Per Windows** : eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="454fa-374">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="454fa-375">**Per MacOS e Linux** : eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="454fa-375">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="454fa-376">La tabella seguente illustra in dettaglio le opzioni del generatore di codice ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="454fa-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="454fa-377">Opzione</span><span class="sxs-lookup"><span data-stu-id="454fa-377">Option</span></span>               | <span data-ttu-id="454fa-378">Descrizione</span><span class="sxs-lookup"><span data-stu-id="454fa-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="454fa-379">Nome del modello.</span><span class="sxs-lookup"><span data-stu-id="454fa-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="454fa-380">Classe `DbContext` da usare.</span><span class="sxs-lookup"><span data-stu-id="454fa-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="454fa-381">Uso del layout predefinito.</span><span class="sxs-lookup"><span data-stu-id="454fa-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="454fa-382">Percorso relativo della cartella di output per creare le viste.</span><span class="sxs-lookup"><span data-stu-id="454fa-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="454fa-383">Aggiunge `_ValidationScriptsPartial` a Edit e Create pages</span><span class="sxs-lookup"><span data-stu-id="454fa-383">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="454fa-384">Usare l' `-h` opzione per ottenere informazioni sul `aspnet-codegenerator razorpage` comando:</span><span class="sxs-lookup"><span data-stu-id="454fa-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="454fa-385">Per ulteriori informazioni, vedere [DotNet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="454fa-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="454fa-386">Usare SQLite per lo sviluppo, SQL Server per la produzione</span><span class="sxs-lookup"><span data-stu-id="454fa-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="454fa-387">Quando si seleziona SQLite, il codice generato dal modello è pronto per lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="454fa-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="454fa-388">Il codice seguente illustra come inserire all' <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> avvio.</span><span class="sxs-lookup"><span data-stu-id="454fa-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="454fa-389">`IWebHostEnvironment` viene inserito in modo `ConfigureServices` che possa usare SQLite per lo sviluppo e la SQL Server nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="454fa-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="454fa-390">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="454fa-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="454fa-391">Create una cartella *pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="454fa-391">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="454fa-392">Fare clic con il pulsante destro del mouse sulla cartella *pagine* > **Aggiungi** > **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="454fa-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="454fa-393">Denominare la cartella *Movies*.</span><span class="sxs-lookup"><span data-stu-id="454fa-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="454fa-394">Fare clic con il pulsante destro del mouse sulla cartella *pages/Movies* > **Aggiungi** > **Nuova impalcatura...**.</span><span class="sxs-lookup"><span data-stu-id="454fa-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/scaMac.png)

<span data-ttu-id="454fa-396">Nella finestra di dialogo **Nuova impalcatura** selezionare **Razor pagine con Entity Framework (CRUD)** > **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="454fa-396">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="454fa-398">Completare la finestra di dialogo **Aggiungi Razor pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="454fa-398">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="454fa-399">Nell'elenco a discesa **classe modello** selezionare o digitare **Movie ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="454fa-399">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="454fa-400">Nella riga della **classe del contesto dati** Digitare il nome della nuova classe, Razor PagesMovie. **Dati**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="454fa-400">In the **Data context class** row, type the name for the new class, RazorPagesMovie. **Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="454fa-401">[Questa modifica](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) non è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="454fa-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="454fa-402">Crea la classe del contesto di database con lo spazio dei nomi corretto.</span><span class="sxs-lookup"><span data-stu-id="454fa-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="454fa-403">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="454fa-403">Select **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/arpMac.png)

<span data-ttu-id="454fa-405">Il *appsettings.json* file viene aggiornato con la stringa di connessione utilizzata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="454fa-405">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="454fa-406">Aggiungi strumenti EF</span><span class="sxs-lookup"><span data-stu-id="454fa-406">Add EF tools</span></span>

<span data-ttu-id="454fa-407">Eseguire il comando interfaccia della riga di comando di .NET Core seguente:</span><span class="sxs-lookup"><span data-stu-id="454fa-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="454fa-408">Il comando precedente aggiunge gli strumenti Entity Framework Core per l'interfaccia della riga di comando di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="454fa-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="454fa-409">Per ulteriori informazioni, vedere [riferimento agli strumenti Entity Framework Core-interfaccia della riga di comando di .NET Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="454fa-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="454fa-410">Usare SQLite per lo sviluppo, SQL Server per la produzione</span><span class="sxs-lookup"><span data-stu-id="454fa-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="454fa-411">Quando si seleziona SQLite, il codice generato dal modello è pronto per lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="454fa-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="454fa-412">Il codice seguente illustra come inserire all' <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> avvio.</span><span class="sxs-lookup"><span data-stu-id="454fa-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="454fa-413">`IWebHostEnvironment` viene inserito in modo `ConfigureServices` che possa usare SQLite per lo sviluppo e la SQL Server nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="454fa-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="454fa-414">File creati</span><span class="sxs-lookup"><span data-stu-id="454fa-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="454fa-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="454fa-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="454fa-416">Il processo di scaffolding crea e aggiorna i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="454fa-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="454fa-417">*Pagine/film* : Create , Delete , Details, Edit e Index .</span><span class="sxs-lookup"><span data-stu-id="454fa-417">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="454fa-418">*Data/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="454fa-418">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="454fa-419">Aggiornato</span><span class="sxs-lookup"><span data-stu-id="454fa-419">Updated</span></span>

* <span data-ttu-id="454fa-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="454fa-420">*Startup.cs*</span></span>

<span data-ttu-id="454fa-421">I file creati e aggiornati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="454fa-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="454fa-422">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="454fa-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="454fa-423">Il processo di scaffolding crea e aggiorna i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="454fa-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="454fa-424">*Pagine/film* : Create , Delete , Details, Edit e Index .</span><span class="sxs-lookup"><span data-stu-id="454fa-424">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="454fa-425">*Data/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="454fa-425">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="454fa-426">Aggiornato</span><span class="sxs-lookup"><span data-stu-id="454fa-426">Updated</span></span>

* <span data-ttu-id="454fa-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="454fa-427">*Startup.cs*</span></span>

<span data-ttu-id="454fa-428">I file creati e aggiornati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="454fa-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="454fa-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="454fa-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="454fa-430">Il processo di scaffolding crea i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="454fa-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="454fa-431">*Pagine/film* : Create , Delete , Details, Edit e Index .</span><span class="sxs-lookup"><span data-stu-id="454fa-431">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="454fa-432">I file creati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="454fa-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="454fa-433">Migrazione iniziale</span><span class="sxs-lookup"><span data-stu-id="454fa-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="454fa-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="454fa-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="454fa-435">In questa sezione viene usata la Console di Gestione pacchetti (PMC) per:</span><span class="sxs-lookup"><span data-stu-id="454fa-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="454fa-436">Aggiungere una migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="454fa-436">Add an initial migration.</span></span>
* <span data-ttu-id="454fa-437">Aggiornare il database con la migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="454fa-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="454fa-438">Nel menu **Strumenti** selezionare **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="454fa-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu della Console di Gestione pacchetti](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="454fa-440">Nella Console di Gestione pacchetti immettere i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="454fa-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="454fa-441">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="454fa-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="454fa-442">Eseguire i seguenti comandi dell'interfaccia della riga di comando di .NET Core:</span><span class="sxs-lookup"><span data-stu-id="454fa-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="454fa-443">I comandi precedenti generano l'avviso seguente: "nessun tipo specificato per la colonna decimale ' Price ' nel tipo di entità' Movie '.</span><span class="sxs-lookup"><span data-stu-id="454fa-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="454fa-444">This will cause values to be silently truncated if they do not fit in the default precision and scale. (I valori saranno quindi automaticamente troncati se non rispettano la precisione e la scala predefinite).</span><span class="sxs-lookup"><span data-stu-id="454fa-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="454fa-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Specificare in modo esplicito il tipo di colonna di SQL Server che può supportare tutti i valori usando 'HasColumnType()')"</span><span class="sxs-lookup"><span data-stu-id="454fa-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="454fa-446">Ignorare l'avviso poiché verrà risolto in un passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="454fa-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="454fa-447">Il comando Migrations genera il codice per creare lo schema del database iniziale.</span><span class="sxs-lookup"><span data-stu-id="454fa-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="454fa-448">Lo schema è basato sul modello specificato in `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="454fa-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="454fa-449">L'argomento `InitialCreate` viene usato per denominare le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="454fa-449">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="454fa-450">È possibile usare qualsiasi nome, ma per convenzione viene selezionato un nome che descrive la migrazione.</span><span class="sxs-lookup"><span data-stu-id="454fa-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="454fa-451">Il `update` comando esegue il `Up` Metodo nelle migrazioni che non sono state applicate.</span><span class="sxs-lookup"><span data-stu-id="454fa-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="454fa-452">In tal caso, `update` esegue il `Up` metodo nel file  *migrations/ \<time-stamp> _Initial Create . cs* , che crea il database.</span><span class="sxs-lookup"><span data-stu-id="454fa-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="454fa-453">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="454fa-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="454fa-454">Esaminare il contesto registrato con l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="454fa-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="454fa-455">ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="454fa-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="454fa-456">I servizi, ad esempio il contesto del contesto del database EF Core, vengono registrati con l'inserimento delle dipendenze durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="454fa-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="454fa-457">I componenti che richiedono questi servizi, ad esempio Razor le pagine, vengono forniti tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="454fa-457">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="454fa-458">Il codice del costruttore che ottiene un'istanza del contesto del contesto del database viene illustrato più avanti nell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="454fa-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="454fa-459">Lo strumento di ponteggi crea automaticamente un contesto del contesto del database e lo registra con il contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="454fa-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="454fa-460">Esaminare il metodo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="454fa-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="454fa-461">La riga evidenziata è stata aggiunta dallo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="454fa-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="454fa-462">Le `RazorPagesMovieContext` coordinate EF Core funzionalità, ad esempio Create , Read, Update e Delete , per il `Movie` modello.</span><span class="sxs-lookup"><span data-stu-id="454fa-462">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="454fa-463">Il contesto dei dati (`RazorPagesMovieContext`) è derivato da [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="454fa-463">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="454fa-464">Il contesto dei dati specifica le entità incluse nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="454fa-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="454fa-465">Il codice precedente crea una [proprietà \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="454fa-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="454fa-466">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="454fa-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="454fa-467">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="454fa-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="454fa-468">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="454fa-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="454fa-469">Per lo sviluppo locale, il [sistema di configurazione](xref:fundamentals/configuration/index) legge la stringa di connessione dal *appsettings.json* file.</span><span class="sxs-lookup"><span data-stu-id="454fa-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="454fa-470">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="454fa-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="454fa-471">Esaminare il metodo `Up`.</span><span class="sxs-lookup"><span data-stu-id="454fa-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="454fa-472">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="454fa-472">Test the app</span></span>

* <span data-ttu-id="454fa-473">Eseguire l'app e accodare `/Movies` all'URL nel browser (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="454fa-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="454fa-474">Se viene visualizzato l'errore:</span><span class="sxs-lookup"><span data-stu-id="454fa-474">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="454fa-475">Non è stato eseguita la [migrazione](#pmc).</span><span class="sxs-lookup"><span data-stu-id="454fa-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="454fa-476">Testare il **Create** collegamento.</span><span class="sxs-lookup"><span data-stu-id="454fa-476">Test the **Create** link.</span></span>

  ![::: NO-LOC (Create)::: page](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="454fa-478">Potrebbe non essere possibile immettere virgole decimali nel campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="454fa-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="454fa-479">Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese (Stati Uniti), è necessario localizzare l'app.</span><span class="sxs-lookup"><span data-stu-id="454fa-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="454fa-480">Per istruzioni sulla globalizzazione, vedere [questo problema su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="454fa-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="454fa-481">Testare la **modifica** , **i dettagli** e i **Delete** collegamenti.</span><span class="sxs-lookup"><span data-stu-id="454fa-481">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="454fa-482">L'esercitazione successiva illustra i file creati tramite scaffolding.</span><span class="sxs-lookup"><span data-stu-id="454fa-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="454fa-483">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="454fa-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="454fa-484">[Precedente: introduzione](xref:tutorials/razor-pages/razor-pages-start) 
>  Passaggio [successivo: impalcatura Razor Pagine](xref:tutorials/razor-pages/page) di</span><span class="sxs-lookup"><span data-stu-id="454fa-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="454fa-485">In questa sezione vengono aggiunte le classi per la gestione dei film in un [database SQLite](https://www.sqlite.org/index.html)multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="454fa-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="454fa-486">Le app create da un modello di ASP.NET Core usano un database SQLite.</span><span class="sxs-lookup"><span data-stu-id="454fa-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="454fa-487">Le classi modello dell'app vengono usate con [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core provider di database](/ef/core/providers/sqlite)) per lavorare con il database.</span><span class="sxs-lookup"><span data-stu-id="454fa-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="454fa-488">EF Core è un framework ORM (Object-Relational Mapping) che semplifica l'accesso ai dati.</span><span class="sxs-lookup"><span data-stu-id="454fa-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="454fa-489">Le classi di modello sono dette classi POCO (da "plain-old CLR objects") perché non hanno alcuna dipendenza in EF Core.</span><span class="sxs-lookup"><span data-stu-id="454fa-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="454fa-490">Definiscono le proprietà dei dati archiviati nel database.</span><span class="sxs-lookup"><span data-stu-id="454fa-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="454fa-491">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="454fa-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="454fa-492">Aggiungere un modello di dati</span><span class="sxs-lookup"><span data-stu-id="454fa-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="454fa-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="454fa-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="454fa-494">Fare clic con il pulsante destro del mouse sul progetto **Razor PagesMovie** > **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="454fa-494">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="454fa-495">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="454fa-495">Name the folder *Models*.</span></span>

<span data-ttu-id="454fa-496">Fare clic con il pulsante destro del mouse sulla cartella *modelli* .</span><span class="sxs-lookup"><span data-stu-id="454fa-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="454fa-497">Selezionare **Aggiungi**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="454fa-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="454fa-498">Denominare la classe **Movie**.</span><span class="sxs-lookup"><span data-stu-id="454fa-498">Name the class **Movie**.</span></span>

<span data-ttu-id="454fa-499">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="454fa-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="454fa-500">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="454fa-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="454fa-501">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="454fa-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="454fa-502">`[DataType(DataType.Date)]`: L'attributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="454fa-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="454fa-503">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="454fa-503">With this attribute:</span></span>

  * <span data-ttu-id="454fa-504">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="454fa-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="454fa-505">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="454fa-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="454fa-506">L'attributo [DataAnnotations](xref:System.ComponentModel.DataAnnotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="454fa-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="454fa-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="454fa-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="454fa-508">Aggiungere una cartella denominata *Modelli*.</span><span class="sxs-lookup"><span data-stu-id="454fa-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="454fa-509">Aggiungere una classe alla cartella *Modello* denominata *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="454fa-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="454fa-510">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="454fa-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="454fa-511">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="454fa-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="454fa-512">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="454fa-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="454fa-513">`[DataType(DataType.Date)]`: L'attributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="454fa-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="454fa-514">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="454fa-514">With this attribute:</span></span>

  * <span data-ttu-id="454fa-515">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="454fa-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="454fa-516">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="454fa-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="454fa-517">L'attributo [DataAnnotations](xref:System.ComponentModel.DataAnnotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="454fa-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="454fa-518">Aggiungere i pacchetti NuGet e gli strumenti EF</span><span class="sxs-lookup"><span data-stu-id="454fa-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="454fa-519">Aggiungere una classe di contesto dei dati</span><span class="sxs-lookup"><span data-stu-id="454fa-519">Add a database context class</span></span>

<span data-ttu-id="454fa-520">Nel Razor progetto PagesMovie creare una nuova cartella denominata *Data*.</span><span class="sxs-lookup"><span data-stu-id="454fa-520">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="454fa-521">Aggiungere la classe `RazorPagesMovieContext` seguente alla cartella *Data* :</span><span class="sxs-lookup"><span data-stu-id="454fa-521">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="454fa-522">Il codice precedente crea una proprietà `DbSet` per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="454fa-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="454fa-523">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database e un'entità corrisponde a una riga della tabella.</span><span class="sxs-lookup"><span data-stu-id="454fa-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="454fa-524">Il codice non verrà compilato fino a quando le dipendenze non verranno aggiunte in un passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="454fa-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="454fa-525">Aggiungere una stringa di connessione del database</span><span class="sxs-lookup"><span data-stu-id="454fa-525">Add a database connection string</span></span>

<span data-ttu-id="454fa-526">Aggiungere una stringa di connessione al *appsettings.json* file come illustrato nel codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="454fa-526">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="454fa-527">Aggiungere i pacchetti NuGet necessari</span><span class="sxs-lookup"><span data-stu-id="454fa-527">Add required NuGet packages</span></span>

<span data-ttu-id="454fa-528">Eseguire il comando interfaccia della riga di comando di .NET Core seguente per aggiungere SQLite e CodeGeneration. Design al progetto:</span><span class="sxs-lookup"><span data-stu-id="454fa-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="454fa-529">Il pacchetto `Microsoft.VisualStudio.Web.CodeGeneration.Design` è necessario per lo scaffolding.</span><span class="sxs-lookup"><span data-stu-id="454fa-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="454fa-530">Registrare il contesto del database</span><span class="sxs-lookup"><span data-stu-id="454fa-530">Register the database context</span></span>

<span data-ttu-id="454fa-531">Aggiungere le istruzioni `using` seguenti all'inizio di *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="454fa-531">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="454fa-532">Registrare il contesto del database con il contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="454fa-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="454fa-533">Compilare il progetto per il controllo di eventuali errori.</span><span class="sxs-lookup"><span data-stu-id="454fa-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="454fa-534">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="454fa-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="454fa-535">Nella **finestra degli strumenti della soluzione** , fare clic sul progetto *Razor PagesMovie* e quindi selezionare **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="454fa-535">In **Solution Tool Window** , control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="454fa-536">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="454fa-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="454fa-537">CTRL: fare clic sulla cartella *modelli* , quindi selezionare **Aggiungi** > **nuovo file**.</span><span class="sxs-lookup"><span data-stu-id="454fa-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="454fa-538">Nel finestra di dialogo **Nuovo file** :</span><span class="sxs-lookup"><span data-stu-id="454fa-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="454fa-539">Selezionare **Generale** nel riquadro a sinistra.</span><span class="sxs-lookup"><span data-stu-id="454fa-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="454fa-540">Selezionare **Classe vuota** nel riquadro centrale.</span><span class="sxs-lookup"><span data-stu-id="454fa-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="454fa-541">Denominare la classe **Filmato** e selezionare **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="454fa-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="454fa-542">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="454fa-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="454fa-543">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="454fa-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="454fa-544">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="454fa-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="454fa-545">`[DataType(DataType.Date)]`: L'attributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="454fa-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="454fa-546">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="454fa-546">With this attribute:</span></span>

  * <span data-ttu-id="454fa-547">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="454fa-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="454fa-548">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="454fa-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="454fa-549">L'attributo [DataAnnotations](xref:System.ComponentModel.DataAnnotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="454fa-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="454fa-550">Compilare il progetto per verificare che non siano presenti errori di compilazione.</span><span class="sxs-lookup"><span data-stu-id="454fa-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="454fa-551">Eseguire lo scaffolding del modello di filmato</span><span class="sxs-lookup"><span data-stu-id="454fa-551">Scaffold the movie model</span></span>

<span data-ttu-id="454fa-552">In questa sezione viene eseguito lo scaffolding del modello *Movie*.</span><span class="sxs-lookup"><span data-stu-id="454fa-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="454fa-553">Ovvero, lo strumento di impalcatura produce pagine per Create le operazioni di lettura, aggiornamento e Delete (CRUD) per il modello di film.</span><span class="sxs-lookup"><span data-stu-id="454fa-553">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="454fa-554">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="454fa-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="454fa-555">Create una cartella *pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="454fa-555">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="454fa-556">Fare clic con il pulsante destro del mouse sulla cartella *pagine* > **Aggiungi** > **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="454fa-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="454fa-557">Denominare la cartella *Movies*.</span><span class="sxs-lookup"><span data-stu-id="454fa-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="454fa-558">Fare clic con il pulsante destro del mouse sulla cartella *pages/Movies* > **Aggiungi** > **nuovo elemento con impalcatura**.</span><span class="sxs-lookup"><span data-stu-id="454fa-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/sca.png)

<span data-ttu-id="454fa-560">Nella finestra di dialogo **Aggiungi impalcatura** selezionare **Razor pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="454fa-560">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffold.png)

<span data-ttu-id="454fa-562">Completare la finestra di dialogo **Aggiungi Razor pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="454fa-562">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="454fa-563">Nell'elenco a discesa **classe modello** selezionare **Movie ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="454fa-563">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="454fa-564">Nella riga della **classe del contesto dati** selezionare il **+** segno (più) e accettare il nome generato **Razor PagesMovie. Models. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="454fa-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="454fa-565">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="454fa-565">Select **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/arp.png)

<span data-ttu-id="454fa-567">Il *appsettings.json* file viene aggiornato con la stringa di connessione utilizzata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="454fa-567">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="454fa-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="454fa-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="454fa-569">Aprire una finestra di comando nella directory del progetto che contiene i file *Program.cs* , *Startup.cs* e *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="454fa-569">Open a command window in the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="454fa-570">**Per Windows** : eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="454fa-570">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="454fa-571">**Per MacOS e Linux** : eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="454fa-571">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="454fa-572">La tabella seguente illustra in dettaglio le opzioni del generatore di codice ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="454fa-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="454fa-573">Opzione</span><span class="sxs-lookup"><span data-stu-id="454fa-573">Option</span></span>               | <span data-ttu-id="454fa-574">Descrizione</span><span class="sxs-lookup"><span data-stu-id="454fa-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="454fa-575">Nome del modello.</span><span class="sxs-lookup"><span data-stu-id="454fa-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="454fa-576">Classe `DbContext` da usare.</span><span class="sxs-lookup"><span data-stu-id="454fa-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="454fa-577">Uso del layout predefinito.</span><span class="sxs-lookup"><span data-stu-id="454fa-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="454fa-578">Percorso relativo della cartella di output per creare le viste.</span><span class="sxs-lookup"><span data-stu-id="454fa-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="454fa-579">Aggiunge `_ValidationScriptsPartial` a Edit e Create pages</span><span class="sxs-lookup"><span data-stu-id="454fa-579">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="454fa-580">Usare l' `-h` opzione per ottenere informazioni sul `aspnet-codegenerator razorpage` comando:</span><span class="sxs-lookup"><span data-stu-id="454fa-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="454fa-581">Per ulteriori informazioni, vedere [DotNet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="454fa-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="454fa-582">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="454fa-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="454fa-583">Create una cartella *pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="454fa-583">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="454fa-584">CTRL: fare clic sulla cartella *pagine* > **Aggiungi** > **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="454fa-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="454fa-585">Denominare la cartella *Movies*.</span><span class="sxs-lookup"><span data-stu-id="454fa-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="454fa-586">Control: fare clic sulla cartella *pages/Movies* > **aggiungere** un > **nuovo elemento con impalcatura**.</span><span class="sxs-lookup"><span data-stu-id="454fa-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/scaMac.png)

<span data-ttu-id="454fa-588">Nella finestra di dialogo **Aggiungi nuova impalcatura** selezionare **Razor pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="454fa-588">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="454fa-590">Completare la finestra di dialogo **Aggiungi Razor pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="454fa-590">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="454fa-591">Nell'elenco a discesa **classe modello** selezionare o digitare **Movie**.</span><span class="sxs-lookup"><span data-stu-id="454fa-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="454fa-592">Nella riga della **classe del contesto dati** Digitare select the **Razor PagesMovieContext** . verrà creata una nuova classe del contesto di database con lo spazio dei nomi corretto.</span><span class="sxs-lookup"><span data-stu-id="454fa-592">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="454fa-593">In questo caso sarà **Razor PagesMovie. Models. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="454fa-593">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="454fa-594">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="454fa-594">Select **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/arpMac.png)

<span data-ttu-id="454fa-596">Il *appsettings.json* file viene aggiornato con la stringa di connessione utilizzata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="454fa-596">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="454fa-597">Il processo di scaffolding crea e aggiorna i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="454fa-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="454fa-598">File creati</span><span class="sxs-lookup"><span data-stu-id="454fa-598">Files created</span></span>

* <span data-ttu-id="454fa-599">*Pagine/film* : Create , Delete , Details, Edit e Index .</span><span class="sxs-lookup"><span data-stu-id="454fa-599">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="454fa-600">*Data/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="454fa-600">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="454fa-601">File aggiornato</span><span class="sxs-lookup"><span data-stu-id="454fa-601">File updated</span></span>

* <span data-ttu-id="454fa-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="454fa-602">*Startup.cs*</span></span>

<span data-ttu-id="454fa-603">I file creati e aggiornati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="454fa-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="454fa-604">Migrazione iniziale</span><span class="sxs-lookup"><span data-stu-id="454fa-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="454fa-605">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="454fa-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="454fa-606">In questa sezione viene usata la Console di Gestione pacchetti (PMC) per:</span><span class="sxs-lookup"><span data-stu-id="454fa-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="454fa-607">Aggiungere una migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="454fa-607">Add an initial migration.</span></span>
* <span data-ttu-id="454fa-608">Aggiornare il database con la migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="454fa-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="454fa-609">Nel menu **Strumenti** selezionare **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="454fa-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu della Console di Gestione pacchetti](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="454fa-611">Nella Console di Gestione pacchetti immettere i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="454fa-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="454fa-612">Il comando `Add-Migration` genera un codice per creare lo schema del database iniziale.</span><span class="sxs-lookup"><span data-stu-id="454fa-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="454fa-613">Lo schema è basato sul modello specificato in `DbContext` , nel file *Razor PagesMovieContext.cs* .</span><span class="sxs-lookup"><span data-stu-id="454fa-613">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="454fa-614">L' `InitialCreate` argomento viene usato per assegnare un nome alla migrazione.</span><span class="sxs-lookup"><span data-stu-id="454fa-614">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="454fa-615">È possibile usare qualsiasi nome, ma per convenzione viene usato un nome che descrive la migrazione.</span><span class="sxs-lookup"><span data-stu-id="454fa-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="454fa-616">Per altre informazioni, vedere <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="454fa-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="454fa-617">Il `Update-Database` comando esegue il `Up` metodo nel file *migrations/ \<time-stamp> _Initial Create . cs* .</span><span class="sxs-lookup"><span data-stu-id="454fa-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="454fa-618">Il metodo `Up` crea il database.</span><span class="sxs-lookup"><span data-stu-id="454fa-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="454fa-619">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="454fa-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="454fa-620">Eseguire i seguenti comandi dell'interfaccia della riga di comando di .NET Core:</span><span class="sxs-lookup"><span data-stu-id="454fa-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="454fa-621">I comandi precedenti generano l'avviso seguente: "nessun tipo specificato per la colonna decimale ' Price ' nel tipo di entità' Movie '.</span><span class="sxs-lookup"><span data-stu-id="454fa-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="454fa-622">This will cause values to be silently truncated if they do not fit in the default precision and scale. (I valori saranno quindi automaticamente troncati se non rispettano la precisione e la scala predefinite).</span><span class="sxs-lookup"><span data-stu-id="454fa-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="454fa-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Specificare in modo esplicito il tipo di colonna di SQL Server che può supportare tutti i valori usando 'HasColumnType()')"</span><span class="sxs-lookup"><span data-stu-id="454fa-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="454fa-624">Ignorare l'avviso poiché verrà risolto in un passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="454fa-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="454fa-625">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="454fa-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="454fa-626">Esaminare il contesto registrato con l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="454fa-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="454fa-627">ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="454fa-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="454fa-628">I servizi (ad esempio il contesto del contesto del database EF Core) vengono registrati con l'inserimento delle dipendenze durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="454fa-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="454fa-629">I componenti che richiedono questi servizi, ad esempio Razor le pagine, vengono forniti tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="454fa-629">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="454fa-630">Il codice del costruttore che ottiene un'istanza del contesto contextB del contesto di database viene illustrato più avanti nell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="454fa-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="454fa-631">Lo strumento di ponteggi crea automaticamente un contesto del contesto del database e lo registra con il contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="454fa-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="454fa-632">Esaminare il metodo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="454fa-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="454fa-633">La riga evidenziata è stata aggiunta dallo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="454fa-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="454fa-634">Le `RazorPagesMovieContext` coordinate EF Core funzionalità, ad esempio Create , Read, Update e Delete , per il `Movie` modello.</span><span class="sxs-lookup"><span data-stu-id="454fa-634">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="454fa-635">Il contesto dei dati (`RazorPagesMovieContext`) è derivato da [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="454fa-635">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="454fa-636">Il contesto dei dati specifica le entità incluse nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="454fa-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="454fa-637">Il codice precedente crea una [proprietà \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="454fa-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="454fa-638">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="454fa-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="454fa-639">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="454fa-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="454fa-640">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="454fa-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="454fa-641">Per lo sviluppo locale, il [sistema di configurazione](xref:fundamentals/configuration/index) legge la stringa di connessione dal *appsettings.json* file.</span><span class="sxs-lookup"><span data-stu-id="454fa-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="454fa-642">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="454fa-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="454fa-643">Esaminare il metodo `Up`.</span><span class="sxs-lookup"><span data-stu-id="454fa-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="454fa-644">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="454fa-644">Test the app</span></span>

* <span data-ttu-id="454fa-645">Eseguire l'app e accodare `/Movies` all'URL nel browser (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="454fa-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="454fa-646">Se viene visualizzato l'errore:</span><span class="sxs-lookup"><span data-stu-id="454fa-646">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="454fa-647">Non è stato eseguita la [migrazione](#pmc).</span><span class="sxs-lookup"><span data-stu-id="454fa-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="454fa-648">Testare il **Create** collegamento.</span><span class="sxs-lookup"><span data-stu-id="454fa-648">Test the **Create** link.</span></span>

  ![::: NO-LOC (Create)::: page](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="454fa-650">Potrebbe non essere possibile immettere virgole decimali nel campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="454fa-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="454fa-651">Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese (Stati Uniti), è necessario localizzare l'app.</span><span class="sxs-lookup"><span data-stu-id="454fa-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="454fa-652">Per istruzioni sulla globalizzazione, vedere [questo problema su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="454fa-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="454fa-653">Testare la **modifica** , **i dettagli** e i **Delete** collegamenti.</span><span class="sxs-lookup"><span data-stu-id="454fa-653">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="454fa-654">L'esercitazione successiva illustra i file creati tramite scaffolding.</span><span class="sxs-lookup"><span data-stu-id="454fa-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="454fa-655">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="454fa-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="454fa-656">[Precedente: introduzione](xref:tutorials/razor-pages/razor-pages-start) 
>  Passaggio [successivo: impalcatura Razor Pagine](xref:tutorials/razor-pages/page) di</span><span class="sxs-lookup"><span data-stu-id="454fa-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
