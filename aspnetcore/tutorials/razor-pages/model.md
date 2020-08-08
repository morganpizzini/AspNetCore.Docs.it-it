---
title: Parte 2, aggiungere un modello a un' Razor app di pagine in ASP.NET Core
author: rick-anderson
description: Parte 2 della serie di esercitazioni sulle Razor pagine.
ms.author: riande
ms.date: 12/05/2019
no-loc:
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
ms.openlocfilehash: 6b50f46863a6dabb01bcf0976a42abb504e6f7b7
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020457"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="dcca8-103">Parte 2, aggiungere un modello a un' Razor app di pagine in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dcca8-103">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="dcca8-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="dcca8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="dcca8-105">In questa sezione vengono aggiunte le classi per la gestione dei filmati.</span><span class="sxs-lookup"><span data-stu-id="dcca8-105">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="dcca8-106">Le classi modello dell'app usano [Entity Framework Core (EF Core)](/ef/core) per lavorare con il database.</span><span class="sxs-lookup"><span data-stu-id="dcca8-106">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="dcca8-107">EF Core è un mapper relazionale a oggetti (O/RM) che semplifica l'accesso ai dati.</span><span class="sxs-lookup"><span data-stu-id="dcca8-107">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="dcca8-108">Le classi di modello sono dette classi POCO (da "plain-old CLR objects") perché non hanno alcuna dipendenza in EF Core.</span><span class="sxs-lookup"><span data-stu-id="dcca8-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="dcca8-109">Definiscono le proprietà dei dati archiviati nel database.</span><span class="sxs-lookup"><span data-stu-id="dcca8-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="dcca8-110">Aggiungere un modello di dati</span><span class="sxs-lookup"><span data-stu-id="dcca8-110">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dcca8-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dcca8-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dcca8-112">Fare clic con il pulsante destro del mouse sul progetto \*\* Razor PagesMovie\*\* > **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="dcca8-113">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="dcca8-113">Name the folder *Models*.</span></span>

<span data-ttu-id="dcca8-114">Fare clic con il pulsante destro del mouse sulla cartella *Models*.</span><span class="sxs-lookup"><span data-stu-id="dcca8-114">Right click the *Models* folder.</span></span> <span data-ttu-id="dcca8-115">Selezionare **Aggiungi**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="dcca8-116">Denominare la classe **Movie**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="dcca8-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dcca8-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="dcca8-118">Aggiungere una cartella denominata *Modelli*.</span><span class="sxs-lookup"><span data-stu-id="dcca8-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="dcca8-119">Aggiungere una classe alla cartella *Modello* denominata *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="dcca8-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dcca8-120">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="dcca8-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="dcca8-121">In riquadro della soluzione fare clic con il pulsante destro del mouse sul progetto \*\* Razor PagesMovie\*\* , quindi scegliere **Aggiungi** > **nuova cartella.** Assegnare un nome ai *modelli*di cartella.</span><span class="sxs-lookup"><span data-stu-id="dcca8-121">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="dcca8-122">Fare clic con il pulsante destro del mouse sulla cartella *Models* , quindi scegliere **Aggiungi** > **nuovo file**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-122">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="dcca8-123">Nel finestra di dialogo **Nuovo file**:</span><span class="sxs-lookup"><span data-stu-id="dcca8-123">In the **New File** dialog:</span></span>

  * <span data-ttu-id="dcca8-124">Selezionare **Generale** nel riquadro a sinistra.</span><span class="sxs-lookup"><span data-stu-id="dcca8-124">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="dcca8-125">Selezionare **Classe vuota** nel riquadro centrale.</span><span class="sxs-lookup"><span data-stu-id="dcca8-125">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="dcca8-126">Denominare la classe **Filmato** e selezionare **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-126">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="dcca8-127">Compilare il progetto per verificare che non siano presenti errori di compilazione.</span><span class="sxs-lookup"><span data-stu-id="dcca8-127">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="dcca8-128">Eseguire lo scaffolding del modello di filmato</span><span class="sxs-lookup"><span data-stu-id="dcca8-128">Scaffold the movie model</span></span>

<span data-ttu-id="dcca8-129">In questa sezione viene eseguito lo scaffolding del modello *Movie*.</span><span class="sxs-lookup"><span data-stu-id="dcca8-129">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="dcca8-130">Lo strumento di scaffolding crea quindi le pagine per le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) per il modello *Movie*.</span><span class="sxs-lookup"><span data-stu-id="dcca8-130">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dcca8-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dcca8-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dcca8-132">Creare una cartella *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="dcca8-132">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="dcca8-133">Fare clic con il pulsante destro del mouse sulla cartella *Pages* > **Aggiungi** > **Nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-133">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="dcca8-134">Assegnare il nome *Movies* alla cartella</span><span class="sxs-lookup"><span data-stu-id="dcca8-134">Name the folder *Movies*</span></span>

<span data-ttu-id="dcca8-135">Fare clic con il pulsante destro del mouse sulla cartella *Pages/Movies* > **Aggiungi** > **Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-135">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/sca.png)

<span data-ttu-id="dcca8-137">Nella finestra di dialogo **Aggiungi impalcatura** selezionare \*\* Razor pages using Entity Framework (CRUD)\*\* > **Add**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-137">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffold.png)

<span data-ttu-id="dcca8-139">Completare la finestra di dialogo **Aggiungi Razor pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="dcca8-139">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="dcca8-140">Nell'elenco a discesa **classe modello** selezionare **Movie ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-140">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="dcca8-141">Nella riga della **classe del contesto dati** selezionare il **+** segno (segno più) e modificare il nome generato da Razor PagesMovie.\*\* Modelli**. Razor Da PagesMovieContext a Razor PagesMovie.** Dati\*\*. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="dcca8-141">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="dcca8-142">[Questa modifica](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) non è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="dcca8-142">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="dcca8-143">Crea la classe del contesto di database con lo spazio dei nomi corretto.</span><span class="sxs-lookup"><span data-stu-id="dcca8-143">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="dcca8-144">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-144">Select **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/3/arp.png)

<span data-ttu-id="dcca8-146">Il file *appsettings.json* è stato aggiornato con la stringa di connessione usata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="dcca8-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dcca8-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dcca8-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="dcca8-148">Aprire una finestra di comando nella directory del progetto (la directory che contiene i file *Program.cs*, *Startup.cs* e *csproj*).</span><span class="sxs-lookup"><span data-stu-id="dcca8-148">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="dcca8-149">Installare lo strumento di scaffolding:</span><span class="sxs-lookup"><span data-stu-id="dcca8-149">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="dcca8-150">**Per Windows**: eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="dcca8-150">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="dcca8-151">**Per MacOS e Linux**: eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="dcca8-151">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dcca8-152">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="dcca8-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="dcca8-153">Creare una cartella *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="dcca8-153">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="dcca8-154">Fare clic con il pulsante destro del mouse sulla cartella *Pages* > **Aggiungi** > **Nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-154">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="dcca8-155">Assegnare il nome *Movies* alla cartella</span><span class="sxs-lookup"><span data-stu-id="dcca8-155">Name the folder *Movies*</span></span>

<span data-ttu-id="dcca8-156">Fare clic con il pulsante destro del mouse sulla cartella *pages/Movies* > **Aggiungi** > **Nuova impalcatura...**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-156">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/scaMac.png)

<span data-ttu-id="dcca8-158">Nella finestra di dialogo **Nuova impalcatura** selezionare \*\* Razor pagine con Entity Framework (CRUD)\*\* > **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-158">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="dcca8-160">Completare la finestra di dialogo **Aggiungi Razor pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="dcca8-160">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="dcca8-161">Nell'elenco a discesa **classe modello** selezionare o digitare **Movie ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-161">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="dcca8-162">Nella riga della **classe del contesto dati** Digitare il nome della nuova classe, Razor PagesMovie.\*\* Dati\*\*. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="dcca8-162">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="dcca8-163">[Questa modifica](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) non è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="dcca8-163">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="dcca8-164">Crea la classe del contesto di database con lo spazio dei nomi corretto.</span><span class="sxs-lookup"><span data-stu-id="dcca8-164">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="dcca8-165">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-165">Select **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/arpMac.png)

<span data-ttu-id="dcca8-167">Il file *appsettings.json* è stato aggiornato con la stringa di connessione usata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="dcca8-167">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="dcca8-168">Aggiungi strumenti EF</span><span class="sxs-lookup"><span data-stu-id="dcca8-168">Add EF tools</span></span>

<span data-ttu-id="dcca8-169">Eseguire il comando interfaccia della riga di comando di .NET Core seguente:</span><span class="sxs-lookup"><span data-stu-id="dcca8-169">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="dcca8-170">Il comando precedente aggiunge gli strumenti Entity Framework Core per l'interfaccia della riga di comando di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="dcca8-170">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="dcca8-171">File creati</span><span class="sxs-lookup"><span data-stu-id="dcca8-171">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dcca8-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dcca8-172">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dcca8-173">Il processo di scaffolding crea e aggiorna i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="dcca8-173">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="dcca8-174">*Pages/Movies*: pagine Create (Crea), Delete (Elimina), Details (Dettagli), Edit (Modifica) e Index (Indice).</span><span class="sxs-lookup"><span data-stu-id="dcca8-174">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="dcca8-175">*Data/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="dcca8-175">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="dcca8-176">Updated</span><span class="sxs-lookup"><span data-stu-id="dcca8-176">Updated</span></span>

* <span data-ttu-id="dcca8-177">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="dcca8-177">*Startup.cs*</span></span>

<span data-ttu-id="dcca8-178">I file creati e aggiornati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="dcca8-178">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dcca8-179">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="dcca8-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="dcca8-180">Il processo di scaffolding crea e aggiorna i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="dcca8-180">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="dcca8-181">*Pages/Movies*: pagine Create (Crea), Delete (Elimina), Details (Dettagli), Edit (Modifica) e Index (Indice).</span><span class="sxs-lookup"><span data-stu-id="dcca8-181">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="dcca8-182">*Data/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="dcca8-182">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="dcca8-183">Updated</span><span class="sxs-lookup"><span data-stu-id="dcca8-183">Updated</span></span>

* <span data-ttu-id="dcca8-184">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="dcca8-184">*Startup.cs*</span></span>

<span data-ttu-id="dcca8-185">I file creati e aggiornati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="dcca8-185">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dcca8-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dcca8-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dcca8-187">Il processo di scaffolding crea i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="dcca8-187">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="dcca8-188">*Pages/Movies*: pagine Create (Crea), Delete (Elimina), Details (Dettagli), Edit (Modifica) e Index (Indice).</span><span class="sxs-lookup"><span data-stu-id="dcca8-188">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="dcca8-189">I file creati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="dcca8-189">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="dcca8-190">Migrazione iniziale</span><span class="sxs-lookup"><span data-stu-id="dcca8-190">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dcca8-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dcca8-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dcca8-192">In questa sezione viene usata la Console di Gestione pacchetti (PMC) per:</span><span class="sxs-lookup"><span data-stu-id="dcca8-192">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="dcca8-193">Aggiungere una migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="dcca8-193">Add an initial migration.</span></span>
* <span data-ttu-id="dcca8-194">Aggiornare il database con la migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="dcca8-194">Update the database with the initial migration.</span></span>

<span data-ttu-id="dcca8-195">Nel menu **Strumenti** selezionare **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-195">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu della Console di Gestione pacchetti](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="dcca8-197">Nella Console di Gestione pacchetti immettere i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="dcca8-197">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="dcca8-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dcca8-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dcca8-199">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="dcca8-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="dcca8-200">I comandi precedenti generano l'avviso seguente: "nessun tipo specificato per la colonna decimale ' Price ' nel tipo di entità' Movie '.</span><span class="sxs-lookup"><span data-stu-id="dcca8-200">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="dcca8-201">This will cause values to be silently truncated if they do not fit in the default precision and scale. (I valori saranno quindi automaticamente troncati se non rispettano la precisione e la scala predefinite).</span><span class="sxs-lookup"><span data-stu-id="dcca8-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="dcca8-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Specificare in modo esplicito il tipo di colonna di SQL Server che può supportare tutti i valori usando 'HasColumnType()')"</span><span class="sxs-lookup"><span data-stu-id="dcca8-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="dcca8-203">È possibile ignorare tale avviso. Verrà risolto in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="dcca8-203">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="dcca8-204">Il comando Migrations genera il codice per creare lo schema del database iniziale.</span><span class="sxs-lookup"><span data-stu-id="dcca8-204">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="dcca8-205">Lo schema è basato sul modello specificato in `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="dcca8-205">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="dcca8-206">L'argomento `InitialCreate` viene usato per denominare le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="dcca8-206">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="dcca8-207">È possibile usare qualsiasi nome, ma per convenzione viene selezionato un nome che descrive la migrazione.</span><span class="sxs-lookup"><span data-stu-id="dcca8-207">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="dcca8-208">Il `update` comando esegue il `Up` Metodo nelle migrazioni che non sono state applicate.</span><span class="sxs-lookup"><span data-stu-id="dcca8-208">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="dcca8-209">In tal caso, `update` esegue il `Up` metodo nel file *migrations/ \<time-stamp> _InitialCreate. cs* , che crea il database.</span><span class="sxs-lookup"><span data-stu-id="dcca8-209">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dcca8-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dcca8-210">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="dcca8-211">Esaminare il contesto registrato con l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="dcca8-211">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="dcca8-212">ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="dcca8-212">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="dcca8-213">I servizi, ad esempio il contesto di database di Entity Framework Core, vengono registrati tramite dependency injection durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="dcca8-213">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="dcca8-214">I componenti che richiedono questi servizi, ad esempio Razor le pagine, vengono forniti tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="dcca8-214">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="dcca8-215">Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="dcca8-215">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="dcca8-216">Lo strumento di scaffolding ha creato automaticamente un contesto del database e lo ha registrato per la dependency injection.</span><span class="sxs-lookup"><span data-stu-id="dcca8-216">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="dcca8-217">Esaminare il metodo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="dcca8-217">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="dcca8-218">La riga evidenziata è stata aggiunta dallo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="dcca8-218">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="dcca8-219">`RazorPagesMovieContext` coordina la funzionalità di EF Core (Create, Read, Update, Delete e così via) per il modello `Movie`.</span><span class="sxs-lookup"><span data-stu-id="dcca8-219">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="dcca8-220">Il contesto dei dati (`RazorPagesMovieContext`) è derivato da [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="dcca8-220">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="dcca8-221">Il contesto dei dati specifica le entità incluse nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="dcca8-221">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="dcca8-222">Il codice precedente crea una [proprietà \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="dcca8-222">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="dcca8-223">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="dcca8-223">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="dcca8-224">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="dcca8-224">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="dcca8-225">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="dcca8-225">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="dcca8-226">Per lo sviluppo locale, il [sistema di configurazione di ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="dcca8-226">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dcca8-227">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dcca8-227">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dcca8-228">Esaminare il metodo `Up`.</span><span class="sxs-lookup"><span data-stu-id="dcca8-228">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dcca8-229">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="dcca8-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="dcca8-230">Esaminare il metodo `Up`.</span><span class="sxs-lookup"><span data-stu-id="dcca8-230">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="dcca8-231">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="dcca8-231">Test the app</span></span>

* <span data-ttu-id="dcca8-232">Eseguire l'app e accodare `/Movies` all'URL nel browser (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="dcca8-232">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="dcca8-233">Se viene visualizzato l'errore:</span><span class="sxs-lookup"><span data-stu-id="dcca8-233">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="dcca8-234">Non è stato eseguita la [migrazione](#pmc).</span><span class="sxs-lookup"><span data-stu-id="dcca8-234">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="dcca8-235">Eseguire il test del collegamento **Crea**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-235">Test the **Create** link.</span></span>

  ![Pagina di creazione](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="dcca8-237">Potrebbe non essere possibile immettere virgole decimali nel campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="dcca8-237">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="dcca8-238">Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese (Stati Uniti), è necessario localizzare l'app.</span><span class="sxs-lookup"><span data-stu-id="dcca8-238">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="dcca8-239">Per istruzioni sulla globalizzazione, vedere [questo problema su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="dcca8-239">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="dcca8-240">Testare i collegamenti **Modifica**, **Dettagli** ed **Elimina**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-240">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="dcca8-241">L'esercitazione successiva illustra i file creati tramite scaffolding.</span><span class="sxs-lookup"><span data-stu-id="dcca8-241">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dcca8-242">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="dcca8-242">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="dcca8-243">[Precedente: introduzione](xref:tutorials/razor-pages/razor-pages-start) 
>  Passaggio [successivo: impalcatura Razor Pagine](xref:tutorials/razor-pages/page) di</span><span class="sxs-lookup"><span data-stu-id="dcca8-243">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dcca8-244">In questa sezione vengono aggiunte le classi per la gestione dei film in un [database SQLite](https://www.sqlite.org/index.html)multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="dcca8-244">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="dcca8-245">Le app create da un modello di ASP.NET Core usano un database SQLite.</span><span class="sxs-lookup"><span data-stu-id="dcca8-245">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="dcca8-246">Le classi modello dell'app vengono usate con [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core provider di database](/ef/core/providers/sqlite)) per lavorare con il database.</span><span class="sxs-lookup"><span data-stu-id="dcca8-246">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="dcca8-247">EF Core è un framework ORM (Object-Relational Mapping) che semplifica l'accesso ai dati.</span><span class="sxs-lookup"><span data-stu-id="dcca8-247">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="dcca8-248">Le classi di modello sono dette classi POCO (da "plain-old CLR objects") perché non hanno alcuna dipendenza in EF Core.</span><span class="sxs-lookup"><span data-stu-id="dcca8-248">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="dcca8-249">Definiscono le proprietà dei dati archiviati nel database.</span><span class="sxs-lookup"><span data-stu-id="dcca8-249">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="dcca8-250">Aggiungere un modello di dati</span><span class="sxs-lookup"><span data-stu-id="dcca8-250">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dcca8-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dcca8-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dcca8-252">Fare clic con il pulsante destro del mouse sul progetto \*\* Razor PagesMovie\*\* > **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-252">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="dcca8-253">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="dcca8-253">Name the folder *Models*.</span></span>

<span data-ttu-id="dcca8-254">Fare clic con il pulsante destro del mouse sulla cartella *Models*.</span><span class="sxs-lookup"><span data-stu-id="dcca8-254">Right click the *Models* folder.</span></span> <span data-ttu-id="dcca8-255">Selezionare **Aggiungi**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-255">Select **Add** > **Class**.</span></span> <span data-ttu-id="dcca8-256">Denominare la classe **Movie**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-256">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="dcca8-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dcca8-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="dcca8-258">Aggiungere una cartella denominata *Modelli*.</span><span class="sxs-lookup"><span data-stu-id="dcca8-258">Add a folder named *Models*.</span></span>
* <span data-ttu-id="dcca8-259">Aggiungere una classe alla cartella *Modello* denominata *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="dcca8-259">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dcca8-260">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="dcca8-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="dcca8-261">In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto \*\* Razor PagesMovie\*\* , quindi scegliere **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-261">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="dcca8-262">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="dcca8-262">Name the folder *Models*.</span></span>
* <span data-ttu-id="dcca8-263">Fare clic con il pulsante destro del mouse sulla cartella *Models* e scegliere **Aggiungi** > **Nuovo file**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-263">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="dcca8-264">Nel finestra di dialogo **Nuovo file**:</span><span class="sxs-lookup"><span data-stu-id="dcca8-264">In the **New File** dialog:</span></span>

  * <span data-ttu-id="dcca8-265">Selezionare **Generale** nel riquadro a sinistra.</span><span class="sxs-lookup"><span data-stu-id="dcca8-265">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="dcca8-266">Selezionare **Classe vuota** nel riquadro centrale.</span><span class="sxs-lookup"><span data-stu-id="dcca8-266">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="dcca8-267">Denominare la classe **Filmato** e selezionare **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-267">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="dcca8-268">Compilare il progetto per verificare che non siano presenti errori di compilazione.</span><span class="sxs-lookup"><span data-stu-id="dcca8-268">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="dcca8-269">Eseguire lo scaffolding del modello di filmato</span><span class="sxs-lookup"><span data-stu-id="dcca8-269">Scaffold the movie model</span></span>

<span data-ttu-id="dcca8-270">In questa sezione viene eseguito lo scaffolding del modello *Movie*.</span><span class="sxs-lookup"><span data-stu-id="dcca8-270">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="dcca8-271">Lo strumento di scaffolding crea quindi le pagine per le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) per il modello *Movie*.</span><span class="sxs-lookup"><span data-stu-id="dcca8-271">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dcca8-272">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dcca8-272">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dcca8-273">Creare una cartella *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="dcca8-273">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="dcca8-274">Fare clic con il pulsante destro del mouse sulla cartella *Pages* > **Aggiungi** > **Nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-274">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="dcca8-275">Assegnare il nome *Movies* alla cartella</span><span class="sxs-lookup"><span data-stu-id="dcca8-275">Name the folder *Movies*</span></span>

<span data-ttu-id="dcca8-276">Fare clic con il pulsante destro del mouse sulla cartella *Pages/Movies* > **Aggiungi** > **Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-276">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/sca.png)

<span data-ttu-id="dcca8-278">Nella finestra di dialogo **Aggiungi impalcatura** selezionare \*\* Razor pages using Entity Framework (CRUD)\*\* > **Add**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-278">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffold.png)

<span data-ttu-id="dcca8-280">Completare la finestra di dialogo **Aggiungi Razor pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="dcca8-280">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="dcca8-281">Nell'elenco a discesa **classe modello** selezionare **Movie ( Razor PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-281">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="dcca8-282">Nella riga della **classe del contesto dati** selezionare il **+** segno (più) e accettare il nome generato \*\* Razor PagesMovie. Models. Razor PagesMovieContext\*\*.</span><span class="sxs-lookup"><span data-stu-id="dcca8-282">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="dcca8-283">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-283">Select **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/arp.png)

<span data-ttu-id="dcca8-285">Il file *appsettings.json* è stato aggiornato con la stringa di connessione usata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="dcca8-285">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dcca8-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dcca8-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="dcca8-287">Aprire una finestra di comando nella directory del progetto (la directory che contiene i file *Program.cs*, *Startup.cs* e *csproj*).</span><span class="sxs-lookup"><span data-stu-id="dcca8-287">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="dcca8-288">**Per Windows**: eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="dcca8-288">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="dcca8-289">**Per MacOS e Linux**: eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="dcca8-289">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dcca8-290">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="dcca8-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="dcca8-291">Creare una cartella *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="dcca8-291">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="dcca8-292">Fare clic con il pulsante destro del mouse sulla cartella *Pages* > **Aggiungi** > **Nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-292">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="dcca8-293">Assegnare il nome *Movies* alla cartella</span><span class="sxs-lookup"><span data-stu-id="dcca8-293">Name the folder *Movies*</span></span>

<span data-ttu-id="dcca8-294">Fare clic con il pulsante destro del mouse sulla cartella *Pages/Movies* > **Aggiungi** > **Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-294">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/scaMac.png)

<span data-ttu-id="dcca8-296">Nella finestra di dialogo **Aggiungi nuova impalcatura** selezionare \*\* Razor pages using Entity Framework (CRUD)\*\* > **Add**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-296">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="dcca8-298">Completare la finestra di dialogo **Aggiungi Razor pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="dcca8-298">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="dcca8-299">Nell'elenco a discesa **classe modello** selezionare o digitare **Movie**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-299">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="dcca8-300">Nella riga della **classe del contesto dati** Digitare select the \*\* Razor PagesMovieContext\*\* . verrà creata una nuova classe del contesto DB con lo spazio dei nomi corretto.</span><span class="sxs-lookup"><span data-stu-id="dcca8-300">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="dcca8-301">In questo caso sarà \*\* Razor PagesMovie. Models. Razor PagesMovieContext\*\*.</span><span class="sxs-lookup"><span data-stu-id="dcca8-301">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="dcca8-302">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-302">Select **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/arpMac.png)

<span data-ttu-id="dcca8-304">Il file *appsettings.json* è stato aggiornato con la stringa di connessione usata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="dcca8-304">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="dcca8-305">Il processo di scaffolding crea e aggiorna i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="dcca8-305">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="dcca8-306">File creati</span><span class="sxs-lookup"><span data-stu-id="dcca8-306">Files created</span></span>

* <span data-ttu-id="dcca8-307">*Pages/Movies*: pagine Create (Crea), Delete (Elimina), Details (Dettagli), Edit (Modifica) e Index (Indice).</span><span class="sxs-lookup"><span data-stu-id="dcca8-307">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="dcca8-308">*Data/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="dcca8-308">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="dcca8-309">File aggiornato</span><span class="sxs-lookup"><span data-stu-id="dcca8-309">File updated</span></span>

* <span data-ttu-id="dcca8-310">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="dcca8-310">*Startup.cs*</span></span>

<span data-ttu-id="dcca8-311">I file creati e aggiornati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="dcca8-311">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="dcca8-312">Migrazione iniziale</span><span class="sxs-lookup"><span data-stu-id="dcca8-312">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dcca8-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dcca8-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dcca8-314">In questa sezione viene usata la Console di Gestione pacchetti (PMC) per:</span><span class="sxs-lookup"><span data-stu-id="dcca8-314">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="dcca8-315">Aggiungere una migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="dcca8-315">Add an initial migration.</span></span>
* <span data-ttu-id="dcca8-316">Aggiornare il database con la migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="dcca8-316">Update the database with the initial migration.</span></span>

<span data-ttu-id="dcca8-317">Nel menu **Strumenti** selezionare **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-317">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu della Console di Gestione pacchetti](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="dcca8-319">Nella Console di Gestione pacchetti immettere i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="dcca8-319">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="dcca8-320">Il comando `Add-Migration` genera un codice per creare lo schema del database iniziale.</span><span class="sxs-lookup"><span data-stu-id="dcca8-320">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="dcca8-321">Lo schema è basato sul modello specificato in `DbContext` (nel file \* Razor PagesMovieContext.cs\* ).</span><span class="sxs-lookup"><span data-stu-id="dcca8-321">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="dcca8-322">L' `InitialCreate` argomento viene usato per assegnare un nome alla migrazione.</span><span class="sxs-lookup"><span data-stu-id="dcca8-322">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="dcca8-323">È possibile usare qualsiasi nome, ma per convenzione viene usato un nome che descrive la migrazione.</span><span class="sxs-lookup"><span data-stu-id="dcca8-323">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="dcca8-324">Per altre informazioni, vedere <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="dcca8-324">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="dcca8-325">Il `Update-Database` comando esegue il `Up` metodo nel file *migrations/ \<time-stamp> _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="dcca8-325">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="dcca8-326">Il metodo `Up` crea il database.</span><span class="sxs-lookup"><span data-stu-id="dcca8-326">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dcca8-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dcca8-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dcca8-328">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="dcca8-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="dcca8-329">I comandi precedenti generano l'avviso seguente: "*nessun tipo specificato per la colonna decimale ' Price ' nel tipo di entità' Movie '. In questo modo i valori verranno troncati automaticamente se non rientrano nella precisione e nella scala predefinite. Specificare in modo esplicito il tipo di colonna di SQL Server in grado di contenere tutti i valori utilizzando ' HasColumnType ()'.* È possibile ignorare l'avviso, che verrà risolto in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="dcca8-329">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dcca8-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dcca8-330">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="dcca8-331">Esaminare il contesto registrato con l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="dcca8-331">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="dcca8-332">ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="dcca8-332">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="dcca8-333">I servizi, ad esempio il contesto di database di Entity Framework Core, vengono registrati tramite dependency injection durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="dcca8-333">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="dcca8-334">I componenti che richiedono questi servizi, ad esempio Razor le pagine, vengono forniti tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="dcca8-334">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="dcca8-335">Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="dcca8-335">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="dcca8-336">Lo strumento di scaffolding ha creato automaticamente un contesto del database e lo ha registrato per la dependency injection.</span><span class="sxs-lookup"><span data-stu-id="dcca8-336">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="dcca8-337">Esaminare il metodo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="dcca8-337">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="dcca8-338">La riga evidenziata è stata aggiunta dallo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="dcca8-338">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="dcca8-339">`RazorPagesMovieContext` coordina la funzionalità di EF Core (Create, Read, Update, Delete e così via) per il modello `Movie`.</span><span class="sxs-lookup"><span data-stu-id="dcca8-339">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="dcca8-340">Il contesto dei dati (`RazorPagesMovieContext`) è derivato da [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="dcca8-340">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="dcca8-341">Il contesto dei dati specifica le entità incluse nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="dcca8-341">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="dcca8-342">Il codice precedente crea una [proprietà \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="dcca8-342">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="dcca8-343">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="dcca8-343">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="dcca8-344">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="dcca8-344">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="dcca8-345">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="dcca8-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="dcca8-346">Per lo sviluppo locale, il [sistema di configurazione di ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="dcca8-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dcca8-347">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dcca8-347">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dcca8-348">Esaminare il metodo `Up`.</span><span class="sxs-lookup"><span data-stu-id="dcca8-348">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dcca8-349">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="dcca8-349">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="dcca8-350">Esaminare il metodo `Up`.</span><span class="sxs-lookup"><span data-stu-id="dcca8-350">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="dcca8-351">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="dcca8-351">Test the app</span></span>

* <span data-ttu-id="dcca8-352">Eseguire l'app e accodare `/Movies` all'URL nel browser (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="dcca8-352">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="dcca8-353">Se viene visualizzato l'errore:</span><span class="sxs-lookup"><span data-stu-id="dcca8-353">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="dcca8-354">Non è stato eseguita la [migrazione](#pmc).</span><span class="sxs-lookup"><span data-stu-id="dcca8-354">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="dcca8-355">Eseguire il test del collegamento **Crea**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-355">Test the **Create** link.</span></span>

  ![Pagina di creazione](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="dcca8-357">Potrebbe non essere possibile immettere virgole decimali nel campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="dcca8-357">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="dcca8-358">Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese (Stati Uniti), è necessario localizzare l'app.</span><span class="sxs-lookup"><span data-stu-id="dcca8-358">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="dcca8-359">Per istruzioni sulla globalizzazione, vedere [questo problema su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="dcca8-359">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="dcca8-360">Testare i collegamenti **Modifica**, **Dettagli** ed **Elimina**.</span><span class="sxs-lookup"><span data-stu-id="dcca8-360">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="dcca8-361">L'esercitazione successiva illustra i file creati tramite scaffolding.</span><span class="sxs-lookup"><span data-stu-id="dcca8-361">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dcca8-362">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="dcca8-362">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="dcca8-363">[Precedente: introduzione](xref:tutorials/razor-pages/razor-pages-start) 
>  Passaggio [successivo: impalcatura Razor Pagine](xref:tutorials/razor-pages/page) di</span><span class="sxs-lookup"><span data-stu-id="dcca8-363">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
