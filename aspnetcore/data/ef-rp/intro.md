---
title: 'Razor Pages con Entity Framework Core in ASP.NET Core: esercitazione 1 di 8'
author: rick-anderson
description: Viene illustrato come creare un'app Razor Pages con Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: data/ef-rp/intro
ms.openlocfilehash: 07faf5e596e7ea8b134d13caa0259c1e9d74ff1b
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661621"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="c9cc5-103">Razor Pages con Entity Framework Core in ASP.NET Core: esercitazione 1 di 8</span><span class="sxs-lookup"><span data-stu-id="c9cc5-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="c9cc5-104">Di [Tom Dykstra](https://github.com/tdykstra) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c9cc5-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c9cc5-105">Questa è la prima di una serie di esercitazioni che illustrano come usare Entity Framework (EF) Core in [un'app di ASP.NET Core Razor Pages.](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="c9cc5-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="c9cc5-106">Con queste esercitazioni viene creato un sito Web per una fittizia Contoso University.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="c9cc5-107">Il sito include funzionalità, come ad esempio l'ammissione di studenti, la creazione di corsi e le assegnazioni degli insegnati.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="c9cc5-108">L'esercitazione usa il primo approccio al codice.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="c9cc5-109">Per informazioni su come seguire questa esercitazione utilizzando prima l'approccio del database, vedere [questo problema di Github](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="c9cc5-110">Scaricare o visualizzare l'app completata.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="c9cc5-111">[Istruzioni per il download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c9cc5-112">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="c9cc5-112">Prerequisites</span></span>

* <span data-ttu-id="c9cc5-113">Se non si ha familiarità con Razor Pages, vedere la serie di esercitazioni [Introduzione a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) prima di iniziare.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c9cc5-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9cc5-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c9cc5-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c9cc5-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="c9cc5-116">Motori di database</span><span class="sxs-lookup"><span data-stu-id="c9cc5-116">Database engines</span></span>

<span data-ttu-id="c9cc5-117">Le istruzioni per Visual Studio usano [SQL Server Local DB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), una versione di SQL Server Express eseguita solo in Windows.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="c9cc5-118">Le istruzioni per Visual Studio Code usano [SQLite](https://www.sqlite.org/), un motore di database multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="c9cc5-119">Se si sceglie di usare SQLite, scaricare e installare uno strumento di terze parti per la gestione e la visualizzazione di un database SQLite, ad esempio [DB Browser per SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="c9cc5-120">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="c9cc5-120">Troubleshooting</span></span>

<span data-ttu-id="c9cc5-121">Se si verifica un problema che non è possibile risolvere, confrontare il codice con il [progetto completato](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="c9cc5-122">Un buon metodo per ottenere assistenza è quello di pubblicare una domanda in StackOverflow.com con il [tag ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o il [tag EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="c9cc5-123">App di esempio</span><span class="sxs-lookup"><span data-stu-id="c9cc5-123">The sample app</span></span>

<span data-ttu-id="c9cc5-124">L'applicazione compilata in queste esercitazioni è il sito Web di base di un'università.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="c9cc5-125">Gli utenti possono visualizzare e aggiornare le informazioni che riguardano studenti, corsi e insegnanti.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="c9cc5-126">Di seguito sono disponibili alcune schermate dell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-126">Here are a few of the screens created in the tutorial.</span></span>

![Pagina Student Index (Indice degli studenti)](intro/_static/students-index30.png)

![Pagina Students Edit (Modifica studenti)](intro/_static/student-edit30.png)

<span data-ttu-id="c9cc5-129">Lo stile dell'interfaccia utente del sito è basato sui modelli di progetto predefiniti.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="c9cc5-130">L'obiettivo dell'esercitazione è quello di usare EF Core e non di personalizzare l'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-130">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="c9cc5-131">Seguire il collegamento nella parte superiore della pagina per ottenere il codice sorgente per il progetto completato.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-131">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="c9cc5-132">La cartella *cu30* contiene il codice per la versione di ASP.NET Core 3.0 dell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-132">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="c9cc5-133">I file che riflettono lo stato del codice per le esercitazioni 1-7 si trovano nella cartella *cu30snapshots*.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-133">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c9cc5-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9cc5-134">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c9cc5-135">Per eseguire l'app dopo il download del progetto completato:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-135">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="c9cc5-136">Eliminare i tre file e la cartella con *SQLite* nel nome.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-136">Delete three files and one folder that have *SQLite* in the name.</span></span>
* <span data-ttu-id="c9cc5-137">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-137">Build the project.</span></span>
* <span data-ttu-id="c9cc5-138">Nella console di Gestione pacchetti eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-138">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="c9cc5-139">Eseguire il progetto per il seeding del database.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-139">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c9cc5-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c9cc5-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c9cc5-141">Per eseguire l'app dopo il download del progetto completato:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-141">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="c9cc5-142">Eliminare *ContosoUniversity.csproj* e rinominare *ContosoUniversitySQLite.csproj* in *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-142">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="c9cc5-143">Eliminare *Startup.cs* e rinominare *StartupSQLite.cs* in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-143">Delete *Startup.cs*, and rename *StartupSQLite.cs* to *Startup.cs*.</span></span>
* <span data-ttu-id="c9cc5-144">Eliminare *appSettings.json* e rinominare *appSettingsSQLite.json* in *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-144">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="c9cc5-145">Eliminare la cartella *Migrations* e rinominare *MigrationsSQL* in *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-145">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="c9cc5-146">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-146">Build the project.</span></span>
* <span data-ttu-id="c9cc5-147">Al prompt dei comandi nella cartella del progetto eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-147">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* <span data-ttu-id="c9cc5-148">Nello strumento SQLite eseguire l'istruzione SQL seguente:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-148">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="c9cc5-149">Eseguire il progetto per il seeding del database.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-149">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="c9cc5-150">Creare il progetto di app Web</span><span class="sxs-lookup"><span data-stu-id="c9cc5-150">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c9cc5-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9cc5-151">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c9cc5-152">Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="c9cc5-153">Selezionare **Applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="c9cc5-154">Denominare il progetto *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-154">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="c9cc5-155">È importante usare questo nome esatto, incluse le maiuscole, in modo che gli spazi dei nomi corrispondano quando il codice viene copiato e incollato.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-155">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="c9cc5-156">Selezionare **.NET Core** e **ASP.NET Core 3.0** nell'elenco a discesa, quindi selezionare **Applicazione Web**.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-156">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c9cc5-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c9cc5-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c9cc5-158">In un terminale passare alla cartella in cui deve essere creata la cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-158">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="c9cc5-159">Eseguire i comandi seguenti per creare un progetto Razor Pages e `cd` nella nuova cartella di progetto:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-159">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="c9cc5-160">Impostare lo stile del sito</span><span class="sxs-lookup"><span data-stu-id="c9cc5-160">Set up the site style</span></span>

<span data-ttu-id="c9cc5-161">Per impostare l'intestazione, il piè di pagina e il menu del sito, aggiornare *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-161">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="c9cc5-162">Modificare tutte le occorrenze di "ContosoUniversity" in "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="c9cc5-162">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="c9cc5-163">Le occorrenze sono tre.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-163">There are three occurrences.</span></span>

* <span data-ttu-id="c9cc5-164">Eliminare le voci di menu **Home** e **Privacy**, quindi aggiungere le voci per **About** (Informazioni su), **Students** (Studenti), **Courses** (Corsi), **Instructors** (Insegnanti) e **Departments** (Dipartimenti).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-164">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="c9cc5-165">Le modifiche sono evidenziate.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-165">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="c9cc5-166">In *Pages/Index.cshtml* sostituire il contenuto del file con il codice seguente. In questo modo il testo su ASP.NET sarà sostituito dal testo relativo a questa app:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-166">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="c9cc5-167">Eseguire l'app per verificare che venga visualizzata la home page.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-167">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="c9cc5-168">Modello di dati</span><span class="sxs-lookup"><span data-stu-id="c9cc5-168">The data model</span></span>

<span data-ttu-id="c9cc5-169">Nelle sezioni seguenti viene descritto come creare un modello di dati:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-169">The following sections create a data model:</span></span>

![Diagramma modello di dati Course/Enrollment/Student (Corso/Iscrizione/Studente)](intro/_static/data-model-diagram.png)

<span data-ttu-id="c9cc5-171">Uno studente può iscriversi a un numero qualsiasi di corsi e un corso può avere un numero qualsiasi di studenti iscritti.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-171">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="c9cc5-172">Entità Student (Studente)</span><span class="sxs-lookup"><span data-stu-id="c9cc5-172">The Student entity</span></span>

![Diagramma entità Student (Studente)](intro/_static/student-entity.png)

* <span data-ttu-id="c9cc5-174">Creare una cartella *Models* nella cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-174">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="c9cc5-175">Creare *Models/Student.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-175">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="c9cc5-176">La proprietà `ID` diventa la colonna chiave primaria della tabella del database che corrisponde a questa classe.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-176">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="c9cc5-177">Per impostazione predefinita, Entity Framework Core interpreta una proprietà denominata `ID` o `classnameID` come chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-177">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="c9cc5-178">Il nome alternativo riconosciuto automaticamente per la chiave primaria della classe `Student` è quindi `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-178">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="c9cc5-179">Per ulteriori informazioni, vedere [EF Core - Chiavi](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-179">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="c9cc5-180">La proprietà `Enrollments` rappresenta una [proprietà di navigazione](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-180">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="c9cc5-181">Le proprietà di navigazione contengono altre entità correlate a questa entità.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-181">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="c9cc5-182">In questo caso la proprietà `Enrollments` di un'entità `Student` contiene tutte le entità `Enrollment` correlate a tale studente.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-182">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="c9cc5-183">Ad esempio, se una riga Student nel database presenta due righe Enrollment correlate, la proprietà di navigazione `Enrollments` contiene questi due entità Enrollment.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-183">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="c9cc5-184">Nel database, una riga Enrollment è correlata a una riga Student se la relativa colonna StudentID contiene il valore ID dello studente.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-184">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="c9cc5-185">Si supponga, ad esempio, che una riga Student abbia ID=1.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-185">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="c9cc5-186">Le righe Enrollment correlate avranno StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-186">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="c9cc5-187">StudentID è una *chiave esterna* nella tabella Enrollment.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-187">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="c9cc5-188">La proprietà `Enrollments` è definita come `ICollection<Enrollment>` perché potrebbero essere presenti più entità Enrollment correlate.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-188">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="c9cc5-189">È possibile usare altri tipi di raccolta, ad esempio `List<Enrollment>` o `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-189">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="c9cc5-190">Se si specifica `ICollection<Enrollment>`, per impostazione predefinita Entity Framework Core crea una raccolta `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-190">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="c9cc5-191">Entità Enrollment (Iscrizione)</span><span class="sxs-lookup"><span data-stu-id="c9cc5-191">The Enrollment entity</span></span>

![Diagramma entità Enrollment (Iscrizione)](intro/_static/enrollment-entity.png)

<span data-ttu-id="c9cc5-193">Creare *Models/Enrollment.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-193">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="c9cc5-194">La proprietà `EnrollmentID` è la chiave primaria. Questa entità usa il modello `classnameID` anziché `ID` da solo.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-194">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="c9cc5-195">Per un modello di dati di produzione, scegliere un modello e usarlo in modo coerente.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-195">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="c9cc5-196">Questa esercitazione usa entrambi solo per illustrare che entrambi funzionano.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-196">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="c9cc5-197">L'uso di `ID` senza `classname` rende più semplice l'implementazione di alcuni tipi di modifiche al modello di dati.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-197">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="c9cc5-198">La proprietà `Grade` è un oggetto`enum`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-198">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="c9cc5-199">Il punto interrogativo dopo la dichiarazione del tipo `Grade` indica che la proprietà `Grade`[ammette i valori Null](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-199">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="c9cc5-200">Un voto con valore Null è diverso da un voto con valore zero. Null significa che un voto è sconosciuto o non è stato ancora assegnato.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-200">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="c9cc5-201">La proprietà `StudentID` è una chiave esterna e la proprietà di navigazione corrispondente è `Student`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-201">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="c9cc5-202">Un'entità `Enrollment` è associata a un'entità `Student`, pertanto la proprietà contiene un'unica entità `Student`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-202">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="c9cc5-203">La proprietà `CourseID` è una chiave esterna e la proprietà di navigazione corrispondente è `Course`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-203">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="c9cc5-204">Un'entità `Enrollment` è associata a un'entità `Course`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-204">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="c9cc5-205">Entity Framework Core interpreta una proprietà come chiave esterna se è denominata `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-205">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="c9cc5-206">Ad esempio, `StudentID` è la chiave esterna per la proprietà di navigazione `Student`, dato che la chiave primaria `Student` dell'entità è `ID`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-206">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="c9cc5-207">Le proprietà di chiave esterna possono anche essere denominate `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-207">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="c9cc5-208">Ad esempio, `CourseID` poiché la chiave primaria `Course` dell'entità è `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-208">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="c9cc5-209">Entità Course (Corso)</span><span class="sxs-lookup"><span data-stu-id="c9cc5-209">The Course entity</span></span>

![Diagramma entità Course (Corso)](intro/_static/course-entity.png)

<span data-ttu-id="c9cc5-211">Creare *Models/Course.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-211">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="c9cc5-212">La proprietà `Enrollments` rappresenta una proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-212">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="c9cc5-213">È possibile correlare un'entità `Course` a un numero qualsiasi di entità `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-213">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="c9cc5-214">L'attributo `DatabaseGenerated` consente all'app di specificare la chiave primaria anziché chiedere al database di generarla.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-214">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="c9cc5-215">Compilare il progetto per verificare che non siano presenti errori del compilatore.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-215">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="c9cc5-216">Scaffolding delle pagine Student</span><span class="sxs-lookup"><span data-stu-id="c9cc5-216">Scaffold Student pages</span></span>

<span data-ttu-id="c9cc5-217">In questa sezione si userà lo strumento di scaffolding di ASP.NET Core per generare:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-217">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="c9cc5-218">Una classe *contesto* di EF Core.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-218">An EF Core *context* class.</span></span> <span data-ttu-id="c9cc5-219">Il contesto è la classe principale che coordina le funzionalità di Entity Framework per un determinato modello di dati.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-219">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="c9cc5-220">Deriva dalla classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-220">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="c9cc5-221">Pagine Razor che gestiscono operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) per l'entità `Student`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-221">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c9cc5-222">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9cc5-222">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c9cc5-223">Creare una cartella *Students* nella cartella *Pages*.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-223">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="c9cc5-224">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Pages/Students* e quindi scegliere **Aggiungi** > **Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-224">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="c9cc5-225">Nella finestra di dialogo **Aggiungi scaffold** selezionare **Pagine Razor utilizzando Entity Framework (CRUD)** > **ADD**.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-225">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="c9cc5-226">Nella finestra di dialogo **Pagine Razor che usano Entity Framework (CRUD)**:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-226">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="c9cc5-227">Nell'elenco a discesa **Classe modello** selezionare **Student (ContosoUniversity.Models)**.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-227">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="c9cc5-228">Nella riga **Classe contesto di dati** selezionare il segno più **+**.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-228">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="c9cc5-229">Modificare il nome del contesto di dati da *ContosoUniversity.Models.ContosoUniversityContext* a *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-229">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="c9cc5-230">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-230">Select **Add**.</span></span>

<span data-ttu-id="c9cc5-231">Vengono installati automaticamente i pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-231">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="c9cc5-232">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c9cc5-232">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c9cc5-233">Eseguire i comandi seguenti dell'interfaccia della riga di comando di .NET Core per installare i pacchetti NuGet necessari:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-233">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  <span data-ttu-id="c9cc5-234">Per lo scaffolding è necessario il pacchetto Microsoft.VisualStudio.Web.CodeGeneration.Design.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-234">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="c9cc5-235">Sebbene l'app non usi SQL Server, lo strumento di scaffolding richiede il pacchetto di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-235">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="c9cc5-236">Creare una cartella *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-236">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="c9cc5-237">Eseguire il comando seguente per installare lo [strumento di scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-237">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="c9cc5-238">Eseguire il comando seguente per eseguire lo scaffolding delle pagine Student.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-238">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="c9cc5-239">**In Windows**</span><span class="sxs-lookup"><span data-stu-id="c9cc5-239">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="c9cc5-240">**In macOS o Linux**</span><span class="sxs-lookup"><span data-stu-id="c9cc5-240">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="c9cc5-241">Se si riscontra un problema con il passaggio precedente, compilare il progetto e riprovare a eseguire il passaggio di scaffolding.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-241">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="c9cc5-242">Il processo di scaffolding:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-242">The scaffolding process:</span></span>

* <span data-ttu-id="c9cc5-243">Crea pagine Razor nella cartella *Pages/Students*:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-243">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="c9cc5-244">*Create.cshtml* e *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="c9cc5-244">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="c9cc5-245">*Delete.cshtml* e *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="c9cc5-245">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="c9cc5-246">*Details.cshtml* e *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="c9cc5-246">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="c9cc5-247">*Edit.cshtml* e *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="c9cc5-247">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="c9cc5-248">*Index.cshtml* e *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="c9cc5-248">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="c9cc5-249">Crea *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-249">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="c9cc5-250">Aggiunge il contesto all'inserimento delle dipendenze in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-250">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="c9cc5-251">Aggiunge la stringa di connessione al database ad *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-251">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="c9cc5-252">Stringa di connessione al database</span><span class="sxs-lookup"><span data-stu-id="c9cc5-252">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c9cc5-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9cc5-253">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c9cc5-254">La stringa di connessione specifica [SQL Server Local DB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-254">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="c9cc5-255">Local DB è una versione leggera del motore di database di SQL Server Express appositamente pensato per lo sviluppo di app e non per la produzione.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-255">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="c9cc5-256">Per impostazione predefinita, Local DB crea file con estensione *mdf* nella directory `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-256">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c9cc5-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c9cc5-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c9cc5-258">Modificare la stringa di connessione in modo che punti a un file di database SQLite denominato *CU.db*:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-258">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="c9cc5-259">Aggiornare la classe del contesto di database</span><span class="sxs-lookup"><span data-stu-id="c9cc5-259">Update the database context class</span></span>

<span data-ttu-id="c9cc5-260">La classe principale che coordina le funzionalità di EF Core per un determinato modello di dati è la classe del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-260">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="c9cc5-261">Il contesto è derivato da [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-261">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="c9cc5-262">Il contesto specifica le entità incluse nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-262">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="c9cc5-263">In questo progetto la classe è denominata `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-263">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="c9cc5-264">Aggiornare *SchoolContext.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-264">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="c9cc5-265">Il codice evidenziato crea una proprietà [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per ogni set di entità.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-265">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="c9cc5-266">Nella terminologia di Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-266">In EF Core terminology:</span></span>

* <span data-ttu-id="c9cc5-267">Un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-267">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="c9cc5-268">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-268">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="c9cc5-269">Dato che un set di entità contiene più entità, le proprietà DBSet devono essere nomi plurali.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-269">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="c9cc5-270">Dato che lo strumento di scaffolding ha creato un DBSet `Student`, questo passaggio lo modifica nel plurale `Students`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-270">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="c9cc5-271">Per fare in modo che il codice Razor Pages riconosca il nuovo nome del DBSet, sostituire globalmente `_context.Student` con `_context.Students` nell'intero progetto.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-271">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="c9cc5-272">Sono presenti 8 occorrenze.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-272">There are 8 occurrences.</span></span>

<span data-ttu-id="c9cc5-273">Compilare il progetto per verificare che non siano presenti errori di compilazione.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-273">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="c9cc5-274">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="c9cc5-274">Startup.cs</span></span>

<span data-ttu-id="c9cc5-275">ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-275">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c9cc5-276">I servizi, ad esempio il contesto di database di EF Core, vengono registrati con l'inserimento delle dipendenze durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-276">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="c9cc5-277">Questi servizi vengono quindi offerti ai componenti per cui sono necessari (ad esempio Razor Pages) tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-277">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="c9cc5-278">Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-278">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="c9cc5-279">Lo strumento di scaffolding ha registrato automaticamente la classe di contesto nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-279">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c9cc5-280">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9cc5-280">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c9cc5-281">In `ConfigureServices` le righe evidenziate sono state aggiunte dallo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-281">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c9cc5-282">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c9cc5-282">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c9cc5-283">In `ConfigureServices` assicurarsi che il codice aggiunto dallo scaffolder chiami `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-283">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="c9cc5-284">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-284">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="c9cc5-285">Per lo sviluppo locale, il [sistema di configurazione di ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-285">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="c9cc5-286">Creare il database</span><span class="sxs-lookup"><span data-stu-id="c9cc5-286">Create the database</span></span>

<span data-ttu-id="c9cc5-287">Aggiornare *Program.cs* per creare il database se non esiste:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-287">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="c9cc5-288">Il metodo [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) non esegue alcuna azione se esiste un database per il contesto.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-288">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="c9cc5-289">Se non esiste alcun database, vengono creati il database e lo schema.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-289">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="c9cc5-290">`EnsureCreated` abilita il flusso di lavoro seguente per la gestione delle modifiche al modello di dati:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-290">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="c9cc5-291">Eliminare il database.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-291">Delete the database.</span></span> <span data-ttu-id="c9cc5-292">Tutti i dati esistenti andranno perduti.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-292">Any existing data is lost.</span></span>
* <span data-ttu-id="c9cc5-293">Modificare il modello di dati.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-293">Change the data model.</span></span> <span data-ttu-id="c9cc5-294">Ad esempio, aggiungere un campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-294">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="c9cc5-295">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-295">Run the app.</span></span>
* <span data-ttu-id="c9cc5-296">`EnsureCreated` crea un database con il nuovo schema.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-296">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="c9cc5-297">Questo flusso di lavoro funziona correttamente nelle fasi iniziali dello sviluppo quando lo schema è in rapida evoluzione, purché non sia necessario conservare i dati.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-297">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="c9cc5-298">La situazione è diversa quando è necessario mantenere i dati immessi nel database.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-298">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="c9cc5-299">In tal caso, usare le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-299">When that is the case, use migrations.</span></span>

<span data-ttu-id="c9cc5-300">Più avanti nella serie di esercitazioni si vedrà come eliminare il database creato da `EnsureCreated` e usare invece le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-300">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="c9cc5-301">Non è possibile aggiornare un database creato da `EnsureCreated` usando le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-301">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="c9cc5-302">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="c9cc5-302">Test the app</span></span>

* <span data-ttu-id="c9cc5-303">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-303">Run the app.</span></span>
* <span data-ttu-id="c9cc5-304">Selezionare il collegamento **Students** (Studenti) e quindi **Crea nuovo**.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-304">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="c9cc5-305">Eseguire il test dei collegamenti Edit (Modifica), Details (Dettagli) e Delete (Elimina).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-305">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="c9cc5-306">Specificare il valore di inizializzazione del database</span><span class="sxs-lookup"><span data-stu-id="c9cc5-306">Seed the database</span></span>

<span data-ttu-id="c9cc5-307">Il metodo `EnsureCreated` crea un database vuoto.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-307">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="c9cc5-308">In questa sezione viene aggiunto il codice che popola il database con dati di test.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-308">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="c9cc5-309">Creare *Data/DbInitializer.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-309">Create *Data/DbInitializer.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="c9cc5-310">Il codice controlla se esistono studenti nel database.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-310">The code checks if there are any students in the database.</span></span> <span data-ttu-id="c9cc5-311">Se non sono presenti studenti, aggiunge i dati di test al database.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-311">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="c9cc5-312">I dati di test vengono creati in matrici anziché in raccolte `List<T>` per ottimizzare le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-312">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="c9cc5-313">In *Program.cs* sostituire la chiamata di `EnsureCreated` con una chiamata di `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-313">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="c9cc5-314">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9cc5-314">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c9cc5-315">Arrestare l'app se è in esecuzione ed eseguire il comando seguente nella **console di Gestione pacchetti**:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-315">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="c9cc5-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c9cc5-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c9cc5-317">Arrestare l'app se è in esecuzione ed eliminare il file *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-317">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="c9cc5-318">Riavviare l'app.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-318">Restart the app.</span></span>

* <span data-ttu-id="c9cc5-319">Selezionare la pagina Students per visualizzare i dati di seeding.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-319">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="c9cc5-320">Visualizzare il database</span><span class="sxs-lookup"><span data-stu-id="c9cc5-320">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c9cc5-321">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9cc5-321">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c9cc5-322">Aprire **Esplora oggetti di SQL Server** dal menu **Visualizza** in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-322">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="c9cc5-323">In Esplora oggetti di SQL Server selezionare **(localdb)\MSSQLLocalDB > Database > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-323">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="c9cc5-324">Il nome del database viene generato dal nome del contesto specificato in precedenza con l'aggiunta di un trattino e un GUID.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-324">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="c9cc5-325">Espandere il nodo **Tabelle**.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-325">Expand the **Tables** node.</span></span>
* <span data-ttu-id="c9cc5-326">Fare clic con il pulsante destro del mouse sulla tabella **Student** (Studente) e fare clic su **Visualizza dati** per visualizzare le colonne create e le righe inserite nella tabella.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-326">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="c9cc5-327">Fare clic con il pulsante destro del mouse sulla tabella **Student** e scegliere **Visualizza codice** per vedere il mapping tra il modello `Student` e lo schema della tabella `Student`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-327">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c9cc5-328">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c9cc5-328">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c9cc5-329">Usare lo strumento SQLite per visualizzare lo schema del database e i dati di seeding.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-329">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="c9cc5-330">Il file di database è denominato*CU.db* e si trova nella cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-330">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="c9cc5-331">Codice asincrono</span><span class="sxs-lookup"><span data-stu-id="c9cc5-331">Asynchronous code</span></span>

<span data-ttu-id="c9cc5-332">La programmazione asincrona è la modalità predefinita per ASP.NET Core ed Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-332">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="c9cc5-333">Per un server Web è disponibile un numero limitato di thread e in situazioni di carico elevato tutti i thread disponibili potrebbero essere in uso.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-333">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="c9cc5-334">In queste circostanze il server non può elaborare nuove richieste finché i thread non saranno liberi.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-334">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="c9cc5-335">Con il codice sincrono, può succedere che molti thread siano vincolati nonostante in quel momento non stiano eseguendo alcuna operazione. Rimangono tuttavia in attesa che l'operazione I/O sia completata.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-335">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="c9cc5-336">Con il codice asincrono, se un processo è in attesa del completamento dell'operazione I/O, il thread viene liberato e il server lo può usare per l'elaborazione di altre richieste.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-336">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="c9cc5-337">Di conseguenza, il codice asincrono consente un uso più efficiente delle risorse del server e il server può gestire una maggiore quantità di traffico senza ritardi.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-337">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="c9cc5-338">Il codice asincrono comporta un minimo sovraccarico in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-338">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="c9cc5-339">In caso di traffico ridotto, il calo delle prestazioni è irrilevante, mentre nelle situazioni di traffico elevato, è essenziale ottimizzare le prestazioni potenziali.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-339">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="c9cc5-340">Nel codice seguente la parola chiave [async](/dotnet/csharp/language-reference/keywords/async), il valore restituito `Task<T>`, la parola chiave `await` e il metodo `ToListAsync` consentono di eseguire il codice in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-340">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="c9cc5-341">La parola chiave `async` indica al compilatore di eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-341">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="c9cc5-342">Generare callback per parti del corpo del metodo.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-342">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="c9cc5-343">Creare l'oggetto [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) restituito.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-343">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="c9cc5-344">Il tipo restituito `Task<T>` rappresenta il lavoro in corso.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-344">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="c9cc5-345">La parola chiave `await` indica al compilatore di suddividere il metodo in due parti.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-345">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="c9cc5-346">La prima parte termina con l'operazione avviata in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-346">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="c9cc5-347">La seconda parte viene inserita in un metodo di callback che viene chiamato al termine dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-347">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="c9cc5-348">`ToListAsync` è la versione asincrona del metodo di estensione `ToList`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-348">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="c9cc5-349">Alcuni aspetti da considerare quando si scrive codice asincrono usato da Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-349">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="c9cc5-350">Solo le istruzioni che generano query o comandi da inviare al database vengono eseguite in modo asincrono,</span><span class="sxs-lookup"><span data-stu-id="c9cc5-350">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="c9cc5-351">Sono incluse `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-351">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="c9cc5-352">Non sono incluse le istruzioni che modificano solo un oggetto `IQueryable`, ad esempio `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-352">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="c9cc5-353">Un contesto di Entity Framework Core non è thread-safe. Non tentare di eseguire più operazioni parallelamente.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-353">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="c9cc5-354">Per sfruttare i vantaggi del codice asincrono in termini di prestazioni, verificare che i pacchetti della libreria impiegati, ad esempio per il paging, usino la modalità asincrona per chiamare i metodi di EF Core che inviano query al database.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-354">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="c9cc5-355">Per altre informazioni sulla programmazione asincrona in .NET, vedere [Panoramica della programmazione asincrona](/dotnet/standard/async) e [Programmazione asincrona con async e await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-355">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="c9cc5-356">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="c9cc5-356">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="c9cc5-357">Esercitazione successiva</span><span class="sxs-lookup"><span data-stu-id="c9cc5-357">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c9cc5-358">L'app Web di esempio di Contoso University illustra come creare un'app ASP.NET Core di Razor Pages con Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-358">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="c9cc5-359">L'app di esempio è un sito Web per una fittizia Contoso University.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-359">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="c9cc5-360">Include funzionalità, come ad esempio l'ammissione di studenti, la creazione di corsi e le assegnazioni di insegnati.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-360">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="c9cc5-361">Questa pagina è il prima di una serie di esercitazioni che illustrano come compilare l'app di esempio di Contoso University.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-361">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="c9cc5-362">Scaricare o visualizzare l'app completata.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-362">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="c9cc5-363">[Istruzioni per il download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-363">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c9cc5-364">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="c9cc5-364">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c9cc5-365">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9cc5-365">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c9cc5-366">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c9cc5-366">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="c9cc5-367">Conoscenza di [Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-367">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="c9cc5-368">Prima di iniziare questa serie, i programmatori non esperti dovranno completare l'[introduzione a Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-368">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="c9cc5-369">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="c9cc5-369">Troubleshooting</span></span>

<span data-ttu-id="c9cc5-370">Se si verifica un problema che non si sa come risolvere, è generalmente possibile trovare la soluzione confrontando il codice con il [progetto completato](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-370">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="c9cc5-371">Un buon metodo per ottenere assistenza è quello di pubblicare una domanda in [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) per [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-371">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="c9cc5-372">App Web di Contoso University</span><span class="sxs-lookup"><span data-stu-id="c9cc5-372">The Contoso University web app</span></span>

<span data-ttu-id="c9cc5-373">L'applicazione compilata in queste esercitazioni è il sito Web di base di un'università.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-373">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="c9cc5-374">Gli utenti possono visualizzare e aggiornare le informazioni che riguardano studenti, corsi e insegnanti.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-374">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="c9cc5-375">Di seguito sono disponibili alcune schermate dell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-375">Here are a few of the screens created in the tutorial.</span></span>

![Pagina Student Index (Indice degli studenti)](intro/_static/students-index.png)

![Pagina Students Edit (Modifica studenti)](intro/_static/student-edit.png)

<span data-ttu-id="c9cc5-378">Lo stile dell'interfaccia utente del sito è simile a quanto è stato generato tramite i modelli predefiniti.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-378">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="c9cc5-379">L'esercitazione è incentrata su Entity Framework Core con Razor Pages e non sull'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-379">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-razor-pages-web-app"></a><span data-ttu-id="c9cc5-380">Creare l'app Web di Razor Pages ContosoUniversity</span><span class="sxs-lookup"><span data-stu-id="c9cc5-380">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c9cc5-381">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9cc5-381">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c9cc5-382">Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-382">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="c9cc5-383">Creare una nuova applicazione Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-383">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="c9cc5-384">Denominare il progetto **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-384">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="c9cc5-385">È importante denominare il progetto *ContosoUniversity* in modo che gli spazi dei nomi corrispondano quando il codice viene copiato/incollato.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-385">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="c9cc5-386">Selezionare **ASP.NET Core 2.1** nell'elenco a discesa, quindi selezionare **Applicazione Web**.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-386">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="c9cc5-387">Per le immagini dei passaggi precedenti, vedere [Creare un'app Web Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-387">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="c9cc5-388">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-388">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c9cc5-389">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c9cc5-389">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="c9cc5-390">Impostare lo stile del sito</span><span class="sxs-lookup"><span data-stu-id="c9cc5-390">Set up the site style</span></span>

<span data-ttu-id="c9cc5-391">Con alcune modifiche è possibile impostare il menu del sito, il layout e la home page.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-391">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="c9cc5-392">Aggiornare *Pages/Shared/_Layout.cshtml* con le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-392">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="c9cc5-393">Modificare tutte le occorrenze di "ContosoUniversity" in "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="c9cc5-393">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="c9cc5-394">Le occorrenze sono tre.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-394">There are three occurrences.</span></span>

* <span data-ttu-id="c9cc5-395">Aggiungere le voci di menu per **Students** (Studenti), **Courses** (Corsi), **Instructors** (Insegnanti) e **Departments** (Dipartimenti) ed eliminare la voce di menu **Contact** (Contatto).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-395">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="c9cc5-396">Le modifiche sono evidenziate.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-396">The changes are highlighted.</span></span> <span data-ttu-id="c9cc5-397">*Non* viene visualizzato l'intero markup.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-397">(All the markup is *not* displayed.)</span></span>

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="c9cc5-398">In *Pages/Index.cshtml* sostituire il contenuto del file con il codice seguente. In questo modo il testo su ASP.NET e MVC sarà sostituito dal testo relativo a questa app:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-398">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="c9cc5-399">Creare il modello di dati</span><span class="sxs-lookup"><span data-stu-id="c9cc5-399">Create the data model</span></span>

<span data-ttu-id="c9cc5-400">Creare le classi delle entità per l'app di Contoso University.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-400">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="c9cc5-401">Iniziare con le tre entità seguenti:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-401">Start with the following three entities:</span></span>

![Diagramma modello di dati Course/Enrollment/Student (Corso/Iscrizione/Studente)](intro/_static/data-model-diagram.png)

<span data-ttu-id="c9cc5-403">Esiste una relazione uno-a-molti tra le entità `Student` e `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-403">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="c9cc5-404">Esiste una relazione uno-a-molti tra le entità `Course` e `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-404">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="c9cc5-405">Uno studente può iscriversi a un numero qualsiasi di corsi.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-405">A student can enroll in any number of courses.</span></span> <span data-ttu-id="c9cc5-406">Un corso può avere un numero qualsiasi di studenti iscritti.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-406">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="c9cc5-407">Nelle sezioni seguenti viene creata una classe per ognuna di queste entità.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-407">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="c9cc5-408">Entità Student (Studente)</span><span class="sxs-lookup"><span data-stu-id="c9cc5-408">The Student entity</span></span>

![Diagramma entità Student (Studente)](intro/_static/student-entity.png)

<span data-ttu-id="c9cc5-410">Creare una cartella *Models* (Modelli).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-410">Create a *Models* folder.</span></span> <span data-ttu-id="c9cc5-411">Nella cartella *Models* (Modelli) creare un file di classe denominato *Student.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-411">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="c9cc5-412">La proprietà `ID` diventa la colonna di chiave primaria della tabella di database (DB) che corrisponde a questa classe.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-412">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="c9cc5-413">Per impostazione predefinita, Entity Framework Core interpreta una proprietà denominata `ID` o `classnameID` come chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-413">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="c9cc5-414">In `classnameID``classname` è il nome della classe.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-414">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="c9cc5-415">La chiave primaria alternativa riconosciuta automaticamente è `StudentID` nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-415">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="c9cc5-416">La proprietà `Enrollments` rappresenta una [proprietà di navigazione](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-416">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="c9cc5-417">Le proprietà di navigazione si collegano ad altre entità correlate a questa entità.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-417">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="c9cc5-418">In questo caso la proprietà `Enrollments` di `Student entity` contiene tutte le entità `Enrollment` correlate a `Student`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-418">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="c9cc5-419">Ad esempio, se una riga Student (Studente) nella database presenta due righe Enrollment (Iscrizione) correlate, la proprietà di navigazione `Enrollments` contiene questi due entità `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-419">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="c9cc5-420">Una riga `Enrollment` correlata è una riga che contiene il valore della chiave primaria dello studente nella colonna `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-420">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="c9cc5-421">Si supponga ad esempio che per lo studente con ID = 1 siano presenti due righe nella tabella `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-421">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="c9cc5-422">La tabella `Enrollment` contiene due righe con `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-422">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="c9cc5-423">`StudentID` è una chiave esterna nella tabella `Enrollment` che specifica lo studente nella tabella `Student`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-423">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="c9cc5-424">Se una proprietà di navigazione può contenere più entità, deve essere un tipo elenco, ad esempio `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-424">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="c9cc5-425">È possibile specificare `ICollection<T>` o un tipo, ad esempio `List<T>` o `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-425">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="c9cc5-426">Se si specifica `ICollection<T>`, per impostazione predefinita Entity Framework Core crea una raccolta `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-426">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="c9cc5-427">Le proprietà di navigazione che contengono più entità provengono da relazioni molti-a-molti e uno-a-molti.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-427">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="c9cc5-428">Entità Enrollment (Iscrizione)</span><span class="sxs-lookup"><span data-stu-id="c9cc5-428">The Enrollment entity</span></span>

![Diagramma entità Enrollment (Iscrizione)](intro/_static/enrollment-entity.png)

<span data-ttu-id="c9cc5-430">Nella cartella *Models* (Modelli) creare un file *Enrollment.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-430">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="c9cc5-431">La proprietà `EnrollmentID` è la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-431">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="c9cc5-432">Questa entità usa il criterio `classnameID` anziché `ID` come l'entità `Student`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-432">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="c9cc5-433">In genere gli sviluppatori scelgono un criterio che usano poi in tutto il modello di dati.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-433">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="c9cc5-434">In un'esercitazione successiva viene illustrato l'uso di ID senza classname. In questo modo si semplifica l'implementazione dell'ereditarietà nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-434">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="c9cc5-435">La proprietà `Grade` è un oggetto`enum`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-435">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="c9cc5-436">Il punto interrogativo dopo la dichiarazione del tipo `Grade` indica che la proprietà `Grade` ammette i valori Null.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-436">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="c9cc5-437">Un voto il cui valore è Null è diverso da un voto con valore zero. Null significa che un voto è sconosciuto o non è stato ancora assegnato.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-437">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="c9cc5-438">La proprietà `StudentID` è una chiave esterna e la proprietà di navigazione corrispondente è `Student`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-438">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="c9cc5-439">Un'entità `Enrollment` è associata a un'entità `Student`, pertanto la proprietà contiene un'unica entità `Student`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-439">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="c9cc5-440">L'entità `Student` si differenzia dalla proprietà di navigazione `Student.Enrollments` che contiene più entità `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-440">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="c9cc5-441">La proprietà `CourseID` è una chiave esterna e la proprietà di navigazione corrispondente è `Course`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-441">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="c9cc5-442">Un'entità `Enrollment` è associata a un'entità `Course`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-442">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="c9cc5-443">Entity Framework Core interpreta una proprietà come chiave esterna se è denominata `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-443">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="c9cc5-444">Ad esempio, `StudentID` per la proprietà di navigazione `Student`, poiché la chiave primaria `Student` dell'entità è `ID`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-444">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="c9cc5-445">Le proprietà di chiave esterna possono anche essere denominate `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-445">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="c9cc5-446">Ad esempio, `CourseID` poiché la chiave primaria `Course` dell'entità è `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-446">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="c9cc5-447">Entità Course (Corso)</span><span class="sxs-lookup"><span data-stu-id="c9cc5-447">The Course entity</span></span>

![Diagramma entità Course (Corso)](intro/_static/course-entity.png)

<span data-ttu-id="c9cc5-449">Nella cartella *Models* (Modelli) creare un file *Course.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-449">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="c9cc5-450">La proprietà `Enrollments` rappresenta una proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-450">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="c9cc5-451">È possibile correlare un'entità `Course` a un numero qualsiasi di entità `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-451">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="c9cc5-452">L'attributo `DatabaseGenerated` consente all'app di specificare la chiave primaria anziché chiedere al database di generarla.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-452">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="c9cc5-453">Eseguire lo scaffolding del modello Student (Studente)</span><span class="sxs-lookup"><span data-stu-id="c9cc5-453">Scaffold the student model</span></span>

<span data-ttu-id="c9cc5-454">In questa sezione viene eseguito lo scaffolding del modello Student (Studente).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-454">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="c9cc5-455">Lo strumento di scaffolding crea quindi le pagine per le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) per il modello Student (Studente).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-455">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="c9cc5-456">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-456">Build the project.</span></span>
* <span data-ttu-id="c9cc5-457">Creare la cartella *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-457">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c9cc5-458">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9cc5-458">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c9cc5-459">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Pages/Students* > **Aggiungi** > **Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-459">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="c9cc5-460">Nella finestra di dialogo **Aggiungi scaffold** selezionare **Pagine Razor utilizzando Entity Framework (CRUD)** > **ADD**.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-460">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="c9cc5-461">Completare la finestra di dialogo **Pagine Razor che usano Entity Framework (CRUD)**:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-461">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="c9cc5-462">Nell'elenco a discesa **Classe modello** selezionare **Student (ContosoUniversity.Models)**.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-462">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="c9cc5-463">Nella riga **Classe contesto di dati** selezionare il segno **+** (più) e modificare il nome generato in **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-463">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="c9cc5-464">Nell'elenco a discesa **Classe contesto di dati** selezionare **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-464">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="c9cc5-465">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-465">Select **Add**.</span></span>

![Finestra di dialogo CRUD](intro/_static/s1.png)

<span data-ttu-id="c9cc5-467">Vedere [Eseguire lo scaffolding del modello di filmato](xref:tutorials/razor-pages/model#scaffold-the-movie-model) se si riscontrano problemi con il passaggio precedente.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-467">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c9cc5-468">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c9cc5-468">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c9cc5-469">Eseguire i comandi seguenti per eseguire lo scaffolding del modello Student (Studente).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-469">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="c9cc5-470">Il processo di scaffolding ha creato e modificato i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-470">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="c9cc5-471">File creati</span><span class="sxs-lookup"><span data-stu-id="c9cc5-471">Files created</span></span>

* <span data-ttu-id="c9cc5-472">Pagine Create (Crea), Delete (Elimina), Details (Dettagli), Edit (Modifica) e Index (Indice) di *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-472">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="c9cc5-473">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="c9cc5-473">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="c9cc5-474">Aggiornamenti dei file</span><span class="sxs-lookup"><span data-stu-id="c9cc5-474">File updates</span></span>

* <span data-ttu-id="c9cc5-475">*Startup.cs*: le modifiche a questo file sono descritte in dettaglio nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-475">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="c9cc5-476">*appsettings.json*: è stata aggiunta la stringa di connessione usata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-476">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="c9cc5-477">Esaminare il contesto registrato con l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="c9cc5-477">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="c9cc5-478">ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-478">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c9cc5-479">I servizi, ad esempio il contesto di database di Entity Framework Core, vengono registrati tramite dependency injection durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-479">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="c9cc5-480">Questi servizi vengono quindi offerti ai componenti per cui sono necessari (ad esempio Razor Pages) tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-480">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="c9cc5-481">Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-481">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="c9cc5-482">Lo strumento di scaffolding ha creato automaticamente un contesto del database e lo ha registrato con il contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-482">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="c9cc5-483">Esaminare il metodo `ConfigureServices` in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-483">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="c9cc5-484">La riga evidenziata è stata aggiunta dallo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-484">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="c9cc5-485">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-485">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="c9cc5-486">Per lo sviluppo locale, il [sistema di configurazione di ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-486">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="c9cc5-487">Aggiornare il metodo Main</span><span class="sxs-lookup"><span data-stu-id="c9cc5-487">Update main</span></span>

<span data-ttu-id="c9cc5-488">In *Program.cs* modificare il metodo `Main` per eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-488">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="c9cc5-489">Ottenere un'istanza del contesto di database dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-489">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="c9cc5-490">Chiamare [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-490">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="c9cc5-491">Eliminare il contesto dopo che il metodo `EnsureCreated` è stato completato.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-491">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="c9cc5-492">Il codice seguente illustra il file *Program.cs* aggiornato.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-492">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="c9cc5-493">`EnsureCreated` assicura che esista il database per il contesto.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-493">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="c9cc5-494">Se esiste, non viene eseguita alcuna azione.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-494">If it exists, no action is taken.</span></span> <span data-ttu-id="c9cc5-495">Se non esiste, vengono creati il database e tutti i relativi schemi.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-495">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="c9cc5-496">`EnsureCreated` non usa le migrazioni per creare il database.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-496">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="c9cc5-497">Un database creato con `EnsureCreated` non potrà essere aggiornato successivamente usando le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-497">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="c9cc5-498">`EnsureCreated` viene chiamato all'avvio dell'app e consente il flusso di lavoro seguente:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-498">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="c9cc5-499">Eliminare il database.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-499">Delete the DB.</span></span>
* <span data-ttu-id="c9cc5-500">Modificare lo schema di database, ad esempio, aggiungere un campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-500">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="c9cc5-501">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-501">Run the app.</span></span>
* <span data-ttu-id="c9cc5-502">`EnsureCreated` crea un database con la colonna `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-502">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="c9cc5-503">`EnsureCreated` è utile nelle prime fasi di sviluppo quando lo schema è in rapida evoluzione.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-503">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="c9cc5-504">Più avanti nell'esercitazione il database verrà eliminato e si useranno le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-504">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="c9cc5-505">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="c9cc5-505">Test the app</span></span>

<span data-ttu-id="c9cc5-506">Eseguire l'app e accettare l'informativa sui cookie.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-506">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="c9cc5-507">Questa app non memorizza informazioni personali.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-507">This app doesn't keep personal information.</span></span> <span data-ttu-id="c9cc5-508">È possibile consultare l'informativa sui cookie in [Supporto per il Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-508">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="c9cc5-509">Selezionare il collegamento **Students** (Studenti) e quindi **Crea nuovo**.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-509">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="c9cc5-510">Eseguire il test dei collegamenti Edit (Modifica), Details (Dettagli) e Delete (Elimina).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-510">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="c9cc5-511">Esaminare il contesto di database SchoolContext</span><span class="sxs-lookup"><span data-stu-id="c9cc5-511">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="c9cc5-512">La classe principale che coordina le funzionalità di Entity Framework Core per un determinato modello di dati è la classe del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-512">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="c9cc5-513">Il contesto dei dati è derivato da [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-513">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="c9cc5-514">Il contesto dei dati specifica le entità incluse nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-514">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="c9cc5-515">In questo progetto la classe è denominata `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-515">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="c9cc5-516">Aggiornare *SchoolContext.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-516">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="c9cc5-517">Il codice evidenziato crea una proprietà [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per ogni set di entità.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-517">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="c9cc5-518">Nella terminologia di Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-518">In EF Core terminology:</span></span>

* <span data-ttu-id="c9cc5-519">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-519">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="c9cc5-520">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-520">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="c9cc5-521">`DbSet<Enrollment>` e `DbSet<Course>` potrebbero essere omessi.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-521">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="c9cc5-522">Core Entity Framework le include in modo implicito perché l'entità `Student` fa riferimento all'entità `Enrollment` e l'entità `Enrollment` fa riferimento all'entità `Course`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-522">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="c9cc5-523">Per questa esercitazione, mantenere `DbSet<Enrollment>` e `DbSet<Course>` in `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-523">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="c9cc5-524">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="c9cc5-524">SQL Server Express LocalDB</span></span>

<span data-ttu-id="c9cc5-525">La stringa di connessione specifica [SQL Server Local DB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-525">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="c9cc5-526">Local DB è una versione leggera del motore di database di SQL Server Express appositamente pensato per lo sviluppo di app e non per la produzione.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-526">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="c9cc5-527">Local DB viene avviato su richiesta ed eseguito in modalità utente; non richiede quindi una configurazione complessa.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-527">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="c9cc5-528">Per impostazione predefinita, Local DB crea file di database con estensione *mdf* nella directory `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-528">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="c9cc5-529">Aggiungere il codice per inizializzare il database con dati di test</span><span class="sxs-lookup"><span data-stu-id="c9cc5-529">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="c9cc5-530">Entity Framework Core crea un database vuoto.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-530">EF Core creates an empty DB.</span></span> <span data-ttu-id="c9cc5-531">In questa sezione viene scritto un metodo `Initialize` per popolare il database con i dati di test.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-531">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="c9cc5-532">Nella cartella *Data* (Dati) creare un nuovo file di classe denominato *DbInitializer.cs* e aggiungere il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-532">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="c9cc5-533">Nota: il codice `Models` precedente viene`namespace ContosoUniversity.Models`utilizzato `Data`per lo spazio dei nomi ( ) anziché .</span><span class="sxs-lookup"><span data-stu-id="c9cc5-533">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="c9cc5-534">`Models` è coerente con il codice generato dallo scaffolder.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-534">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="c9cc5-535">Per altre informazioni, vedere [questo problema relativo allo scaffolding in GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-535">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="c9cc5-536">Il codice controlla se esistono studenti nel database.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-536">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="c9cc5-537">Se il database non contiene studenti, il database viene inizializzato con i dati di test.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-537">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="c9cc5-538">I dati di test vengono caricati in matrici anziché in raccolte `List<T>` per ottimizzare le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-538">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="c9cc5-539">Il metodo `EnsureCreated` crea automaticamente il database per il contesto di database.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-539">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="c9cc5-540">Se il database esiste, `EnsureCreated` restituisce senza modificare il database.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-540">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="c9cc5-541">In *Program.cs* modificare il metodo `Main` per chiamare `Initialize`:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-541">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="c9cc5-542">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9cc5-542">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c9cc5-543">Arrestare l'app se è in esecuzione ed eseguire il comando seguente nella **console di Gestione pacchetti**:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-543">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="c9cc5-544">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c9cc5-544">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c9cc5-545">Arrestare l'app se è in esecuzione ed eliminare il file *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-545">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="c9cc5-546">Visualizzare il database</span><span class="sxs-lookup"><span data-stu-id="c9cc5-546">View the DB</span></span>

<span data-ttu-id="c9cc5-547">Il nome del database viene generato dal nome del contesto specificato in precedenza con l'aggiunta di un trattino e un GUID.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-547">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="c9cc5-548">Di conseguenza, il nome del database sarà "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="c9cc5-548">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="c9cc5-549">Il GUID sarà diverso per ogni utente.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-549">The GUID will be different for each user.</span></span>
<span data-ttu-id="c9cc5-550">Aprire **Esplora oggetti di SQL Server** dal menu **Visualizza** in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-550">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="c9cc5-551">In Esplora oggetti di SQL Server fare clic su **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-551">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="c9cc5-552">Espandere il nodo **Tabelle**.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-552">Expand the **Tables** node.</span></span>

<span data-ttu-id="c9cc5-553">Fare clic con il pulsante destro del mouse sulla tabella **Student** (Studente) e fare clic su **Visualizza dati** per visualizzare le colonne create e le righe inserite nella tabella.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-553">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="c9cc5-554">Codice asincrono</span><span class="sxs-lookup"><span data-stu-id="c9cc5-554">Asynchronous code</span></span>

<span data-ttu-id="c9cc5-555">La programmazione asincrona è la modalità predefinita per ASP.NET Core ed Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-555">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="c9cc5-556">Per un server Web è disponibile un numero limitato di thread e in situazioni di carico elevato tutti i thread disponibili potrebbero essere in uso.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-556">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="c9cc5-557">In queste circostanze il server non può elaborare nuove richieste finché i thread non saranno liberi.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-557">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="c9cc5-558">Con il codice sincrono, può succedere che molti thread siano vincolati nonostante in quel momento non stiano eseguendo alcuna operazione. Rimangono tuttavia in attesa che l'operazione I/O sia completata.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-558">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="c9cc5-559">Con il codice asincrono, se un processo è in attesa del completamento dell'operazione I/O, il thread viene liberato e il server lo può usare per l'elaborazione di altre richieste.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-559">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="c9cc5-560">Di conseguenza, il codice asincrono consente un uso più efficiente delle risorse e il server è abilitato per gestire una maggiore quantità di traffico senza ritardi.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-560">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="c9cc5-561">Il codice asincrono comporta un minimo sovraccarico in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-561">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="c9cc5-562">In caso di traffico ridotto, il calo delle prestazioni è irrilevante, mentre nelle situazioni di traffico elevato, è essenziale ottimizzare le prestazioni potenziali.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-562">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="c9cc5-563">Nel codice seguente la parola chiave [async](/dotnet/csharp/language-reference/keywords/async), il valore restituito `Task<T>`, la parola chiave `await` e il metodo `ToListAsync` consentono di eseguire il codice in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-563">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="c9cc5-564">La parola chiave `async` indica al compilatore di eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-564">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="c9cc5-565">Generare callback per parti del corpo del metodo.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-565">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="c9cc5-566">Creare automaticamente l' oggetto [Task](/dotnet/api/system.threading.tasks.task) restituito.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-566">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="c9cc5-567">Per altre informazioni, vedere [Tipo restituito Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-567">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="c9cc5-568">Il tipo restituito implicito `Task` rappresenta il lavoro in corso.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-568">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="c9cc5-569">La parola chiave `await` indica al compilatore di suddividere il metodo in due parti.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-569">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="c9cc5-570">La prima parte termina con l'operazione avviata in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-570">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="c9cc5-571">La seconda parte viene inserita in un metodo di callback che viene chiamato al termine dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-571">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="c9cc5-572">`ToListAsync` è la versione asincrona del metodo di estensione `ToList`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-572">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="c9cc5-573">Alcuni aspetti da considerare quando si scrive codice asincrono usato da Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="c9cc5-573">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="c9cc5-574">Solo le istruzioni che generano query o comandi da inviare al database vengono eseguite in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-574">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="c9cc5-575">Sono incluse `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-575">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="c9cc5-576">Non sono incluse le istruzioni che modificano solo un oggetto `IQueryable`, ad esempio `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-576">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="c9cc5-577">Un contesto di Entity Framework Core non è thread-safe. Non tentare di eseguire più operazioni parallelamente.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-577">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="c9cc5-578">Per sfruttare i vantaggi del codice asincrono in termini di prestazioni, verificare che i pacchetti della libreria impiegati, ad esempio per il paging, usino la modalità asincrona per chiamare i metodi di Entity Framework Core che generano query da inviare al database.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-578">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="c9cc5-579">Per altre informazioni sulla programmazione asincrona in .NET, vedere [Panoramica della programmazione asincrona](/dotnet/standard/async) e [Programmazione asincrona con async e await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="c9cc5-579">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="c9cc5-580">Nella prossima esercitazione, vengono esaminate le operazioni CRUD per creare, leggere, aggiornare, eliminare ed elencare.</span><span class="sxs-lookup"><span data-stu-id="c9cc5-580">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="c9cc5-581">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="c9cc5-581">Additional resources</span></span>

* [<span data-ttu-id="c9cc5-582">Versione YouTube dell'esercitazione</span><span class="sxs-lookup"><span data-stu-id="c9cc5-582">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="c9cc5-583">Avanti</span><span class="sxs-lookup"><span data-stu-id="c9cc5-583">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
