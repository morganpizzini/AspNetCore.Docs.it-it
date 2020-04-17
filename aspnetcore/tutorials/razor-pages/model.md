---
title: Aggiungere un modello a un'app Razor Pages in ASP.NET Core
author: rick-anderson
description: Scoprire come aggiungere classi per la gestione dei film in un database tramite Entity Framework Core (EF Core).
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: 7f7c2a09b74e6007ee3ea9c038398bac54988186
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488871"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="eaa54-103">Aggiungere un modello a un'app Razor Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eaa54-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="eaa54-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="eaa54-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="eaa54-105">In questa sezione vengono aggiunte classi per la gestione dei filmati.</span><span class="sxs-lookup"><span data-stu-id="eaa54-105">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="eaa54-106">Le classi del modello dell'app usano [Entity Framework Core (EF Core)](/ef/core) per lavorare con il database.</span><span class="sxs-lookup"><span data-stu-id="eaa54-106">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="eaa54-107">EF Core è un mapper relazionale a oggetti (O/RM) che semplifica l'accesso ai dati.</span><span class="sxs-lookup"><span data-stu-id="eaa54-107">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="eaa54-108">Le classi di modello sono dette classi POCO (da "plain-old CLR objects") perché non hanno alcuna dipendenza in EF Core.</span><span class="sxs-lookup"><span data-stu-id="eaa54-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="eaa54-109">Definiscono le proprietà dei dati archiviati nel database.</span><span class="sxs-lookup"><span data-stu-id="eaa54-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="eaa54-110">Aggiungere un modello di dati</span><span class="sxs-lookup"><span data-stu-id="eaa54-110">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eaa54-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eaa54-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eaa54-112">Fare clic con il pulsante destro del mouse sul progetto **RazorPagesMovie** > **Aggiungi** > **Nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="eaa54-113">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="eaa54-113">Name the folder *Models*.</span></span>

<span data-ttu-id="eaa54-114">Fare clic con il pulsante destro del mouse sulla cartella *Models*.</span><span class="sxs-lookup"><span data-stu-id="eaa54-114">Right click the *Models* folder.</span></span> <span data-ttu-id="eaa54-115">Selezionare **Aggiungi** > **classe**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="eaa54-116">Denominare la classe **Movie**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="eaa54-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eaa54-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="eaa54-118">Aggiungere una cartella denominata *Modelli*.</span><span class="sxs-lookup"><span data-stu-id="eaa54-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="eaa54-119">Aggiungere una classe alla cartella *Modello* denominata *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="eaa54-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eaa54-120">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="eaa54-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="eaa54-121">In Riquadro della soluzione fare clic con il pulsante destro del mouse sul progetto **RazorPagesMovie** e quindi **scegliere Aggiungi** > **nuova cartella...**. Assegnare alla cartella il nome *Modelli*.</span><span class="sxs-lookup"><span data-stu-id="eaa54-121">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="eaa54-122">Fare clic con il pulsante destro del mouse sulla cartella *Modelli,* quindi scegliere **Aggiungi** > **nuovo file...**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-122">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="eaa54-123">Nel finestra di dialogo **Nuovo file**:</span><span class="sxs-lookup"><span data-stu-id="eaa54-123">In the **New File** dialog:</span></span>

  * <span data-ttu-id="eaa54-124">Selezionare **Generale** nel riquadro a sinistra.</span><span class="sxs-lookup"><span data-stu-id="eaa54-124">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="eaa54-125">Selezionare **Classe vuota** nel riquadro centrale.</span><span class="sxs-lookup"><span data-stu-id="eaa54-125">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="eaa54-126">Denominare la classe **Filmato** e selezionare **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-126">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="eaa54-127">Compilare il progetto per verificare che non siano presenti errori di compilazione.</span><span class="sxs-lookup"><span data-stu-id="eaa54-127">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="eaa54-128">Eseguire lo scaffolding del modello di filmato</span><span class="sxs-lookup"><span data-stu-id="eaa54-128">Scaffold the movie model</span></span>

<span data-ttu-id="eaa54-129">In questa sezione viene eseguito lo scaffolding del modello *Movie*.</span><span class="sxs-lookup"><span data-stu-id="eaa54-129">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="eaa54-130">Lo strumento di scaffolding crea quindi le pagine per le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) per il modello *Movie*.</span><span class="sxs-lookup"><span data-stu-id="eaa54-130">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eaa54-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eaa54-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eaa54-132">Creare una cartella *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="eaa54-132">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="eaa54-133">Fare clic con il pulsante destro del mouse sulla cartella *Pages* > **Aggiungi** > **Nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-133">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="eaa54-134">Assegnare il nome *Movies* alla cartella</span><span class="sxs-lookup"><span data-stu-id="eaa54-134">Name the folder *Movies*</span></span>

<span data-ttu-id="eaa54-135">Fare clic con il pulsante destro del mouse sulla cartella *Pages/Movies* > **Aggiungi** > **Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-135">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/sca.png)

<span data-ttu-id="eaa54-137">Nella finestra di dialogo **Aggiungi scaffolding** selezionare **Razor che usano Entity Framework (CRUD)** > **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-137">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffold.png)

<span data-ttu-id="eaa54-139">Completare la finestra di dialogo **Pagine Razor che usano Entity Framework (CRUD)**:</span><span class="sxs-lookup"><span data-stu-id="eaa54-139">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="eaa54-140">Nel menu a discesa **Classe modello** selezionare **Movie (RazorPagesMovie.Models)**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-140">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="eaa54-141">Nella riga **Classe contesto di dati** selezionare il segno più **+** e modificare il nome generato da RazorPagesMovie.**Models**.RazorPagesMovieContext a RazorPagesMovie.**Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="eaa54-141">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="eaa54-142">[Questa modifica](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) non è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="eaa54-142">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="eaa54-143">Crea la classe del contesto di database con lo spazio dei nomi corretto.</span><span class="sxs-lookup"><span data-stu-id="eaa54-143">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="eaa54-144">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-144">Select **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/3/arp.png)

<span data-ttu-id="eaa54-146">Il file *appsettings.json* è stato aggiornato con la stringa di connessione usata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="eaa54-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eaa54-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eaa54-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="eaa54-148">Aprire una finestra di comando nella directory del progetto (la directory che contiene i file *Program.cs*, *Startup.cs* e *csproj*).</span><span class="sxs-lookup"><span data-stu-id="eaa54-148">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="eaa54-149">Installare lo strumento di scaffolding:</span><span class="sxs-lookup"><span data-stu-id="eaa54-149">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="eaa54-150">**Per Windows**: Eseguire il seguente comando:</span><span class="sxs-lookup"><span data-stu-id="eaa54-150">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="eaa54-151">**Per MacOS e Linux**: eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="eaa54-151">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eaa54-152">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="eaa54-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="eaa54-153">Creare una cartella *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="eaa54-153">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="eaa54-154">Fare clic con il pulsante destro del mouse sulla cartella *Pages* > **Aggiungi** > **Nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-154">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="eaa54-155">Assegnare il nome *Movies* alla cartella</span><span class="sxs-lookup"><span data-stu-id="eaa54-155">Name the folder *Movies*</span></span>

<span data-ttu-id="eaa54-156">Fare clic con il pulsante destro del mouse sulla cartella *Pagine/film* > **Aggiungi** > **nuovo scaffolding...**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-156">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/scaMac.png)

<span data-ttu-id="eaa54-158">Nella finestra di dialogo **Nuovo scaffolding** selezionare **Pagine Razor utilizzando Entity Framework (CRUD)** > **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-158">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="eaa54-160">Completare la finestra di dialogo **Pagine Razor che usano Entity Framework (CRUD)**:</span><span class="sxs-lookup"><span data-stu-id="eaa54-160">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="eaa54-161">Nell'elenco a discesa **Classe modello** selezionare o digitare **Film (RazorPagesMovie.Models)**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-161">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="eaa54-162">Nella riga **Della classe contesto dati** digitare il nome della nuova classe RazorPagesMovie.In the Data context class row, type the name for the new class, RazorPagesMovie. **Dati**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="eaa54-162">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="eaa54-163">[Questa modifica](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) non è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="eaa54-163">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="eaa54-164">Crea la classe del contesto di database con lo spazio dei nomi corretto.</span><span class="sxs-lookup"><span data-stu-id="eaa54-164">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="eaa54-165">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-165">Select **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/arpMac.png)

<span data-ttu-id="eaa54-167">Il file *appsettings.json* è stato aggiornato con la stringa di connessione usata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="eaa54-167">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="eaa54-168">Aggiungere strumenti di EF</span><span class="sxs-lookup"><span data-stu-id="eaa54-168">Add EF tools</span></span>

<span data-ttu-id="eaa54-169">Eseguire il comando CLI .NET Core seguente:</span><span class="sxs-lookup"><span data-stu-id="eaa54-169">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="eaa54-170">Il comando precedente aggiunge gli strumenti di base di Entity Framework per l'interfaccia della riga di comando di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="eaa54-170">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="eaa54-171">File creati</span><span class="sxs-lookup"><span data-stu-id="eaa54-171">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eaa54-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eaa54-172">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eaa54-173">Il processo di scaffolding crea e aggiorna i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="eaa54-173">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="eaa54-174">*Pages/Movies*: pagine Create (Crea), Delete (Elimina), Details (Dettagli), Edit (Modifica) e Index (Indice).</span><span class="sxs-lookup"><span data-stu-id="eaa54-174">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="eaa54-175">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="eaa54-175">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="eaa54-176">Updated</span><span class="sxs-lookup"><span data-stu-id="eaa54-176">Updated</span></span>

* <span data-ttu-id="eaa54-177">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="eaa54-177">*Startup.cs*</span></span>

<span data-ttu-id="eaa54-178">I file creati e aggiornati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="eaa54-178">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eaa54-179">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="eaa54-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="eaa54-180">Il processo di scaffolding crea e aggiorna i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="eaa54-180">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="eaa54-181">*Pages/Movies*: pagine Create (Crea), Delete (Elimina), Details (Dettagli), Edit (Modifica) e Index (Indice).</span><span class="sxs-lookup"><span data-stu-id="eaa54-181">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="eaa54-182">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="eaa54-182">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="eaa54-183">Updated</span><span class="sxs-lookup"><span data-stu-id="eaa54-183">Updated</span></span>

* <span data-ttu-id="eaa54-184">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="eaa54-184">*Startup.cs*</span></span>

<span data-ttu-id="eaa54-185">I file creati e aggiornati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="eaa54-185">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eaa54-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eaa54-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="eaa54-187">Il processo di scaffolding crea i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="eaa54-187">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="eaa54-188">*Pages/Movies*: pagine Create (Crea), Delete (Elimina), Details (Dettagli), Edit (Modifica) e Index (Indice).</span><span class="sxs-lookup"><span data-stu-id="eaa54-188">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="eaa54-189">I file creati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="eaa54-189">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="eaa54-190">Migrazione iniziale</span><span class="sxs-lookup"><span data-stu-id="eaa54-190">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eaa54-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eaa54-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eaa54-192">In questa sezione viene usata la Console di Gestione pacchetti (PMC) per:</span><span class="sxs-lookup"><span data-stu-id="eaa54-192">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="eaa54-193">Aggiungere una migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="eaa54-193">Add an initial migration.</span></span>
* <span data-ttu-id="eaa54-194">Aggiornare il database con la migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="eaa54-194">Update the database with the initial migration.</span></span>

<span data-ttu-id="eaa54-195">Nel menu **Strumenti** selezionare **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-195">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu della Console di Gestione pacchetti](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="eaa54-197">Nella Console di Gestione pacchetti immettere i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="eaa54-197">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="eaa54-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eaa54-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eaa54-199">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="eaa54-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="eaa54-200">I comandi precedenti generano il seguente avviso: "Nessun tipo specificato per la colonna decimale 'Prezzo' nel tipo di entità 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="eaa54-200">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="eaa54-201">This will cause values to be silently truncated if they do not fit in the default precision and scale. (I valori saranno quindi automaticamente troncati se non rispettano la precisione e la scala predefinite).</span><span class="sxs-lookup"><span data-stu-id="eaa54-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="eaa54-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Specificare in modo esplicito il tipo di colonna di SQL Server che può supportare tutti i valori usando 'HasColumnType()')"</span><span class="sxs-lookup"><span data-stu-id="eaa54-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="eaa54-203">È possibile ignorare tale avviso. Verrà risolto in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="eaa54-203">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="eaa54-204">Il comando migrations genera il codice per creare lo schema iniziale del database.</span><span class="sxs-lookup"><span data-stu-id="eaa54-204">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="eaa54-205">Lo schema è basato `DbContext`sul modello specificato in .</span><span class="sxs-lookup"><span data-stu-id="eaa54-205">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="eaa54-206">L'argomento `InitialCreate` viene usato per denominare le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="eaa54-206">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="eaa54-207">È possibile usare qualsiasi nome, ma per convenzione viene selezionato un nome che descrive la migrazione.</span><span class="sxs-lookup"><span data-stu-id="eaa54-207">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="eaa54-208">Il `update` comando `Up` esegue il metodo nelle migrazioni che non sono state applicate.</span><span class="sxs-lookup"><span data-stu-id="eaa54-208">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="eaa54-209">In questo `update` caso, `Up` esegue il metodo nel file *Migrations/time-stamp\<>_InitialCreate.cs,* che crea il database.</span><span class="sxs-lookup"><span data-stu-id="eaa54-209">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eaa54-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eaa54-210">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="eaa54-211">Esaminare il contesto registrato con l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="eaa54-211">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="eaa54-212">ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="eaa54-212">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="eaa54-213">I servizi, ad esempio il contesto di database di Entity Framework Core, vengono registrati tramite dependency injection durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="eaa54-213">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="eaa54-214">Questi servizi vengono quindi offerti ai componenti per cui sono necessari (ad esempio Razor Pages) tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="eaa54-214">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="eaa54-215">Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="eaa54-215">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="eaa54-216">Lo strumento di scaffolding ha creato automaticamente un contesto del database e lo ha registrato per la dependency injection.</span><span class="sxs-lookup"><span data-stu-id="eaa54-216">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="eaa54-217">Esaminare il metodo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="eaa54-217">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="eaa54-218">La riga evidenziata è stata aggiunta dallo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="eaa54-218">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="eaa54-219">`RazorPagesMovieContext` coordina la funzionalità di EF Core (Create, Read, Update, Delete e così via) per il modello `Movie`.</span><span class="sxs-lookup"><span data-stu-id="eaa54-219">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="eaa54-220">Il contesto dei dati (`RazorPagesMovieContext`) è derivato da [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="eaa54-220">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="eaa54-221">Il contesto dei dati specifica le entità incluse nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="eaa54-221">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="eaa54-222">Il codice precedente crea una proprietà [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="eaa54-222">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="eaa54-223">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="eaa54-223">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="eaa54-224">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="eaa54-224">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="eaa54-225">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="eaa54-225">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="eaa54-226">Per lo sviluppo locale, il [sistema di configurazione di ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="eaa54-226">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eaa54-227">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eaa54-227">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="eaa54-228">Esaminare il metodo `Up`.</span><span class="sxs-lookup"><span data-stu-id="eaa54-228">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eaa54-229">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="eaa54-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="eaa54-230">Esaminare il metodo `Up`.</span><span class="sxs-lookup"><span data-stu-id="eaa54-230">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="eaa54-231">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="eaa54-231">Test the app</span></span>

* <span data-ttu-id="eaa54-232">Eseguire l'app e accodare `/Movies` all'URL nel browser (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="eaa54-232">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="eaa54-233">Se viene visualizzato l'errore:</span><span class="sxs-lookup"><span data-stu-id="eaa54-233">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="eaa54-234">Non è stato eseguita la [migrazione](#pmc).</span><span class="sxs-lookup"><span data-stu-id="eaa54-234">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="eaa54-235">Eseguire il test del collegamento **Crea**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-235">Test the **Create** link.</span></span>

  ![Pagina di creazione](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="eaa54-237">Potrebbe non essere possibile immettere virgole decimali nel campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="eaa54-237">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="eaa54-238">Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese (Stati Uniti), è necessario localizzare l'app.</span><span class="sxs-lookup"><span data-stu-id="eaa54-238">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="eaa54-239">Per istruzioni sulla globalizzazione, vedere [questo problema su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="eaa54-239">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="eaa54-240">Testare i collegamenti **Modifica**, **Dettagli** ed **Elimina**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-240">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="eaa54-241">L'esercitazione successiva illustra i file creati tramite scaffolding.</span><span class="sxs-lookup"><span data-stu-id="eaa54-241">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eaa54-242">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="eaa54-242">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="eaa54-243">[Precedente: Get Started](xref:tutorials/razor-pages/razor-pages-start)
> [Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="eaa54-243">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="eaa54-244">In questa sezione vengono aggiunte classi per la gestione di filmati in un [database SQLite](https://www.sqlite.org/index.html)multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="eaa54-244">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="eaa54-245">Le app create da un modello ASP.NET Core usano un database SQLite.Apps created from an ASP.NET Core template use a SQLite database.</span><span class="sxs-lookup"><span data-stu-id="eaa54-245">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="eaa54-246">Le classi del modello dell'app vengono usate con [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) per lavorare con il database.</span><span class="sxs-lookup"><span data-stu-id="eaa54-246">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="eaa54-247">EF Core è un framework ORM (Object-Relational Mapping) che semplifica l'accesso ai dati.</span><span class="sxs-lookup"><span data-stu-id="eaa54-247">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="eaa54-248">Le classi di modello sono dette classi POCO (da "plain-old CLR objects") perché non hanno alcuna dipendenza in EF Core.</span><span class="sxs-lookup"><span data-stu-id="eaa54-248">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="eaa54-249">Definiscono le proprietà dei dati archiviati nel database.</span><span class="sxs-lookup"><span data-stu-id="eaa54-249">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="eaa54-250">Aggiungere un modello di dati</span><span class="sxs-lookup"><span data-stu-id="eaa54-250">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eaa54-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eaa54-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eaa54-252">Fare clic con il pulsante destro del mouse sul progetto **RazorPagesMovie** > **Aggiungi** > **Nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-252">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="eaa54-253">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="eaa54-253">Name the folder *Models*.</span></span>

<span data-ttu-id="eaa54-254">Fare clic con il pulsante destro del mouse sulla cartella *Models*.</span><span class="sxs-lookup"><span data-stu-id="eaa54-254">Right click the *Models* folder.</span></span> <span data-ttu-id="eaa54-255">Selezionare **Aggiungi** > **classe**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-255">Select **Add** > **Class**.</span></span> <span data-ttu-id="eaa54-256">Denominare la classe **Movie**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-256">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="eaa54-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eaa54-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="eaa54-258">Aggiungere una cartella denominata *Modelli*.</span><span class="sxs-lookup"><span data-stu-id="eaa54-258">Add a folder named *Models*.</span></span>
* <span data-ttu-id="eaa54-259">Aggiungere una classe alla cartella *Modello* denominata *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="eaa54-259">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eaa54-260">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="eaa54-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="eaa54-261">In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **RazorPagesMovie**, quindi selezionare **Aggiungi** > **Nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-261">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="eaa54-262">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="eaa54-262">Name the folder *Models*.</span></span>
* <span data-ttu-id="eaa54-263">Fare clic con il pulsante destro del mouse sulla cartella *Models* e scegliere **Aggiungi** > **Nuovo file**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-263">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="eaa54-264">Nel finestra di dialogo **Nuovo file**:</span><span class="sxs-lookup"><span data-stu-id="eaa54-264">In the **New File** dialog:</span></span>

  * <span data-ttu-id="eaa54-265">Selezionare **Generale** nel riquadro a sinistra.</span><span class="sxs-lookup"><span data-stu-id="eaa54-265">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="eaa54-266">Selezionare **Classe vuota** nel riquadro centrale.</span><span class="sxs-lookup"><span data-stu-id="eaa54-266">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="eaa54-267">Denominare la classe **Filmato** e selezionare **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-267">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="eaa54-268">Compilare il progetto per verificare che non siano presenti errori di compilazione.</span><span class="sxs-lookup"><span data-stu-id="eaa54-268">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="eaa54-269">Eseguire lo scaffolding del modello di filmato</span><span class="sxs-lookup"><span data-stu-id="eaa54-269">Scaffold the movie model</span></span>

<span data-ttu-id="eaa54-270">In questa sezione viene eseguito lo scaffolding del modello *Movie*.</span><span class="sxs-lookup"><span data-stu-id="eaa54-270">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="eaa54-271">Lo strumento di scaffolding crea quindi le pagine per le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) per il modello *Movie*.</span><span class="sxs-lookup"><span data-stu-id="eaa54-271">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eaa54-272">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eaa54-272">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eaa54-273">Creare una cartella *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="eaa54-273">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="eaa54-274">Fare clic con il pulsante destro del mouse sulla cartella *Pages* > **Aggiungi** > **Nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-274">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="eaa54-275">Assegnare il nome *Movies* alla cartella</span><span class="sxs-lookup"><span data-stu-id="eaa54-275">Name the folder *Movies*</span></span>

<span data-ttu-id="eaa54-276">Fare clic con il pulsante destro del mouse sulla cartella *Pages/Movies* > **Aggiungi** > **Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-276">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/sca.png)

<span data-ttu-id="eaa54-278">Nella finestra di dialogo **Aggiungi scaffolding** selezionare **Razor che usano Entity Framework (CRUD)** > **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-278">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffold.png)

<span data-ttu-id="eaa54-280">Completare la finestra di dialogo **Pagine Razor che usano Entity Framework (CRUD)**:</span><span class="sxs-lookup"><span data-stu-id="eaa54-280">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="eaa54-281">Nel menu a discesa **Classe modello** selezionare **Movie (RazorPagesMovie.Models)**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-281">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="eaa54-282">Nella riga **Classe contesto di dati** selezionare il segno più **+** e accettare il nome generato **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-282">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="eaa54-283">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-283">Select **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/arp.png)

<span data-ttu-id="eaa54-285">Il file *appsettings.json* è stato aggiornato con la stringa di connessione usata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="eaa54-285">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eaa54-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eaa54-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="eaa54-287">Aprire una finestra di comando nella directory del progetto (la directory che contiene i file *Program.cs*, *Startup.cs* e *csproj*).</span><span class="sxs-lookup"><span data-stu-id="eaa54-287">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="eaa54-288">**Per Windows**: Eseguire il seguente comando:</span><span class="sxs-lookup"><span data-stu-id="eaa54-288">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="eaa54-289">**Per MacOS e Linux**: eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="eaa54-289">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eaa54-290">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="eaa54-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="eaa54-291">Creare una cartella *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="eaa54-291">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="eaa54-292">Fare clic con il pulsante destro del mouse sulla cartella *Pages* > **Aggiungi** > **Nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-292">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="eaa54-293">Assegnare il nome *Movies* alla cartella</span><span class="sxs-lookup"><span data-stu-id="eaa54-293">Name the folder *Movies*</span></span>

<span data-ttu-id="eaa54-294">Fare clic con il pulsante destro del mouse sulla cartella *Pages/Movies* > **Aggiungi** > **Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-294">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/scaMac.png)

<span data-ttu-id="eaa54-296">Nella finestra di dialogo **Aggiungi nuovo scaffolding** selezionare **Pagine Razor utilizzando Entity Framework (CRUD)** > **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-296">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="eaa54-298">Completare la finestra di dialogo **Pagine Razor che usano Entity Framework (CRUD)**:</span><span class="sxs-lookup"><span data-stu-id="eaa54-298">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="eaa54-299">Nell'elenco a discesa **Classe modello** selezionare o digitare **Film .**</span><span class="sxs-lookup"><span data-stu-id="eaa54-299">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="eaa54-300">Nella riga **Classe contesto dati** digitare selezionare **RazorPagesMovieContext** in modo da creare una nuova classe di contesto db con lo spazio dei nomi corretto.</span><span class="sxs-lookup"><span data-stu-id="eaa54-300">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="eaa54-301">In questo caso sarà **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-301">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="eaa54-302">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-302">Select **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/arpMac.png)

<span data-ttu-id="eaa54-304">Il file *appsettings.json* è stato aggiornato con la stringa di connessione usata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="eaa54-304">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="eaa54-305">Il processo di scaffolding crea e aggiorna i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="eaa54-305">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="eaa54-306">File creati</span><span class="sxs-lookup"><span data-stu-id="eaa54-306">Files created</span></span>

* <span data-ttu-id="eaa54-307">*Pages/Movies*: pagine Create (Crea), Delete (Elimina), Details (Dettagli), Edit (Modifica) e Index (Indice).</span><span class="sxs-lookup"><span data-stu-id="eaa54-307">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="eaa54-308">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="eaa54-308">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="eaa54-309">File aggiornato</span><span class="sxs-lookup"><span data-stu-id="eaa54-309">File updated</span></span>

* <span data-ttu-id="eaa54-310">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="eaa54-310">*Startup.cs*</span></span>

<span data-ttu-id="eaa54-311">I file creati e aggiornati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="eaa54-311">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="eaa54-312">Migrazione iniziale</span><span class="sxs-lookup"><span data-stu-id="eaa54-312">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eaa54-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eaa54-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eaa54-314">In questa sezione viene usata la Console di Gestione pacchetti (PMC) per:</span><span class="sxs-lookup"><span data-stu-id="eaa54-314">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="eaa54-315">Aggiungere una migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="eaa54-315">Add an initial migration.</span></span>
* <span data-ttu-id="eaa54-316">Aggiornare il database con la migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="eaa54-316">Update the database with the initial migration.</span></span>

<span data-ttu-id="eaa54-317">Nel menu **Strumenti** selezionare **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-317">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu della Console di Gestione pacchetti](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="eaa54-319">Nella Console di Gestione pacchetti immettere i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="eaa54-319">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="eaa54-320">Il comando `Add-Migration` genera un codice per creare lo schema del database iniziale.</span><span class="sxs-lookup"><span data-stu-id="eaa54-320">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="eaa54-321">Lo schema è basato sul modello specificato in `DbContext` (nel file *RazorPagesMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="eaa54-321">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="eaa54-322">L'argomento `InitialCreate` viene utilizzato per denominare la migrazione.</span><span class="sxs-lookup"><span data-stu-id="eaa54-322">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="eaa54-323">È possibile usare qualsiasi nome, ma per convenzione viene usato un nome che descrive la migrazione.</span><span class="sxs-lookup"><span data-stu-id="eaa54-323">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="eaa54-324">Per altre informazioni, vedere <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="eaa54-324">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="eaa54-325">Il comando `Update-Database` esegue il metodo `Up` nel file *Migrations/\<time-stamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="eaa54-325">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="eaa54-326">Il metodo `Up` crea il database.</span><span class="sxs-lookup"><span data-stu-id="eaa54-326">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eaa54-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eaa54-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eaa54-328">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="eaa54-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="eaa54-329">I comandi precedenti generano il seguente avviso:*"Nessun tipo specificato per la colonna decimale 'Prezzo' nel tipo di entità 'Film'. In questo modo i valori verranno troncati automaticamente se non rientrano nella precisione e nella scala predefinite. Specificare in modo esplicito il tipo di colonna di SQL Server che può contenere tutti i valori utilizzando 'HasColumnType()'.*" È possibile ignorare tale avviso, verrà risolto in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="eaa54-329">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eaa54-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eaa54-330">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="eaa54-331">Esaminare il contesto registrato con l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="eaa54-331">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="eaa54-332">ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="eaa54-332">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="eaa54-333">I servizi, ad esempio il contesto di database di Entity Framework Core, vengono registrati tramite dependency injection durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="eaa54-333">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="eaa54-334">Questi servizi vengono quindi offerti ai componenti per cui sono necessari (ad esempio Razor Pages) tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="eaa54-334">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="eaa54-335">Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="eaa54-335">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="eaa54-336">Lo strumento di scaffolding ha creato automaticamente un contesto del database e lo ha registrato per la dependency injection.</span><span class="sxs-lookup"><span data-stu-id="eaa54-336">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="eaa54-337">Esaminare il metodo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="eaa54-337">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="eaa54-338">La riga evidenziata è stata aggiunta dallo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="eaa54-338">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="eaa54-339">`RazorPagesMovieContext` coordina la funzionalità di EF Core (Create, Read, Update, Delete e così via) per il modello `Movie`.</span><span class="sxs-lookup"><span data-stu-id="eaa54-339">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="eaa54-340">Il contesto dei dati (`RazorPagesMovieContext`) è derivato da [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="eaa54-340">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="eaa54-341">Il contesto dei dati specifica le entità incluse nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="eaa54-341">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="eaa54-342">Il codice precedente crea una proprietà [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="eaa54-342">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="eaa54-343">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="eaa54-343">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="eaa54-344">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="eaa54-344">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="eaa54-345">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="eaa54-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="eaa54-346">Per lo sviluppo locale, il [sistema di configurazione di ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="eaa54-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eaa54-347">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eaa54-347">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="eaa54-348">Esaminare il metodo `Up`.</span><span class="sxs-lookup"><span data-stu-id="eaa54-348">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eaa54-349">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="eaa54-349">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="eaa54-350">Esaminare il metodo `Up`.</span><span class="sxs-lookup"><span data-stu-id="eaa54-350">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="eaa54-351">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="eaa54-351">Test the app</span></span>

* <span data-ttu-id="eaa54-352">Eseguire l'app e accodare `/Movies` all'URL nel browser (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="eaa54-352">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="eaa54-353">Se viene visualizzato l'errore:</span><span class="sxs-lookup"><span data-stu-id="eaa54-353">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="eaa54-354">Non è stato eseguita la [migrazione](#pmc).</span><span class="sxs-lookup"><span data-stu-id="eaa54-354">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="eaa54-355">Eseguire il test del collegamento **Crea**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-355">Test the **Create** link.</span></span>

  ![Pagina di creazione](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="eaa54-357">Potrebbe non essere possibile immettere virgole decimali nel campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="eaa54-357">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="eaa54-358">Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese (Stati Uniti), è necessario localizzare l'app.</span><span class="sxs-lookup"><span data-stu-id="eaa54-358">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="eaa54-359">Per istruzioni sulla globalizzazione, vedere [questo problema su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="eaa54-359">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="eaa54-360">Testare i collegamenti **Modifica**, **Dettagli** ed **Elimina**.</span><span class="sxs-lookup"><span data-stu-id="eaa54-360">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="eaa54-361">L'esercitazione successiva illustra i file creati tramite scaffolding.</span><span class="sxs-lookup"><span data-stu-id="eaa54-361">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eaa54-362">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="eaa54-362">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="eaa54-363">[Precedente: Get Started](xref:tutorials/razor-pages/razor-pages-start)
> [Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="eaa54-363">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
