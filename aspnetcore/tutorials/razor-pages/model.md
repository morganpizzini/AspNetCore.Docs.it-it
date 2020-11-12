---
title: Parte 2, aggiungere un modello
author: rick-anderson
description: 'Parte 2 della serie di esercitazioni sulle :::no-loc(Razor)::: pagine. In questa sezione vengono aggiunte le classi del modello.'
ms.author: riande
ms.date: 11/11/2020
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 6244ac8798fb470a88802389961968fb52bd3c0a
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550679"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="8be5e-104">Parte 2, aggiungere un modello a un' :::no-loc(Razor)::: app di pagine in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8be5e-104">Part 2, add a model to a :::no-loc(Razor)::: Pages app in ASP.NET Core</span></span>

<span data-ttu-id="8be5e-105">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8be5e-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="8be5e-106">In questa sezione si aggiungono alcune classi per la gestione di filmati in un database.</span><span class="sxs-lookup"><span data-stu-id="8be5e-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="8be5e-107">Le classi modello dell'app usano [Entity Framework Core (EF Core)](/ef/core) per lavorare con il database.</span><span class="sxs-lookup"><span data-stu-id="8be5e-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="8be5e-108">EF Core è un mapper relazionale a oggetti (O/RM) che semplifica l'accesso ai dati.</span><span class="sxs-lookup"><span data-stu-id="8be5e-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="8be5e-109">Prima di tutto si scrivono le classi del modello e EF Core crea il database.</span><span class="sxs-lookup"><span data-stu-id="8be5e-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="8be5e-110">Le classi del modello sono note come classi POCO (da " **P** Lain- **o** LD **C** LR **o** gli oggetti") perché non hanno una dipendenza da EF core.</span><span class="sxs-lookup"><span data-stu-id="8be5e-110">The model classes are known as POCO classes (from " **P** lain- **O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="8be5e-111">Definiscono le proprietà dei dati archiviati nel database.</span><span class="sxs-lookup"><span data-stu-id="8be5e-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="8be5e-112">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="8be5e-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="8be5e-113">Aggiungere un modello di dati</span><span class="sxs-lookup"><span data-stu-id="8be5e-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8be5e-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8be5e-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="8be5e-115">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto *:::no-loc(Razor)::: PagesMovie* > **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-115">In **Solution Explorer** , right-click the *:::no-loc(Razor):::PagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="8be5e-116">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="8be5e-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="8be5e-117">Fare clic con il pulsante destro del mouse sulla cartella *modelli* .</span><span class="sxs-lookup"><span data-stu-id="8be5e-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="8be5e-118">Selezionare **Aggiungi**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="8be5e-119">Denominare la classe *Movie*.</span><span class="sxs-lookup"><span data-stu-id="8be5e-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="8be5e-120">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="8be5e-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="8be5e-121">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="8be5e-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="8be5e-122">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="8be5e-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="8be5e-123">`[DataType(DataType.Date)]`: L'attributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="8be5e-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="8be5e-124">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="8be5e-124">With this attribute:</span></span>

  * <span data-ttu-id="8be5e-125">L'utente non deve immettere le informazioni sull'ora nel campo Data.</span><span class="sxs-lookup"><span data-stu-id="8be5e-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="8be5e-126">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="8be5e-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8be5e-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8be5e-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="8be5e-128">Aggiungere una cartella denominata *Modelli*.</span><span class="sxs-lookup"><span data-stu-id="8be5e-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="8be5e-129">Aggiungere una classe alla cartella *Modello* denominata *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="8be5e-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="8be5e-130">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="8be5e-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="8be5e-131">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="8be5e-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="8be5e-132">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="8be5e-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="8be5e-133">`[DataType(DataType.Date)]`: L'attributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="8be5e-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="8be5e-134">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="8be5e-134">With this attribute:</span></span>

  * <span data-ttu-id="8be5e-135">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="8be5e-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="8be5e-136">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="8be5e-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="8be5e-137">Aggiungere i pacchetti NuGet e gli strumenti EF</span><span class="sxs-lookup"><span data-stu-id="8be5e-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="8be5e-138">Aggiungere una classe di contesto dei dati</span><span class="sxs-lookup"><span data-stu-id="8be5e-138">Add a database context class</span></span>

1. <span data-ttu-id="8be5e-139">Nel progetto *:::no-loc(Razor)::: PagesMovie* creare una cartella denominata *Data*.</span><span class="sxs-lookup"><span data-stu-id="8be5e-139">In the *:::no-loc(Razor):::PagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="8be5e-140">Nella cartella *dati* aggiungere un file denominato *:::no-loc(Razor)::: PagesMovieContext.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8be5e-140">In the *Data* folder, add a file named *:::no-loc(Razor):::PagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

   <span data-ttu-id="8be5e-141">Il codice precedente crea una proprietà `DbSet` per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="8be5e-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="8be5e-142">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database e un'entità corrisponde a una riga della tabella.</span><span class="sxs-lookup"><span data-stu-id="8be5e-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="8be5e-143">Il codice non verrà compilato fino a quando le dipendenze non verranno aggiunte in un passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="8be5e-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="8be5e-144">Aggiungere una stringa di connessione del database</span><span class="sxs-lookup"><span data-stu-id="8be5e-144">Add a database connection string</span></span>

<span data-ttu-id="8be5e-145">Aggiungere una stringa di connessione al *:::no-loc(appsettings.json):::* file come illustrato nel codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="8be5e-145">Add a connection string to the *:::no-loc(appsettings.json):::* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="8be5e-146">Registrare il contesto del database</span><span class="sxs-lookup"><span data-stu-id="8be5e-146">Register the database context</span></span>

1. <span data-ttu-id="8be5e-147">Aggiungere le istruzioni `using` seguenti all'inizio di *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="8be5e-147">Add the following `using` statements at the top of *Startup.cs* :</span></span>

   ```csharp
   using :::no-loc(Razor):::PagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="8be5e-148">Registrare il contesto del database con il contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8be5e-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8be5e-149">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8be5e-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="8be5e-150">Nella **finestra degli strumenti della soluzione** , fare clic sul progetto *:::no-loc(Razor)::: PagesMovie* e quindi selezionare **Aggiungi** > **nuova cartella.** Assegnare un nome ai *modelli* di cartella.</span><span class="sxs-lookup"><span data-stu-id="8be5e-150">In the **Solution Tool Window** , control-click the *:::no-loc(Razor):::PagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="8be5e-151">CTRL-fare clic sulla cartella *modelli* , quindi selezionare **Aggiungi** > **nuovo file.**...</span><span class="sxs-lookup"><span data-stu-id="8be5e-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="8be5e-152">Nel finestra di dialogo **Nuovo file** :</span><span class="sxs-lookup"><span data-stu-id="8be5e-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="8be5e-153">Selezionare **Generale** nel riquadro a sinistra.</span><span class="sxs-lookup"><span data-stu-id="8be5e-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="8be5e-154">Selezionare **Classe vuota** nel riquadro centrale.</span><span class="sxs-lookup"><span data-stu-id="8be5e-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="8be5e-155">Denominare la classe **Filmato** e selezionare **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="8be5e-156">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="8be5e-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="8be5e-157">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="8be5e-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="8be5e-158">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="8be5e-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="8be5e-159">`[DataType(DataType.Date)]`: L'attributo [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="8be5e-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="8be5e-160">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="8be5e-160">With this attribute:</span></span>

  * <span data-ttu-id="8be5e-161">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="8be5e-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="8be5e-162">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="8be5e-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="8be5e-163">L'attributo [DataAnnotations](xref:System.ComponentModel.DataAnnotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="8be5e-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="8be5e-164">Compilare il progetto per verificare che non siano presenti errori di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8be5e-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="8be5e-165">Eseguire lo scaffolding del modello di filmato</span><span class="sxs-lookup"><span data-stu-id="8be5e-165">Scaffold the movie model</span></span>

<span data-ttu-id="8be5e-166">In questa sezione viene eseguito lo scaffolding del modello *Movie*.</span><span class="sxs-lookup"><span data-stu-id="8be5e-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="8be5e-167">Ovvero, lo strumento di impalcatura produce pagine per :::no-loc(Create)::: le operazioni di lettura, aggiornamento e :::no-loc(Delete)::: (CRUD) per il modello di film.</span><span class="sxs-lookup"><span data-stu-id="8be5e-167">That is, the scaffolding tool produces pages for :::no-loc(Create):::, Read, Update, and :::no-loc(Delete)::: (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8be5e-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8be5e-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="8be5e-169">:::no-loc(Create)::: una cartella *pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="8be5e-169">:::no-loc(Create)::: a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="8be5e-170">Fare clic con il pulsante destro del mouse sulla cartella *pagine* > **Aggiungi** > **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="8be5e-171">Denominare la cartella *Movies*.</span><span class="sxs-lookup"><span data-stu-id="8be5e-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="8be5e-172">Fare clic con il pulsante destro del mouse sulla cartella *pages/Movies* > **Aggiungi** > **nuovo elemento con impalcatura**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![Immagine relativa alle istruzioni precedenti.](model/_static/5/sca.png)

1. <span data-ttu-id="8be5e-174">Nella finestra di dialogo **Aggiungi impalcatura** selezionare **:::no-loc(Razor)::: pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-174">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffold.png)

1. <span data-ttu-id="8be5e-176">Completare la finestra di dialogo **Aggiungi :::no-loc(Razor)::: pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="8be5e-176">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="8be5e-177">Nell'elenco a discesa **classe modello** selezionare **Movie ( :::no-loc(Razor)::: PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-177">In the **Model class** drop down, select **Movie (:::no-loc(Razor):::PagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="8be5e-178">Nella riga **Classe contesto di dati** selezionare il segno più **+**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="8be5e-179">Nella finestra di dialogo **Aggiungi contesto dati** il nome della classe *:::no-loc(Razor)::: PagesMovie. Data. :::no-loc(Razor):::* Viene generato PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="8be5e-179">In the **Add Data Context** dialog, the class name *:::no-loc(Razor):::PagesMovie.Data.:::no-loc(Razor):::PagesMovieContext* is generated.</span></span>
   1. <span data-ttu-id="8be5e-180">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-180">Select **Add**.</span></span>

   ![Immagine relativa alle istruzioni precedenti.](model/_static/3/arp.png)

<span data-ttu-id="8be5e-182">Il *:::no-loc(appsettings.json):::* file viene aggiornato con la stringa di connessione utilizzata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="8be5e-182">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8be5e-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8be5e-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace :::no-loc(Razor):::PagesMovie.Pages_Movies rather than namespace :::no-loc(Razor):::PagesMovie.Pages.Movies
-->

* <span data-ttu-id="8be5e-184">Aprire una shell dei comandi nella directory del progetto che contiene i file *Program.cs* , *Startup.cs* e *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="8be5e-184">Open a command shell to the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="8be5e-185">**Per Windows** : eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="8be5e-185">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="8be5e-186">**Per MacOS e Linux** : eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="8be5e-186">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="8be5e-187">La tabella seguente illustra in dettaglio le opzioni del generatore di codice ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8be5e-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="8be5e-188">Opzione</span><span class="sxs-lookup"><span data-stu-id="8be5e-188">Option</span></span>               | <span data-ttu-id="8be5e-189">Descrizione</span><span class="sxs-lookup"><span data-stu-id="8be5e-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="8be5e-190">Nome del modello.</span><span class="sxs-lookup"><span data-stu-id="8be5e-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="8be5e-191">Classe `DbContext` da usare.</span><span class="sxs-lookup"><span data-stu-id="8be5e-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="8be5e-192">Uso del layout predefinito.</span><span class="sxs-lookup"><span data-stu-id="8be5e-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="8be5e-193">Percorso relativo della cartella di output per creare le viste.</span><span class="sxs-lookup"><span data-stu-id="8be5e-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="8be5e-194">Aggiunge `_ValidationScriptsPartial` a Edit e :::no-loc(Create)::: pages</span><span class="sxs-lookup"><span data-stu-id="8be5e-194">Adds `_ValidationScriptsPartial` to Edit and :::no-loc(Create)::: pages</span></span> |

<span data-ttu-id="8be5e-195">Usare l' `-h` opzione per ottenere informazioni sul `aspnet-codegenerator razorpage` comando:</span><span class="sxs-lookup"><span data-stu-id="8be5e-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="8be5e-196">Per ulteriori informazioni, vedere [DotNet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="8be5e-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="8be5e-197">Usare SQLite per lo sviluppo, SQL Server per la produzione</span><span class="sxs-lookup"><span data-stu-id="8be5e-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="8be5e-198">Quando si seleziona SQLite, il codice generato dal modello è pronto per lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="8be5e-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="8be5e-199">Nel codice seguente viene illustrato come inserire <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in `Startup` .</span><span class="sxs-lookup"><span data-stu-id="8be5e-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="8be5e-200">`IWebHostEnvironment` viene inserito in modo che l'app possa usare SQLite in fase di sviluppo e SQL Server nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="8be5e-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8be5e-201">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8be5e-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="8be5e-202">:::no-loc(Create)::: una cartella *pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="8be5e-202">:::no-loc(Create)::: a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="8be5e-203">CTRL: fare clic sulla cartella *pagine* > **Aggiungi** > **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="8be5e-204">Denominare la cartella *Movies*.</span><span class="sxs-lookup"><span data-stu-id="8be5e-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="8be5e-205">CTRL: fare clic sulla cartella *pagine/filmati* > **Aggiungi** > **Nuova impalcatura...**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![Immagine relativa alle istruzioni precedenti.](model/_static/scaMac.png)

1. <span data-ttu-id="8be5e-207">Nella finestra di dialogo **Nuova impalcatura** selezionare **:::no-loc(Razor)::: pagine con Entity Framework (CRUD)** > **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-207">In the **New Scaffolding** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="8be5e-209">Completare la finestra di dialogo **Aggiungi :::no-loc(Razor)::: pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="8be5e-209">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="8be5e-210">Nella **classe DbContext da usare:** Row, denominare la classe *:::no-loc(Razor)::: PagesMovie. Data. :::no-loc(Razor)::: PagesMovieContext*.</span><span class="sxs-lookup"><span data-stu-id="8be5e-210">In the **DbContext Class to use:** row, name the class *:::no-loc(Razor):::PagesMovie.Data.:::no-loc(Razor):::PagesMovieContext*.</span></span>
   1. <span data-ttu-id="8be5e-211">Selezionare **Fine**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-211">Select **Finish**.</span></span>

   ![Immagine relativa alle istruzioni precedenti.](model/_static/5/arpMac.png)

<span data-ttu-id="8be5e-213">Il *:::no-loc(appsettings.json):::* file viene aggiornato con la stringa di connessione utilizzata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="8be5e-213">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="8be5e-214">Usare SQLite per lo sviluppo, SQL Server per la produzione</span><span class="sxs-lookup"><span data-stu-id="8be5e-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="8be5e-215">Quando si seleziona SQLite, il codice generato dal modello è pronto per lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="8be5e-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="8be5e-216">Nel codice seguente viene illustrato come inserire <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in `Startup` .</span><span class="sxs-lookup"><span data-stu-id="8be5e-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="8be5e-217">`IWebHostEnvironment` viene inserito in modo che l'app possa usare SQLite in fase di sviluppo e SQL Server nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="8be5e-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="8be5e-218">File creati</span><span class="sxs-lookup"><span data-stu-id="8be5e-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8be5e-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8be5e-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8be5e-220">Il processo di scaffolding crea e aggiorna i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="8be5e-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="8be5e-221">*Pagine/film* : :::no-loc(Create)::: , :::no-loc(Delete)::: , Details, Edit e :::no-loc(Index)::: .</span><span class="sxs-lookup"><span data-stu-id="8be5e-221">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>
* <span data-ttu-id="8be5e-222">*Data/ :::no-loc(Razor)::: PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="8be5e-222">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="8be5e-223">Aggiornato</span><span class="sxs-lookup"><span data-stu-id="8be5e-223">Updated</span></span>

* <span data-ttu-id="8be5e-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="8be5e-224">*Startup.cs*</span></span>

<span data-ttu-id="8be5e-225">I file creati e aggiornati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="8be5e-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8be5e-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8be5e-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8be5e-227">Il processo di scaffolding crea i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="8be5e-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="8be5e-228">*Pagine/film* : :::no-loc(Create)::: , :::no-loc(Delete)::: , Details, Edit e :::no-loc(Index)::: .</span><span class="sxs-lookup"><span data-stu-id="8be5e-228">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>

<span data-ttu-id="8be5e-229">I file creati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="8be5e-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8be5e-230">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8be5e-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8be5e-231">Il processo di scaffolding crea e aggiorna i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="8be5e-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="8be5e-232">*Pagine/film* : :::no-loc(Create)::: , :::no-loc(Delete)::: , Details, Edit e :::no-loc(Index)::: .</span><span class="sxs-lookup"><span data-stu-id="8be5e-232">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>
* <span data-ttu-id="8be5e-233">*Data/ :::no-loc(Razor)::: PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="8be5e-233">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="8be5e-234">Aggiornato</span><span class="sxs-lookup"><span data-stu-id="8be5e-234">Updated</span></span>

* <span data-ttu-id="8be5e-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="8be5e-235">*Startup.cs*</span></span>

<span data-ttu-id="8be5e-236">I file creati e aggiornati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="8be5e-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="no-loccreate-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="8be5e-237">:::no-loc(Create)::: schema iniziale del database con la funzionalità di migrazione di EF</span><span class="sxs-lookup"><span data-stu-id="8be5e-237">:::no-loc(Create)::: the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="8be5e-238">La funzionalità migrazioni di Entity Framework Core fornisce un modo per:</span><span class="sxs-lookup"><span data-stu-id="8be5e-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="8be5e-239">:::no-loc(Create)::: schema iniziale del database.</span><span class="sxs-lookup"><span data-stu-id="8be5e-239">:::no-loc(Create)::: the initial database schema.</span></span>
* <span data-ttu-id="8be5e-240">Aggiornare in modo incrementale lo schema del database per mantenerlo sincronizzato con il modello di dati dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="8be5e-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="8be5e-241">Vengono conservati i dati esistenti nel database.</span><span class="sxs-lookup"><span data-stu-id="8be5e-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8be5e-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8be5e-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8be5e-243">In questa sezione viene usata la finestra **console di gestione pacchetti** (PMC) per:</span><span class="sxs-lookup"><span data-stu-id="8be5e-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="8be5e-244">Aggiungere una migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="8be5e-244">Add an initial migration.</span></span>
* <span data-ttu-id="8be5e-245">Aggiornare il database con la migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="8be5e-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="8be5e-246">Nel menu **Strumenti** selezionare **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![Menu della Console di Gestione pacchetti](model/_static/5/pmc.png)

1. <span data-ttu-id="8be5e-248">Nella Console di Gestione pacchetti immettere i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8be5e-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial:::no-loc(Create):::
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8be5e-249">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8be5e-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="8be5e-250">Eseguire i seguenti comandi dell'interfaccia della riga di comando .NET:</span><span class="sxs-lookup"><span data-stu-id="8be5e-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add Initial:::no-loc(Create):::
  dotnet ef database update
  ```

---

<span data-ttu-id="8be5e-251">I comandi precedenti generano l'avviso seguente: "nessun tipo specificato per la colonna decimale ' Price ' nel tipo di entità' Movie '.</span><span class="sxs-lookup"><span data-stu-id="8be5e-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="8be5e-252">This will cause values to be silently truncated if they do not fit in the default precision and scale. (I valori saranno quindi automaticamente troncati se non rispettano la precisione e la scala predefinite).</span><span class="sxs-lookup"><span data-stu-id="8be5e-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="8be5e-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Specificare in modo esplicito il tipo di colonna di SQL Server che può supportare tutti i valori usando 'HasColumnType()')"</span><span class="sxs-lookup"><span data-stu-id="8be5e-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="8be5e-254">Ignorare l'avviso poiché verrà risolto in un passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="8be5e-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="8be5e-255">Il comando `migrations` genera un codice per creare lo schema del database iniziale.</span><span class="sxs-lookup"><span data-stu-id="8be5e-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="8be5e-256">Lo schema è basato sul modello specificato in `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="8be5e-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="8be5e-257">L'argomento `Initial:::no-loc(Create):::` viene usato per denominare le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="8be5e-257">The `Initial:::no-loc(Create):::` argument is used to name the migrations.</span></span> <span data-ttu-id="8be5e-258">È possibile usare qualsiasi nome, ma per convenzione viene selezionato un nome che descrive la migrazione.</span><span class="sxs-lookup"><span data-stu-id="8be5e-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="8be5e-259">Il `update` comando esegue il `Up` Metodo nelle migrazioni che non sono state applicate.</span><span class="sxs-lookup"><span data-stu-id="8be5e-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="8be5e-260">In tal caso, `update` esegue il `Up` metodo nel file *migrations/ \<time-stamp> _Initial :::no-loc(Create)::: . cs* , che crea il database.</span><span class="sxs-lookup"><span data-stu-id="8be5e-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_Initial:::no-loc(Create):::.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8be5e-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8be5e-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="8be5e-262">Esaminare il contesto registrato con l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="8be5e-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="8be5e-263">ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8be5e-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="8be5e-264">I servizi, ad esempio il contesto di database EF Core, vengono registrati con l'inserimento delle dipendenze durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="8be5e-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="8be5e-265">I componenti che richiedono questi servizi, ad esempio :::no-loc(Razor)::: le pagine, vengono forniti tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="8be5e-265">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="8be5e-266">Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="8be5e-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="8be5e-267">Lo strumento di ponteggi crea automaticamente un contesto di database e lo registra con il contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8be5e-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="8be5e-268">Esaminare il metodo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8be5e-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="8be5e-269">La riga evidenziata è stata aggiunta dallo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="8be5e-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="8be5e-270">Le `:::no-loc(Razor):::PagesMovieContext` coordinate EF Core funzionalità, ad esempio :::no-loc(Create)::: , Read, Update e :::no-loc(Delete)::: , per il `Movie` modello.</span><span class="sxs-lookup"><span data-stu-id="8be5e-270">The `:::no-loc(Razor):::PagesMovieContext` coordinates EF Core functionality, such as :::no-loc(Create):::, Read, Update and :::no-loc(Delete):::, for the `Movie` model.</span></span> <span data-ttu-id="8be5e-271">Il contesto dei dati (`:::no-loc(Razor):::PagesMovieContext`) è derivato da [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="8be5e-271">The data context (`:::no-loc(Razor):::PagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="8be5e-272">Il contesto dei dati specifica le entità incluse nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="8be5e-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="8be5e-273">Il codice precedente crea una [proprietà \<Movie> DbSet](xref:Microsoft.EntityFrameworkCore.DbSet%601) per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="8be5e-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="8be5e-274">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="8be5e-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="8be5e-275">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="8be5e-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="8be5e-276">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="8be5e-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="8be5e-277">Per lo sviluppo locale, il [sistema di configurazione](xref:fundamentals/configuration/index) legge la stringa di connessione dal *:::no-loc(appsettings.json):::* file.</span><span class="sxs-lookup"><span data-stu-id="8be5e-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8be5e-278">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8be5e-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="8be5e-279">Esaminare il metodo `Up`.</span><span class="sxs-lookup"><span data-stu-id="8be5e-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="8be5e-280">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="8be5e-280">Test the app</span></span>

1. <span data-ttu-id="8be5e-281">Eseguire l'app e accodare `/Movies` all'URL nel browser (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="8be5e-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="8be5e-282">Se si riceve un messaggio di errore che indica che</span><span class="sxs-lookup"><span data-stu-id="8be5e-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="8be5e-283">Non è stato eseguita la [migrazione](#pmc).</span><span class="sxs-lookup"><span data-stu-id="8be5e-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="8be5e-284">Testare il **:::no-loc(Create):::** collegamento.</span><span class="sxs-lookup"><span data-stu-id="8be5e-284">Test the **:::no-loc(Create):::** link.</span></span>

   ![::: NO-LOC (Create)::: page](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="8be5e-286">Potrebbe non essere possibile immettere virgole decimali nel campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="8be5e-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="8be5e-287">Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese (Stati Uniti), è necessario localizzare l'app.</span><span class="sxs-lookup"><span data-stu-id="8be5e-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="8be5e-288">Per istruzioni sulla globalizzazione, vedere [questo problema su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="8be5e-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="8be5e-289">Testare la **modifica** , **i dettagli** e i **:::no-loc(Delete):::** collegamenti.</span><span class="sxs-lookup"><span data-stu-id="8be5e-289">Test the **Edit** , **Details** , and **:::no-loc(Delete):::** links.</span></span>

<span data-ttu-id="8be5e-290">L'esercitazione successiva illustra i file creati tramite scaffolding.</span><span class="sxs-lookup"><span data-stu-id="8be5e-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8be5e-291">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="8be5e-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="8be5e-292">[Precedente: introduzione](xref:tutorials/razor-pages/razor-pages-start) 
>  Passaggio [successivo: impalcatura :::no-loc(Razor)::: Pagine](xref:tutorials/razor-pages/page) di</span><span class="sxs-lookup"><span data-stu-id="8be5e-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="8be5e-293">In questa sezione vengono aggiunte le classi per la gestione dei filmati.</span><span class="sxs-lookup"><span data-stu-id="8be5e-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="8be5e-294">Le classi modello dell'app usano [Entity Framework Core (EF Core)](/ef/core) per lavorare con il database.</span><span class="sxs-lookup"><span data-stu-id="8be5e-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="8be5e-295">EF Core è un mapper relazionale a oggetti (O/RM) che semplifica l'accesso ai dati.</span><span class="sxs-lookup"><span data-stu-id="8be5e-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="8be5e-296">Le classi di modello sono dette classi POCO (da "plain-old CLR objects") perché non hanno alcuna dipendenza in EF Core.</span><span class="sxs-lookup"><span data-stu-id="8be5e-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="8be5e-297">Definiscono le proprietà dei dati archiviati nel database.</span><span class="sxs-lookup"><span data-stu-id="8be5e-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="8be5e-298">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="8be5e-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="8be5e-299">Aggiungere un modello di dati</span><span class="sxs-lookup"><span data-stu-id="8be5e-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8be5e-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8be5e-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8be5e-301">Fare clic con il pulsante destro del mouse sul progetto **:::no-loc(Razor)::: PagesMovie** > **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-301">Right-click the **:::no-loc(Razor):::PagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="8be5e-302">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="8be5e-302">Name the folder *Models*.</span></span>

<span data-ttu-id="8be5e-303">Fare clic con il pulsante destro del mouse sulla cartella *modelli* .</span><span class="sxs-lookup"><span data-stu-id="8be5e-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="8be5e-304">Selezionare **Aggiungi**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="8be5e-305">Denominare la classe **Movie**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-305">Name the class **Movie**.</span></span>

<span data-ttu-id="8be5e-306">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="8be5e-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="8be5e-307">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="8be5e-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="8be5e-308">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="8be5e-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="8be5e-309">`[DataType(DataType.Date)]`: L'attributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="8be5e-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="8be5e-310">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="8be5e-310">With this attribute:</span></span>

  * <span data-ttu-id="8be5e-311">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="8be5e-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="8be5e-312">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="8be5e-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="8be5e-313">L'attributo [DataAnnotations](xref:System.ComponentModel.DataAnnotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="8be5e-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8be5e-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8be5e-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8be5e-315">Aggiungere una cartella denominata *Modelli*.</span><span class="sxs-lookup"><span data-stu-id="8be5e-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="8be5e-316">Aggiungere una classe alla cartella *Modello* denominata *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="8be5e-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="8be5e-317">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="8be5e-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="8be5e-318">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="8be5e-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="8be5e-319">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="8be5e-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="8be5e-320">`[DataType(DataType.Date)]`: L'attributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="8be5e-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="8be5e-321">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="8be5e-321">With this attribute:</span></span>

  * <span data-ttu-id="8be5e-322">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="8be5e-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="8be5e-323">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="8be5e-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="8be5e-324">L'attributo [DataAnnotations](xref:System.ComponentModel.DataAnnotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="8be5e-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="8be5e-325">Aggiungere i pacchetti NuGet e gli strumenti EF</span><span class="sxs-lookup"><span data-stu-id="8be5e-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="8be5e-326">Aggiungere una classe di contesto dei dati</span><span class="sxs-lookup"><span data-stu-id="8be5e-326">Add a database context class</span></span>

* <span data-ttu-id="8be5e-327">Nel progetto *:::no-loc(Razor)::: PagesMovie* creare una nuova cartella denominata *Data*.</span><span class="sxs-lookup"><span data-stu-id="8be5e-327">In the *:::no-loc(Razor):::PagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="8be5e-328">Aggiungere la classe `:::no-loc(Razor):::PagesMovieContext` seguente alla cartella *Data* :</span><span class="sxs-lookup"><span data-stu-id="8be5e-328">Add the following `:::no-loc(Razor):::PagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="8be5e-329">Il codice precedente crea una proprietà `DbSet` per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="8be5e-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="8be5e-330">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database e un'entità corrisponde a una riga della tabella.</span><span class="sxs-lookup"><span data-stu-id="8be5e-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="8be5e-331">Il codice non verrà compilato fino a quando le dipendenze non verranno aggiunte in un passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="8be5e-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="8be5e-332">Aggiungere una stringa di connessione del database</span><span class="sxs-lookup"><span data-stu-id="8be5e-332">Add a database connection string</span></span>

<span data-ttu-id="8be5e-333">Aggiungere una stringa di connessione al *:::no-loc(appsettings.json):::* file come illustrato nel codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="8be5e-333">Add a connection string to the *:::no-loc(appsettings.json):::* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="8be5e-334">Registrare il contesto del database</span><span class="sxs-lookup"><span data-stu-id="8be5e-334">Register the database context</span></span>

<span data-ttu-id="8be5e-335">Aggiungere le istruzioni `using` seguenti all'inizio di *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="8be5e-335">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using :::no-loc(Razor):::PagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="8be5e-336">Registrare il contesto del database con il contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8be5e-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8be5e-337">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8be5e-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8be5e-338">Nella **finestra degli strumenti della soluzione** , fare clic sul progetto **:::no-loc(Razor)::: PagesMovie** e quindi selezionare **Aggiungi** > **nuova cartella.** Assegnare un nome ai *modelli* di cartella.</span><span class="sxs-lookup"><span data-stu-id="8be5e-338">In the **Solution Tool Window** , control-click the **:::no-loc(Razor):::PagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="8be5e-339">Fare clic con il pulsante destro del mouse sulla cartella *Models* , quindi scegliere **Aggiungi** > **nuovo file**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="8be5e-340">Nel finestra di dialogo **Nuovo file** :</span><span class="sxs-lookup"><span data-stu-id="8be5e-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="8be5e-341">Selezionare **Generale** nel riquadro a sinistra.</span><span class="sxs-lookup"><span data-stu-id="8be5e-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="8be5e-342">Selezionare **Classe vuota** nel riquadro centrale.</span><span class="sxs-lookup"><span data-stu-id="8be5e-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="8be5e-343">Denominare la classe **Filmato** e selezionare **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="8be5e-344">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="8be5e-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="8be5e-345">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="8be5e-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="8be5e-346">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="8be5e-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="8be5e-347">`[DataType(DataType.Date)]`: L'attributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="8be5e-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="8be5e-348">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="8be5e-348">With this attribute:</span></span>

  * <span data-ttu-id="8be5e-349">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="8be5e-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="8be5e-350">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="8be5e-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="8be5e-351">L'attributo [DataAnnotations](xref:System.ComponentModel.DataAnnotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="8be5e-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="8be5e-352">Compilare il progetto per verificare che non siano presenti errori di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8be5e-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="8be5e-353">Eseguire lo scaffolding del modello di filmato</span><span class="sxs-lookup"><span data-stu-id="8be5e-353">Scaffold the movie model</span></span>

<span data-ttu-id="8be5e-354">In questa sezione viene eseguito lo scaffolding del modello *Movie*.</span><span class="sxs-lookup"><span data-stu-id="8be5e-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="8be5e-355">Ovvero, lo strumento di impalcatura produce pagine per :::no-loc(Create)::: le operazioni di lettura, aggiornamento e :::no-loc(Delete)::: (CRUD) per il modello di film.</span><span class="sxs-lookup"><span data-stu-id="8be5e-355">That is, the scaffolding tool produces pages for :::no-loc(Create):::, Read, Update, and :::no-loc(Delete)::: (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8be5e-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8be5e-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8be5e-357">:::no-loc(Create)::: una cartella *pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="8be5e-357">:::no-loc(Create)::: a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="8be5e-358">Fare clic con il pulsante destro del mouse sulla cartella *pagine* > **Aggiungi** > **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="8be5e-359">Denominare la cartella *Movies*.</span><span class="sxs-lookup"><span data-stu-id="8be5e-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="8be5e-360">Fare clic con il pulsante destro del mouse sulla cartella *pages/Movies* > **Aggiungi** > **nuovo elemento con impalcatura**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/sca.png)

<span data-ttu-id="8be5e-362">Nella finestra di dialogo **Aggiungi impalcatura** selezionare **:::no-loc(Razor)::: pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-362">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffold.png)

<span data-ttu-id="8be5e-364">Completare la finestra di dialogo **Aggiungi :::no-loc(Razor)::: pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="8be5e-364">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="8be5e-365">Nell'elenco a discesa **classe modello** selezionare **Movie ( :::no-loc(Razor)::: PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-365">In the **Model class** drop down, select **Movie (:::no-loc(Razor):::PagesMovie.Models)**.</span></span>
* <span data-ttu-id="8be5e-366">Nella riga della **classe del contesto dati** selezionare il **+** segno (segno più) e modificare il nome generato da :::no-loc(Razor)::: PagesMovie. **Modelli**. :::no-loc(Razor)::: Da PagesMovieContext a :::no-loc(Razor)::: PagesMovie. **Dati**. :::no-loc(Razor)::: PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="8be5e-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from :::no-loc(Razor):::PagesMovie. **Models**.:::no-loc(Razor):::PagesMovieContext to :::no-loc(Razor):::PagesMovie. **Data**.:::no-loc(Razor):::PagesMovieContext.</span></span> <span data-ttu-id="8be5e-367">[Questa modifica](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) non è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="8be5e-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="8be5e-368">Crea la classe del contesto di database con lo spazio dei nomi corretto.</span><span class="sxs-lookup"><span data-stu-id="8be5e-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="8be5e-369">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-369">Select **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/3/arp.png)

<span data-ttu-id="8be5e-371">Il *:::no-loc(appsettings.json):::* file viene aggiornato con la stringa di connessione utilizzata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="8be5e-371">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8be5e-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8be5e-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace :::no-loc(Razor):::PagesMovie.Pages_Movies rather than namespace :::no-loc(Razor):::PagesMovie.Pages.Movies
-->

* <span data-ttu-id="8be5e-373">Aprire una finestra di comando nella directory del progetto che contiene i file *Program.cs* , *Startup.cs* e *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="8be5e-373">Open a command window in the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="8be5e-374">**Per Windows** : eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="8be5e-374">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="8be5e-375">**Per MacOS e Linux** : eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="8be5e-375">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="8be5e-376">La tabella seguente illustra in dettaglio le opzioni del generatore di codice ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="8be5e-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="8be5e-377">Opzione</span><span class="sxs-lookup"><span data-stu-id="8be5e-377">Option</span></span>               | <span data-ttu-id="8be5e-378">Descrizione</span><span class="sxs-lookup"><span data-stu-id="8be5e-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="8be5e-379">Nome del modello.</span><span class="sxs-lookup"><span data-stu-id="8be5e-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="8be5e-380">Classe `DbContext` da usare.</span><span class="sxs-lookup"><span data-stu-id="8be5e-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="8be5e-381">Uso del layout predefinito.</span><span class="sxs-lookup"><span data-stu-id="8be5e-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="8be5e-382">Percorso relativo della cartella di output per creare le viste.</span><span class="sxs-lookup"><span data-stu-id="8be5e-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="8be5e-383">Aggiunge `_ValidationScriptsPartial` a Edit e :::no-loc(Create)::: pages</span><span class="sxs-lookup"><span data-stu-id="8be5e-383">Adds `_ValidationScriptsPartial` to Edit and :::no-loc(Create)::: pages</span></span> |

<span data-ttu-id="8be5e-384">Usare l' `-h` opzione per ottenere informazioni sul `aspnet-codegenerator razorpage` comando:</span><span class="sxs-lookup"><span data-stu-id="8be5e-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="8be5e-385">Per ulteriori informazioni, vedere [DotNet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="8be5e-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="8be5e-386">Usare SQLite per lo sviluppo, SQL Server per la produzione</span><span class="sxs-lookup"><span data-stu-id="8be5e-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="8be5e-387">Quando si seleziona SQLite, il codice generato dal modello è pronto per lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="8be5e-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="8be5e-388">Il codice seguente illustra come inserire all' <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> avvio.</span><span class="sxs-lookup"><span data-stu-id="8be5e-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="8be5e-389">`IWebHostEnvironment` viene inserito in modo `ConfigureServices` che possa usare SQLite per lo sviluppo e la SQL Server nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="8be5e-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8be5e-390">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8be5e-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8be5e-391">:::no-loc(Create)::: una cartella *pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="8be5e-391">:::no-loc(Create)::: a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="8be5e-392">Fare clic con il pulsante destro del mouse sulla cartella *pagine* > **Aggiungi** > **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="8be5e-393">Denominare la cartella *Movies*.</span><span class="sxs-lookup"><span data-stu-id="8be5e-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="8be5e-394">Fare clic con il pulsante destro del mouse sulla cartella *pages/Movies* > **Aggiungi** > **Nuova impalcatura...**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/scaMac.png)

<span data-ttu-id="8be5e-396">Nella finestra di dialogo **Nuova impalcatura** selezionare **:::no-loc(Razor)::: pagine con Entity Framework (CRUD)** > **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-396">In the **New Scaffolding** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="8be5e-398">Completare la finestra di dialogo **Aggiungi :::no-loc(Razor)::: pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="8be5e-398">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="8be5e-399">Nell'elenco a discesa **classe modello** selezionare o digitare **Movie ( :::no-loc(Razor)::: PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-399">In the **Model class** drop down, select, or type, **Movie (:::no-loc(Razor):::PagesMovie.Models)**.</span></span>
* <span data-ttu-id="8be5e-400">Nella riga della **classe del contesto dati** Digitare il nome della nuova classe, :::no-loc(Razor)::: PagesMovie. **Dati**. :::no-loc(Razor)::: PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="8be5e-400">In the **Data context class** row, type the name for the new class, :::no-loc(Razor):::PagesMovie. **Data**.:::no-loc(Razor):::PagesMovieContext.</span></span> <span data-ttu-id="8be5e-401">[Questa modifica](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) non è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="8be5e-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="8be5e-402">Crea la classe del contesto di database con lo spazio dei nomi corretto.</span><span class="sxs-lookup"><span data-stu-id="8be5e-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="8be5e-403">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-403">Select **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/arpMac.png)

<span data-ttu-id="8be5e-405">Il *:::no-loc(appsettings.json):::* file viene aggiornato con la stringa di connessione utilizzata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="8be5e-405">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="8be5e-406">Aggiungi strumenti EF</span><span class="sxs-lookup"><span data-stu-id="8be5e-406">Add EF tools</span></span>

<span data-ttu-id="8be5e-407">Eseguire il comando interfaccia della riga di comando di .NET Core seguente:</span><span class="sxs-lookup"><span data-stu-id="8be5e-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="8be5e-408">Il comando precedente aggiunge gli strumenti Entity Framework Core per l'interfaccia della riga di comando di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="8be5e-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="8be5e-409">Per ulteriori informazioni, vedere [riferimento agli strumenti Entity Framework Core-interfaccia della riga di comando di .NET Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="8be5e-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="8be5e-410">Usare SQLite per lo sviluppo, SQL Server per la produzione</span><span class="sxs-lookup"><span data-stu-id="8be5e-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="8be5e-411">Quando si seleziona SQLite, il codice generato dal modello è pronto per lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="8be5e-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="8be5e-412">Il codice seguente illustra come inserire all' <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> avvio.</span><span class="sxs-lookup"><span data-stu-id="8be5e-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="8be5e-413">`IWebHostEnvironment` viene inserito in modo `ConfigureServices` che possa usare SQLite per lo sviluppo e la SQL Server nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="8be5e-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="8be5e-414">File creati</span><span class="sxs-lookup"><span data-stu-id="8be5e-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8be5e-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8be5e-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8be5e-416">Il processo di scaffolding crea e aggiorna i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="8be5e-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="8be5e-417">*Pagine/film* : :::no-loc(Create)::: , :::no-loc(Delete)::: , Details, Edit e :::no-loc(Index)::: .</span><span class="sxs-lookup"><span data-stu-id="8be5e-417">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>
* <span data-ttu-id="8be5e-418">*Data/ :::no-loc(Razor)::: PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="8be5e-418">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="8be5e-419">Aggiornato</span><span class="sxs-lookup"><span data-stu-id="8be5e-419">Updated</span></span>

* <span data-ttu-id="8be5e-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="8be5e-420">*Startup.cs*</span></span>

<span data-ttu-id="8be5e-421">I file creati e aggiornati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="8be5e-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8be5e-422">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8be5e-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8be5e-423">Il processo di scaffolding crea e aggiorna i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="8be5e-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="8be5e-424">*Pagine/film* : :::no-loc(Create)::: , :::no-loc(Delete)::: , Details, Edit e :::no-loc(Index)::: .</span><span class="sxs-lookup"><span data-stu-id="8be5e-424">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>
* <span data-ttu-id="8be5e-425">*Data/ :::no-loc(Razor)::: PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="8be5e-425">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="8be5e-426">Aggiornato</span><span class="sxs-lookup"><span data-stu-id="8be5e-426">Updated</span></span>

* <span data-ttu-id="8be5e-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="8be5e-427">*Startup.cs*</span></span>

<span data-ttu-id="8be5e-428">I file creati e aggiornati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="8be5e-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8be5e-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8be5e-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8be5e-430">Il processo di scaffolding crea i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="8be5e-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="8be5e-431">*Pagine/film* : :::no-loc(Create)::: , :::no-loc(Delete)::: , Details, Edit e :::no-loc(Index)::: .</span><span class="sxs-lookup"><span data-stu-id="8be5e-431">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>

<span data-ttu-id="8be5e-432">I file creati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="8be5e-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="8be5e-433">Migrazione iniziale</span><span class="sxs-lookup"><span data-stu-id="8be5e-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8be5e-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8be5e-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8be5e-435">In questa sezione viene usata la Console di Gestione pacchetti (PMC) per:</span><span class="sxs-lookup"><span data-stu-id="8be5e-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="8be5e-436">Aggiungere una migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="8be5e-436">Add an initial migration.</span></span>
* <span data-ttu-id="8be5e-437">Aggiornare il database con la migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="8be5e-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="8be5e-438">Nel menu **Strumenti** selezionare **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu della Console di Gestione pacchetti](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="8be5e-440">Nella Console di Gestione pacchetti immettere i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8be5e-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial:::no-loc(Create):::
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8be5e-441">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8be5e-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="8be5e-442">Eseguire i seguenti comandi dell'interfaccia della riga di comando di .NET Core:</span><span class="sxs-lookup"><span data-stu-id="8be5e-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add Initial:::no-loc(Create):::
dotnet ef database update
```

---

<span data-ttu-id="8be5e-443">I comandi precedenti generano l'avviso seguente: "nessun tipo specificato per la colonna decimale ' Price ' nel tipo di entità' Movie '.</span><span class="sxs-lookup"><span data-stu-id="8be5e-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="8be5e-444">This will cause values to be silently truncated if they do not fit in the default precision and scale. (I valori saranno quindi automaticamente troncati se non rispettano la precisione e la scala predefinite).</span><span class="sxs-lookup"><span data-stu-id="8be5e-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="8be5e-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Specificare in modo esplicito il tipo di colonna di SQL Server che può supportare tutti i valori usando 'HasColumnType()')"</span><span class="sxs-lookup"><span data-stu-id="8be5e-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="8be5e-446">Ignorare l'avviso poiché verrà risolto in un passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="8be5e-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="8be5e-447">Il comando Migrations genera il codice per creare lo schema del database iniziale.</span><span class="sxs-lookup"><span data-stu-id="8be5e-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="8be5e-448">Lo schema è basato sul modello specificato in `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="8be5e-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="8be5e-449">L'argomento `Initial:::no-loc(Create):::` viene usato per denominare le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="8be5e-449">The `Initial:::no-loc(Create):::` argument is used to name the migrations.</span></span> <span data-ttu-id="8be5e-450">È possibile usare qualsiasi nome, ma per convenzione viene selezionato un nome che descrive la migrazione.</span><span class="sxs-lookup"><span data-stu-id="8be5e-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="8be5e-451">Il `update` comando esegue il `Up` Metodo nelle migrazioni che non sono state applicate.</span><span class="sxs-lookup"><span data-stu-id="8be5e-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="8be5e-452">In tal caso, `update` esegue il `Up` metodo nel file  *migrations/ \<time-stamp> _Initial :::no-loc(Create)::: . cs* , che crea il database.</span><span class="sxs-lookup"><span data-stu-id="8be5e-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_Initial:::no-loc(Create):::.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8be5e-453">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8be5e-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="8be5e-454">Esaminare il contesto registrato con l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="8be5e-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="8be5e-455">ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8be5e-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="8be5e-456">I servizi, ad esempio il contesto del contesto del database EF Core, vengono registrati con l'inserimento delle dipendenze durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="8be5e-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="8be5e-457">I componenti che richiedono questi servizi, ad esempio :::no-loc(Razor)::: le pagine, vengono forniti tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="8be5e-457">Components that require these services, such as :::no-loc(Razor)::: Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="8be5e-458">Il codice del costruttore che ottiene un'istanza del contesto del contesto del database viene illustrato più avanti nell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="8be5e-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="8be5e-459">Lo strumento di ponteggi crea automaticamente un contesto del contesto del database e lo registra con il contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8be5e-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="8be5e-460">Esaminare il metodo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8be5e-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="8be5e-461">La riga evidenziata è stata aggiunta dallo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="8be5e-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="8be5e-462">Le `:::no-loc(Razor):::PagesMovieContext` coordinate EF Core funzionalità, ad esempio :::no-loc(Create)::: , Read, Update e :::no-loc(Delete)::: , per il `Movie` modello.</span><span class="sxs-lookup"><span data-stu-id="8be5e-462">The `:::no-loc(Razor):::PagesMovieContext` coordinates EF Core functionality, such as :::no-loc(Create):::, Read, Update and :::no-loc(Delete):::, for the `Movie` model.</span></span> <span data-ttu-id="8be5e-463">Il contesto dei dati (`:::no-loc(Razor):::PagesMovieContext`) è derivato da [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="8be5e-463">The data context (`:::no-loc(Razor):::PagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="8be5e-464">Il contesto dei dati specifica le entità incluse nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="8be5e-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="8be5e-465">Il codice precedente crea una [proprietà \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="8be5e-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="8be5e-466">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="8be5e-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="8be5e-467">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="8be5e-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="8be5e-468">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="8be5e-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="8be5e-469">Per lo sviluppo locale, il [sistema di configurazione](xref:fundamentals/configuration/index) legge la stringa di connessione dal *:::no-loc(appsettings.json):::* file.</span><span class="sxs-lookup"><span data-stu-id="8be5e-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8be5e-470">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8be5e-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="8be5e-471">Esaminare il metodo `Up`.</span><span class="sxs-lookup"><span data-stu-id="8be5e-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="8be5e-472">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="8be5e-472">Test the app</span></span>

* <span data-ttu-id="8be5e-473">Eseguire l'app e accodare `/Movies` all'URL nel browser (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="8be5e-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="8be5e-474">Se viene visualizzato l'errore:</span><span class="sxs-lookup"><span data-stu-id="8be5e-474">If you get the error:</span></span>

```console
SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="8be5e-475">Non è stato eseguita la [migrazione](#pmc).</span><span class="sxs-lookup"><span data-stu-id="8be5e-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="8be5e-476">Testare il **:::no-loc(Create):::** collegamento.</span><span class="sxs-lookup"><span data-stu-id="8be5e-476">Test the **:::no-loc(Create):::** link.</span></span>

  ![::: NO-LOC (Create)::: page](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="8be5e-478">Potrebbe non essere possibile immettere virgole decimali nel campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="8be5e-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="8be5e-479">Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese (Stati Uniti), è necessario localizzare l'app.</span><span class="sxs-lookup"><span data-stu-id="8be5e-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="8be5e-480">Per istruzioni sulla globalizzazione, vedere [questo problema su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="8be5e-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="8be5e-481">Testare la **modifica** , **i dettagli** e i **:::no-loc(Delete):::** collegamenti.</span><span class="sxs-lookup"><span data-stu-id="8be5e-481">Test the **Edit** , **Details** , and **:::no-loc(Delete):::** links.</span></span>

<span data-ttu-id="8be5e-482">L'esercitazione successiva illustra i file creati tramite scaffolding.</span><span class="sxs-lookup"><span data-stu-id="8be5e-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8be5e-483">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="8be5e-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="8be5e-484">[Precedente: introduzione](xref:tutorials/razor-pages/razor-pages-start) 
>  Passaggio [successivo: impalcatura :::no-loc(Razor)::: Pagine](xref:tutorials/razor-pages/page) di</span><span class="sxs-lookup"><span data-stu-id="8be5e-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8be5e-485">In questa sezione vengono aggiunte le classi per la gestione dei film in un [database SQLite](https://www.sqlite.org/index.html)multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="8be5e-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="8be5e-486">Le app create da un modello di ASP.NET Core usano un database SQLite.</span><span class="sxs-lookup"><span data-stu-id="8be5e-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="8be5e-487">Le classi modello dell'app vengono usate con [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core provider di database](/ef/core/providers/sqlite)) per lavorare con il database.</span><span class="sxs-lookup"><span data-stu-id="8be5e-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="8be5e-488">EF Core è un framework ORM (Object-Relational Mapping) che semplifica l'accesso ai dati.</span><span class="sxs-lookup"><span data-stu-id="8be5e-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="8be5e-489">Le classi di modello sono dette classi POCO (da "plain-old CLR objects") perché non hanno alcuna dipendenza in EF Core.</span><span class="sxs-lookup"><span data-stu-id="8be5e-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="8be5e-490">Definiscono le proprietà dei dati archiviati nel database.</span><span class="sxs-lookup"><span data-stu-id="8be5e-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="8be5e-491">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="8be5e-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="8be5e-492">Aggiungere un modello di dati</span><span class="sxs-lookup"><span data-stu-id="8be5e-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8be5e-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8be5e-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8be5e-494">Fare clic con il pulsante destro del mouse sul progetto **:::no-loc(Razor)::: PagesMovie** > **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-494">Right-click the **:::no-loc(Razor):::PagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="8be5e-495">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="8be5e-495">Name the folder *Models*.</span></span>

<span data-ttu-id="8be5e-496">Fare clic con il pulsante destro del mouse sulla cartella *modelli* .</span><span class="sxs-lookup"><span data-stu-id="8be5e-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="8be5e-497">Selezionare **Aggiungi**  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="8be5e-498">Denominare la classe **Movie**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-498">Name the class **Movie**.</span></span>

<span data-ttu-id="8be5e-499">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="8be5e-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="8be5e-500">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="8be5e-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="8be5e-501">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="8be5e-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="8be5e-502">`[DataType(DataType.Date)]`: L'attributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="8be5e-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="8be5e-503">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="8be5e-503">With this attribute:</span></span>

  * <span data-ttu-id="8be5e-504">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="8be5e-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="8be5e-505">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="8be5e-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="8be5e-506">L'attributo [DataAnnotations](xref:System.ComponentModel.DataAnnotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="8be5e-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8be5e-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8be5e-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8be5e-508">Aggiungere una cartella denominata *Modelli*.</span><span class="sxs-lookup"><span data-stu-id="8be5e-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="8be5e-509">Aggiungere una classe alla cartella *Modello* denominata *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="8be5e-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="8be5e-510">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="8be5e-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="8be5e-511">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="8be5e-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="8be5e-512">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="8be5e-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="8be5e-513">`[DataType(DataType.Date)]`: L'attributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="8be5e-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="8be5e-514">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="8be5e-514">With this attribute:</span></span>

  * <span data-ttu-id="8be5e-515">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="8be5e-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="8be5e-516">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="8be5e-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="8be5e-517">L'attributo [DataAnnotations](xref:System.ComponentModel.DataAnnotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="8be5e-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="8be5e-518">Aggiungere i pacchetti NuGet e gli strumenti EF</span><span class="sxs-lookup"><span data-stu-id="8be5e-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="8be5e-519">Aggiungere una classe di contesto dei dati</span><span class="sxs-lookup"><span data-stu-id="8be5e-519">Add a database context class</span></span>

<span data-ttu-id="8be5e-520">Nel :::no-loc(Razor)::: progetto PagesMovie creare una nuova cartella denominata *Data*.</span><span class="sxs-lookup"><span data-stu-id="8be5e-520">In the :::no-loc(Razor):::PagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="8be5e-521">Aggiungere la classe `:::no-loc(Razor):::PagesMovieContext` seguente alla cartella *Data* :</span><span class="sxs-lookup"><span data-stu-id="8be5e-521">Add the following `:::no-loc(Razor):::PagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="8be5e-522">Il codice precedente crea una proprietà `DbSet` per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="8be5e-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="8be5e-523">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database e un'entità corrisponde a una riga della tabella.</span><span class="sxs-lookup"><span data-stu-id="8be5e-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="8be5e-524">Il codice non verrà compilato fino a quando le dipendenze non verranno aggiunte in un passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="8be5e-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="8be5e-525">Aggiungere una stringa di connessione del database</span><span class="sxs-lookup"><span data-stu-id="8be5e-525">Add a database connection string</span></span>

<span data-ttu-id="8be5e-526">Aggiungere una stringa di connessione al *:::no-loc(appsettings.json):::* file come illustrato nel codice evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="8be5e-526">Add a connection string to the *:::no-loc(appsettings.json):::* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="8be5e-527">Aggiungere i pacchetti NuGet necessari</span><span class="sxs-lookup"><span data-stu-id="8be5e-527">Add required NuGet packages</span></span>

<span data-ttu-id="8be5e-528">Eseguire il comando interfaccia della riga di comando di .NET Core seguente per aggiungere SQLite e CodeGeneration. Design al progetto:</span><span class="sxs-lookup"><span data-stu-id="8be5e-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="8be5e-529">Il pacchetto `Microsoft.VisualStudio.Web.CodeGeneration.Design` è necessario per lo scaffolding.</span><span class="sxs-lookup"><span data-stu-id="8be5e-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="8be5e-530">Registrare il contesto del database</span><span class="sxs-lookup"><span data-stu-id="8be5e-530">Register the database context</span></span>

<span data-ttu-id="8be5e-531">Aggiungere le istruzioni `using` seguenti all'inizio di *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="8be5e-531">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using :::no-loc(Razor):::PagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="8be5e-532">Registrare il contesto del database con il contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8be5e-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="8be5e-533">Compilare il progetto per il controllo di eventuali errori.</span><span class="sxs-lookup"><span data-stu-id="8be5e-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8be5e-534">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8be5e-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8be5e-535">Nella **finestra degli strumenti della soluzione** , fare clic sul progetto *:::no-loc(Razor)::: PagesMovie* e quindi selezionare **Aggiungi**  >  **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-535">In **Solution Tool Window** , control-click the *:::no-loc(Razor):::PagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="8be5e-536">Assegnare il nome *Modelli* alla cartella.</span><span class="sxs-lookup"><span data-stu-id="8be5e-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="8be5e-537">CTRL: fare clic sulla cartella *modelli* , quindi selezionare **Aggiungi** > **nuovo file**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="8be5e-538">Nel finestra di dialogo **Nuovo file** :</span><span class="sxs-lookup"><span data-stu-id="8be5e-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="8be5e-539">Selezionare **Generale** nel riquadro a sinistra.</span><span class="sxs-lookup"><span data-stu-id="8be5e-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="8be5e-540">Selezionare **Classe vuota** nel riquadro centrale.</span><span class="sxs-lookup"><span data-stu-id="8be5e-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="8be5e-541">Denominare la classe **Filmato** e selezionare **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="8be5e-542">Aggiungere le proprietà seguenti alla classe `Movie`:</span><span class="sxs-lookup"><span data-stu-id="8be5e-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="8be5e-543">La classe `Movie` contiene:</span><span class="sxs-lookup"><span data-stu-id="8be5e-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="8be5e-544">Il campo `ID` è richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="8be5e-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="8be5e-545">`[DataType(DataType.Date)]`: L'attributo [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) specifica il tipo di dati ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="8be5e-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="8be5e-546">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="8be5e-546">With this attribute:</span></span>

  * <span data-ttu-id="8be5e-547">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="8be5e-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="8be5e-548">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="8be5e-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="8be5e-549">L'attributo [DataAnnotations](xref:System.ComponentModel.DataAnnotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="8be5e-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="8be5e-550">Compilare il progetto per verificare che non siano presenti errori di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8be5e-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="8be5e-551">Eseguire lo scaffolding del modello di filmato</span><span class="sxs-lookup"><span data-stu-id="8be5e-551">Scaffold the movie model</span></span>

<span data-ttu-id="8be5e-552">In questa sezione viene eseguito lo scaffolding del modello *Movie*.</span><span class="sxs-lookup"><span data-stu-id="8be5e-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="8be5e-553">Ovvero, lo strumento di impalcatura produce pagine per :::no-loc(Create)::: le operazioni di lettura, aggiornamento e :::no-loc(Delete)::: (CRUD) per il modello di film.</span><span class="sxs-lookup"><span data-stu-id="8be5e-553">That is, the scaffolding tool produces pages for :::no-loc(Create):::, Read, Update, and :::no-loc(Delete)::: (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8be5e-554">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8be5e-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8be5e-555">:::no-loc(Create)::: una cartella *pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="8be5e-555">:::no-loc(Create)::: a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="8be5e-556">Fare clic con il pulsante destro del mouse sulla cartella *pagine* > **Aggiungi** > **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="8be5e-557">Denominare la cartella *Movies*.</span><span class="sxs-lookup"><span data-stu-id="8be5e-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="8be5e-558">Fare clic con il pulsante destro del mouse sulla cartella *pages/Movies* > **Aggiungi** > **nuovo elemento con impalcatura**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/sca.png)

<span data-ttu-id="8be5e-560">Nella finestra di dialogo **Aggiungi impalcatura** selezionare **:::no-loc(Razor)::: pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-560">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffold.png)

<span data-ttu-id="8be5e-562">Completare la finestra di dialogo **Aggiungi :::no-loc(Razor)::: pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="8be5e-562">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="8be5e-563">Nell'elenco a discesa **classe modello** selezionare **Movie ( :::no-loc(Razor)::: PagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-563">In the **Model class** drop down, select **Movie (:::no-loc(Razor):::PagesMovie.Models)**.</span></span>
* <span data-ttu-id="8be5e-564">Nella riga della **classe del contesto dati** selezionare il **+** segno (più) e accettare il nome generato **:::no-loc(Razor)::: PagesMovie. Models. :::no-loc(Razor)::: PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **:::no-loc(Razor):::PagesMovie.Models.:::no-loc(Razor):::PagesMovieContext**.</span></span>
* <span data-ttu-id="8be5e-565">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-565">Select **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/arp.png)

<span data-ttu-id="8be5e-567">Il *:::no-loc(appsettings.json):::* file viene aggiornato con la stringa di connessione utilizzata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="8be5e-567">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8be5e-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8be5e-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace :::no-loc(Razor):::PagesMovie.Pages_Movies rather than namespace :::no-loc(Razor):::PagesMovie.Pages.Movies
-->

* <span data-ttu-id="8be5e-569">Aprire una finestra di comando nella directory del progetto che contiene i file *Program.cs* , *Startup.cs* e *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="8be5e-569">Open a command window in the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="8be5e-570">**Per Windows** : eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="8be5e-570">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="8be5e-571">**Per MacOS e Linux** : eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="8be5e-571">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="8be5e-572">La tabella seguente illustra in dettaglio le opzioni del generatore di codice ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="8be5e-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="8be5e-573">Opzione</span><span class="sxs-lookup"><span data-stu-id="8be5e-573">Option</span></span>               | <span data-ttu-id="8be5e-574">Descrizione</span><span class="sxs-lookup"><span data-stu-id="8be5e-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="8be5e-575">Nome del modello.</span><span class="sxs-lookup"><span data-stu-id="8be5e-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="8be5e-576">Classe `DbContext` da usare.</span><span class="sxs-lookup"><span data-stu-id="8be5e-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="8be5e-577">Uso del layout predefinito.</span><span class="sxs-lookup"><span data-stu-id="8be5e-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="8be5e-578">Percorso relativo della cartella di output per creare le viste.</span><span class="sxs-lookup"><span data-stu-id="8be5e-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="8be5e-579">Aggiunge `_ValidationScriptsPartial` a Edit e :::no-loc(Create)::: pages</span><span class="sxs-lookup"><span data-stu-id="8be5e-579">Adds `_ValidationScriptsPartial` to Edit and :::no-loc(Create)::: pages</span></span> |

<span data-ttu-id="8be5e-580">Usare l' `-h` opzione per ottenere informazioni sul `aspnet-codegenerator razorpage` comando:</span><span class="sxs-lookup"><span data-stu-id="8be5e-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="8be5e-581">Per ulteriori informazioni, vedere [DotNet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="8be5e-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8be5e-582">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8be5e-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8be5e-583">:::no-loc(Create)::: una cartella *pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="8be5e-583">:::no-loc(Create)::: a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="8be5e-584">CTRL: fare clic sulla cartella *pagine* > **Aggiungi** > **nuova cartella**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="8be5e-585">Denominare la cartella *Movies*.</span><span class="sxs-lookup"><span data-stu-id="8be5e-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="8be5e-586">Control: fare clic sulla cartella *pages/Movies* > **aggiungere** un > **nuovo elemento con impalcatura**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/scaMac.png)

<span data-ttu-id="8be5e-588">Nella finestra di dialogo **Aggiungi nuova impalcatura** selezionare **:::no-loc(Razor)::: pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-588">In the **Add New Scaffolding** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="8be5e-590">Completare la finestra di dialogo **Aggiungi :::no-loc(Razor)::: pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="8be5e-590">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="8be5e-591">Nell'elenco a discesa **classe modello** selezionare o digitare **Movie**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="8be5e-592">Nella riga della **classe del contesto dati** Digitare select the **:::no-loc(Razor)::: PagesMovieContext** . verrà creata una nuova classe del contesto di database con lo spazio dei nomi corretto.</span><span class="sxs-lookup"><span data-stu-id="8be5e-592">In the **Data context class** row, type select the **:::no-loc(Razor):::PagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="8be5e-593">In questo caso sarà **:::no-loc(Razor)::: PagesMovie. Models. :::no-loc(Razor)::: PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-593">In this case it will be  **:::no-loc(Razor):::PagesMovie.Models.:::no-loc(Razor):::PagesMovieContext**.</span></span>
* <span data-ttu-id="8be5e-594">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-594">Select **Add**.</span></span>

![Immagine relativa alle istruzioni precedenti.](model/_static/arpMac.png)

<span data-ttu-id="8be5e-596">Il *:::no-loc(appsettings.json):::* file viene aggiornato con la stringa di connessione utilizzata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="8be5e-596">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="8be5e-597">Il processo di scaffolding crea e aggiorna i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="8be5e-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="8be5e-598">File creati</span><span class="sxs-lookup"><span data-stu-id="8be5e-598">Files created</span></span>

* <span data-ttu-id="8be5e-599">*Pagine/film* : :::no-loc(Create)::: , :::no-loc(Delete)::: , Details, Edit e :::no-loc(Index)::: .</span><span class="sxs-lookup"><span data-stu-id="8be5e-599">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>
* <span data-ttu-id="8be5e-600">*Data/ :::no-loc(Razor)::: PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="8be5e-600">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="8be5e-601">File aggiornato</span><span class="sxs-lookup"><span data-stu-id="8be5e-601">File updated</span></span>

* <span data-ttu-id="8be5e-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="8be5e-602">*Startup.cs*</span></span>

<span data-ttu-id="8be5e-603">I file creati e aggiornati sono illustrati nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="8be5e-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="8be5e-604">Migrazione iniziale</span><span class="sxs-lookup"><span data-stu-id="8be5e-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8be5e-605">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8be5e-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8be5e-606">In questa sezione viene usata la Console di Gestione pacchetti (PMC) per:</span><span class="sxs-lookup"><span data-stu-id="8be5e-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="8be5e-607">Aggiungere una migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="8be5e-607">Add an initial migration.</span></span>
* <span data-ttu-id="8be5e-608">Aggiornare il database con la migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="8be5e-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="8be5e-609">Nel menu **Strumenti** selezionare **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="8be5e-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu della Console di Gestione pacchetti](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="8be5e-611">Nella Console di Gestione pacchetti immettere i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8be5e-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="8be5e-612">Il comando `Add-Migration` genera un codice per creare lo schema del database iniziale.</span><span class="sxs-lookup"><span data-stu-id="8be5e-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="8be5e-613">Lo schema è basato sul modello specificato in `DbContext` , nel file *:::no-loc(Razor)::: PagesMovieContext.cs* .</span><span class="sxs-lookup"><span data-stu-id="8be5e-613">The schema is based on the model specified in the `DbContext`, in the *:::no-loc(Razor):::PagesMovieContext.cs* file.</span></span> <span data-ttu-id="8be5e-614">L' `Initial:::no-loc(Create):::` argomento viene usato per assegnare un nome alla migrazione.</span><span class="sxs-lookup"><span data-stu-id="8be5e-614">The `Initial:::no-loc(Create):::` argument is used to name the migration.</span></span> <span data-ttu-id="8be5e-615">È possibile usare qualsiasi nome, ma per convenzione viene usato un nome che descrive la migrazione.</span><span class="sxs-lookup"><span data-stu-id="8be5e-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="8be5e-616">Per altre informazioni, vedere <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="8be5e-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="8be5e-617">Il `Update-Database` comando esegue il `Up` metodo nel file *migrations/ \<time-stamp> _Initial :::no-loc(Create)::: . cs* .</span><span class="sxs-lookup"><span data-stu-id="8be5e-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_Initial:::no-loc(Create):::.cs* file.</span></span> <span data-ttu-id="8be5e-618">Il metodo `Up` crea il database.</span><span class="sxs-lookup"><span data-stu-id="8be5e-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8be5e-619">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8be5e-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="8be5e-620">Eseguire i seguenti comandi dell'interfaccia della riga di comando di .NET Core:</span><span class="sxs-lookup"><span data-stu-id="8be5e-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add Initial:::no-loc(Create):::
dotnet ef database update
```

---

<span data-ttu-id="8be5e-621">I comandi precedenti generano l'avviso seguente: "nessun tipo specificato per la colonna decimale ' Price ' nel tipo di entità' Movie '.</span><span class="sxs-lookup"><span data-stu-id="8be5e-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="8be5e-622">This will cause values to be silently truncated if they do not fit in the default precision and scale. (I valori saranno quindi automaticamente troncati se non rispettano la precisione e la scala predefinite).</span><span class="sxs-lookup"><span data-stu-id="8be5e-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="8be5e-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Specificare in modo esplicito il tipo di colonna di SQL Server che può supportare tutti i valori usando 'HasColumnType()')"</span><span class="sxs-lookup"><span data-stu-id="8be5e-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="8be5e-624">Ignorare l'avviso poiché verrà risolto in un passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="8be5e-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8be5e-625">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8be5e-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="8be5e-626">Esaminare il contesto registrato con l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="8be5e-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="8be5e-627">ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8be5e-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="8be5e-628">I servizi (ad esempio il contesto del contesto del database EF Core) vengono registrati con l'inserimento delle dipendenze durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="8be5e-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="8be5e-629">I componenti che richiedono questi servizi, ad esempio :::no-loc(Razor)::: le pagine, vengono forniti tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="8be5e-629">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="8be5e-630">Il codice del costruttore che ottiene un'istanza del contesto contextB del contesto di database viene illustrato più avanti nell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="8be5e-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="8be5e-631">Lo strumento di ponteggi crea automaticamente un contesto del contesto del database e lo registra con il contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8be5e-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="8be5e-632">Esaminare il metodo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8be5e-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="8be5e-633">La riga evidenziata è stata aggiunta dallo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="8be5e-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="8be5e-634">Le `:::no-loc(Razor):::PagesMovieContext` coordinate EF Core funzionalità, ad esempio :::no-loc(Create)::: , Read, Update e :::no-loc(Delete)::: , per il `Movie` modello.</span><span class="sxs-lookup"><span data-stu-id="8be5e-634">The `:::no-loc(Razor):::PagesMovieContext` coordinates EF Core functionality, such as :::no-loc(Create):::, Read, Update, and :::no-loc(Delete):::, for the `Movie` model.</span></span> <span data-ttu-id="8be5e-635">Il contesto dei dati (`:::no-loc(Razor):::PagesMovieContext`) è derivato da [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="8be5e-635">The data context (`:::no-loc(Razor):::PagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="8be5e-636">Il contesto dei dati specifica le entità incluse nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="8be5e-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="8be5e-637">Il codice precedente crea una [proprietà \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="8be5e-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="8be5e-638">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="8be5e-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="8be5e-639">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="8be5e-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="8be5e-640">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions).</span><span class="sxs-lookup"><span data-stu-id="8be5e-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="8be5e-641">Per lo sviluppo locale, il [sistema di configurazione](xref:fundamentals/configuration/index) legge la stringa di connessione dal *:::no-loc(appsettings.json):::* file.</span><span class="sxs-lookup"><span data-stu-id="8be5e-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8be5e-642">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8be5e-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="8be5e-643">Esaminare il metodo `Up`.</span><span class="sxs-lookup"><span data-stu-id="8be5e-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="8be5e-644">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="8be5e-644">Test the app</span></span>

* <span data-ttu-id="8be5e-645">Eseguire l'app e accodare `/Movies` all'URL nel browser (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="8be5e-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="8be5e-646">Se viene visualizzato l'errore:</span><span class="sxs-lookup"><span data-stu-id="8be5e-646">If you get the error:</span></span>

```console
SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="8be5e-647">Non è stato eseguita la [migrazione](#pmc).</span><span class="sxs-lookup"><span data-stu-id="8be5e-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="8be5e-648">Testare il **:::no-loc(Create):::** collegamento.</span><span class="sxs-lookup"><span data-stu-id="8be5e-648">Test the **:::no-loc(Create):::** link.</span></span>

  ![::: NO-LOC (Create)::: page](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="8be5e-650">Potrebbe non essere possibile immettere virgole decimali nel campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="8be5e-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="8be5e-651">Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese (Stati Uniti), è necessario localizzare l'app.</span><span class="sxs-lookup"><span data-stu-id="8be5e-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="8be5e-652">Per istruzioni sulla globalizzazione, vedere [questo problema su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="8be5e-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="8be5e-653">Testare la **modifica** , **i dettagli** e i **:::no-loc(Delete):::** collegamenti.</span><span class="sxs-lookup"><span data-stu-id="8be5e-653">Test the **Edit** , **Details** , and **:::no-loc(Delete):::** links.</span></span>

<span data-ttu-id="8be5e-654">L'esercitazione successiva illustra i file creati tramite scaffolding.</span><span class="sxs-lookup"><span data-stu-id="8be5e-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8be5e-655">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="8be5e-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="8be5e-656">[Precedente: introduzione](xref:tutorials/razor-pages/razor-pages-start) 
>  Passaggio [successivo: impalcatura :::no-loc(Razor)::: Pagine](xref:tutorials/razor-pages/page) di</span><span class="sxs-lookup"><span data-stu-id="8be5e-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
