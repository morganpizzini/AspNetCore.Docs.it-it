---
title: ':::no-loc(Razor)::: Pagine con Entity Framework Core in ASP.NET Core-esercitazione 1 di 8'
author: rick-anderson
description: "Mostra come creare un'app per le :::no-loc(Razor)::: pagine usando Entity Framework Core"
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
no-loc:
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
uid: data/ef-rp/intro
ms.openlocfilehash: 74f65b916c2d5b7de61ec29f4259a51584ee5989
ms.sourcegitcommit: 33f631a4427b9a422755601ac9119953db0b4a3e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365418"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="94f1a-103">:::no-loc(Razor)::: Pagine con Entity Framework Core in ASP.NET Core-esercitazione 1 di 8</span><span class="sxs-lookup"><span data-stu-id="94f1a-103">:::no-loc(Razor)::: Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="94f1a-104">Di [Tom Dykstra](https://github.com/tdykstra) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="94f1a-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="94f1a-105">Questo è il primo di una serie di esercitazioni che illustrano come usare Entity Framework (EF) Core in un'app [ASP.NET Core :::no-loc(Razor)::: pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="94f1a-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="94f1a-106">Con queste esercitazioni viene creato un sito Web per una fittizia Contoso University.</span><span class="sxs-lookup"><span data-stu-id="94f1a-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="94f1a-107">Il sito include funzionalità, come ad esempio l'ammissione di studenti, la creazione di corsi e le assegnazioni degli insegnati.</span><span class="sxs-lookup"><span data-stu-id="94f1a-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="94f1a-108">Nell'esercitazione viene usato l'approccio Code First.</span><span class="sxs-lookup"><span data-stu-id="94f1a-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="94f1a-109">Per informazioni su come seguire questa esercitazione usando il primo approccio al database, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="94f1a-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="94f1a-110">Scaricare o visualizzare l'app completata.</span><span class="sxs-lookup"><span data-stu-id="94f1a-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="94f1a-111">[Istruzioni per il download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="94f1a-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="94f1a-112">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="94f1a-112">Prerequisites</span></span>

* <span data-ttu-id="94f1a-113">Se non si ha familiarità con :::no-loc(Razor)::: le pagine, vedere la serie di esercitazioni [introduttive sulle :::no-loc(Razor)::: pagine](xref:tutorials/razor-pages/razor-pages-start) prima di iniziare.</span><span class="sxs-lookup"><span data-stu-id="94f1a-113">If you're new to :::no-loc(Razor)::: Pages, go through the [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94f1a-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94f1a-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="94f1a-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94f1a-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="94f1a-116">Motori di database</span><span class="sxs-lookup"><span data-stu-id="94f1a-116">Database engines</span></span>

<span data-ttu-id="94f1a-117">Le istruzioni per Visual Studio usano [SQL Server Local DB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), una versione di SQL Server Express eseguita solo in Windows.</span><span class="sxs-lookup"><span data-stu-id="94f1a-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="94f1a-118">Le istruzioni per Visual Studio Code usano [SQLite](https://www.sqlite.org/), un motore di database multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="94f1a-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="94f1a-119">Se si sceglie di usare SQLite, scaricare e installare uno strumento di terze parti per la gestione e la visualizzazione di un database SQLite, ad esempio [DB Browser per SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="94f1a-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="94f1a-120">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="94f1a-120">Troubleshooting</span></span>

<span data-ttu-id="94f1a-121">Se si verifica un problema che non è possibile risolvere, confrontare il codice con il [progetto completato](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="94f1a-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="94f1a-122">Un buon metodo per ottenere assistenza è quello di pubblicare una domanda in StackOverflow.com con il [tag ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o il [tag EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="94f1a-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="94f1a-123">App di esempio</span><span class="sxs-lookup"><span data-stu-id="94f1a-123">The sample app</span></span>

<span data-ttu-id="94f1a-124">L'applicazione compilata in queste esercitazioni è il sito Web di base di un'università.</span><span class="sxs-lookup"><span data-stu-id="94f1a-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="94f1a-125">Gli utenti possono visualizzare e aggiornare le informazioni che riguardano studenti, corsi e insegnanti.</span><span class="sxs-lookup"><span data-stu-id="94f1a-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="94f1a-126">Di seguito sono disponibili alcune schermate dell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="94f1a-126">Here are a few of the screens created in the tutorial.</span></span>

![Pagina Student Index (Indice degli studenti)](intro/_static/students-index30.png)

![Pagina Students Edit (Modifica studenti)](intro/_static/student-edit30.png)

<span data-ttu-id="94f1a-129">Lo stile dell'interfaccia utente del sito è basato sui modelli di progetto predefiniti.</span><span class="sxs-lookup"><span data-stu-id="94f1a-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="94f1a-130">L'obiettivo dell'esercitazione è quello di usare EF Core con ASP.NET Core, non come personalizzare l'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="94f1a-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

<!-- 
Follow the link at the top of the page to get the source code for the completed project. The *cu50* folder has the code for the ASP.NET Core 5.0 version of the tutorial. Files that reflect the state of the code for tutorials 1-7 can be found in the *cu50snapshots* folder.

# [Visual Studio](#tab/visual-studio)

To run the app after downloading the completed project:

* Build the project.
* In Package Manager Console (PMC) run the following command:

  ```powershell
  Update-Database
  ```

* Run the project to seed the database.

# [Visual Studio Code](#tab/visual-studio-code)

To run the app after downloading the completed project:

* In *Program.cs*, remove the comments from `// webBuilder.UseStartup<StartupSQLite>();`  so `StartupSQLite` is used.
* Copy the contents of *appSettingsSQLite.json* into *appSettings.json*.
* Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.
* Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.
* Build the project.
* At a command prompt in the project folder, run the following commands:

  ```dotnetcli
  dotnet tool install --global dotnet-ef -v 5.0.0-*
  dotnet ef database update
  ```

* In your SQLite tool, run the following SQL statement:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Run the project to seed the database.

---

-->

## <a name="create-the-web-app-project"></a><span data-ttu-id="94f1a-131">Creare il progetto di app Web</span><span class="sxs-lookup"><span data-stu-id="94f1a-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94f1a-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94f1a-132">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94f1a-133">Avviare Visual Studio e selezionare **Crea un nuovo progetto**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-133">Start Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="94f1a-134">Selezionare **ASP.NET Core applicazione Web** > **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-134">Select **ASP.NET Core Web Application** > **NEXT**.</span></span>
* <span data-ttu-id="94f1a-135">Denominare il progetto *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="94f1a-135">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="94f1a-136">È importante usare questo nome esatto, incluse le maiuscole, in modo che gli spazi dei nomi corrispondano quando il codice viene copiato e incollato.</span><span class="sxs-lookup"><span data-stu-id="94f1a-136">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="94f1a-137">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-137">Select **Create**.</span></span>
* <span data-ttu-id="94f1a-138">Selezionare **.NET Core** e **ASP.NET Core 5,0** negli elenchi a discesa, quindi selezionare **applicazione Web**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-138">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="94f1a-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94f1a-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="94f1a-140">In un terminale passare alla cartella in cui deve essere creata la cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="94f1a-140">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="94f1a-141">Eseguire i comandi seguenti per creare un :::no-loc(Razor)::: progetto di pagine e `cd` nella nuova cartella di progetto:</span><span class="sxs-lookup"><span data-stu-id="94f1a-141">Run the following commands to create a :::no-loc(Razor)::: Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="94f1a-142">Impostare lo stile del sito</span><span class="sxs-lookup"><span data-stu-id="94f1a-142">Set up the site style</span></span>

<span data-ttu-id="94f1a-143">Copiare e incollare il codice seguente nel file *pages/Shared/_Layout. cshtml* : [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span><span class="sxs-lookup"><span data-stu-id="94f1a-143">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span></span>

<span data-ttu-id="94f1a-144">Il file di layout imposta l'intestazione, il piè di pagina e il menu del sito.</span><span class="sxs-lookup"><span data-stu-id="94f1a-144">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="94f1a-145">Il codice precedente apporta le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="94f1a-145">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="94f1a-146">Ogni occorrenza di "ContosoUniversity" in "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="94f1a-146">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="94f1a-147">Le occorrenze sono tre.</span><span class="sxs-lookup"><span data-stu-id="94f1a-147">There are three occurrences.</span></span>
* <span data-ttu-id="94f1a-148">Le voci del menu **Home** e **privacy** verranno eliminate.</span><span class="sxs-lookup"><span data-stu-id="94f1a-148">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="94f1a-149">Vengono aggiunte voci per **informazioni su** , **studenti** , **corsi** , **docenti** e **reparti**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-149">Entries are added for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="94f1a-150">In *pages/index. cshtml* sostituire il contenuto del file con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="94f1a-150">In *Pages/Index.cshtml* , replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="94f1a-151">Il codice precedente sostituisce il testo relativo ASP.NET Core con il testo relativo a questa app.</span><span class="sxs-lookup"><span data-stu-id="94f1a-151">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="94f1a-152">Eseguire l'app per verificare che venga visualizzata la home page.</span><span class="sxs-lookup"><span data-stu-id="94f1a-152">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="94f1a-153">Modello di dati</span><span class="sxs-lookup"><span data-stu-id="94f1a-153">The data model</span></span>

<span data-ttu-id="94f1a-154">Nelle sezioni seguenti viene descritto come creare un modello di dati:</span><span class="sxs-lookup"><span data-stu-id="94f1a-154">The following sections create a data model:</span></span>

![Diagramma modello di dati Course/Enrollment/Student (Corso/Iscrizione/Studente)](intro/_static/data-model-diagram.png)

<span data-ttu-id="94f1a-156">Uno studente può iscriversi a un numero qualsiasi di corsi e un corso può avere un numero qualsiasi di studenti iscritti.</span><span class="sxs-lookup"><span data-stu-id="94f1a-156">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="94f1a-157">Entità Student (Studente)</span><span class="sxs-lookup"><span data-stu-id="94f1a-157">The Student entity</span></span>

![Diagramma entità Student (Studente)](intro/_static/student-entity.png)

* <span data-ttu-id="94f1a-159">Creare una cartella *Models* nella cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="94f1a-159">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="94f1a-160">Creare *Models/Student.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="94f1a-160">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="94f1a-161">La proprietà `ID` diventa la colonna chiave primaria della tabella del database che corrisponde a questa classe.</span><span class="sxs-lookup"><span data-stu-id="94f1a-161">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="94f1a-162">Per impostazione predefinita, Entity Framework Core interpreta una proprietà denominata `ID` o `classnameID` come chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="94f1a-162">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="94f1a-163">Il nome alternativo riconosciuto automaticamente per la chiave primaria della classe `Student` è quindi `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-163">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="94f1a-164">Per altre informazioni, vedere [chiavi EF Core](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="94f1a-164">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="94f1a-165">La proprietà `Enrollments` rappresenta una [proprietà di navigazione](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="94f1a-165">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="94f1a-166">Le proprietà di navigazione contengono altre entità correlate a questa entità.</span><span class="sxs-lookup"><span data-stu-id="94f1a-166">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="94f1a-167">In questo caso la proprietà `Enrollments` di un'entità `Student` contiene tutte le entità `Enrollment` correlate a tale studente.</span><span class="sxs-lookup"><span data-stu-id="94f1a-167">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="94f1a-168">Ad esempio, se una riga Student nel database presenta due righe Enrollment correlate, la proprietà di navigazione `Enrollments` contiene questi due entità Enrollment.</span><span class="sxs-lookup"><span data-stu-id="94f1a-168">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="94f1a-169">Nel database, una riga Enrollment è correlata a una riga Student se la relativa colonna StudentID contiene il valore ID dello studente.</span><span class="sxs-lookup"><span data-stu-id="94f1a-169">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="94f1a-170">Si supponga, ad esempio, che una riga Student abbia ID=1.</span><span class="sxs-lookup"><span data-stu-id="94f1a-170">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="94f1a-171">Le righe Enrollment correlate avranno StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="94f1a-171">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="94f1a-172">StudentID è una *chiave esterna* nella tabella Enrollment.</span><span class="sxs-lookup"><span data-stu-id="94f1a-172">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="94f1a-173">La proprietà `Enrollments` è definita come `ICollection<Enrollment>` perché potrebbero essere presenti più entità Enrollment correlate.</span><span class="sxs-lookup"><span data-stu-id="94f1a-173">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="94f1a-174">È possibile usare altri tipi di raccolta, ad esempio `List<Enrollment>` o `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-174">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="94f1a-175">Se si specifica `ICollection<Enrollment>`, per impostazione predefinita Entity Framework Core crea una raccolta `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-175">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="94f1a-176">Entità Enrollment (Iscrizione)</span><span class="sxs-lookup"><span data-stu-id="94f1a-176">The Enrollment entity</span></span>

![Diagramma entità Enrollment (Iscrizione)](intro/_static/enrollment-entity.png)

<span data-ttu-id="94f1a-178">Creare *Models/Enrollment.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="94f1a-178">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="94f1a-179">La proprietà `EnrollmentID` è la chiave primaria. Questa entità usa il modello `classnameID` anziché `ID` da solo.</span><span class="sxs-lookup"><span data-stu-id="94f1a-179">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="94f1a-180">Per un modello di dati di produzione, scegliere un modello e usarlo in modo coerente.</span><span class="sxs-lookup"><span data-stu-id="94f1a-180">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="94f1a-181">Questa esercitazione usa entrambi solo per illustrare che entrambi funzionano.</span><span class="sxs-lookup"><span data-stu-id="94f1a-181">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="94f1a-182">L'uso di `ID` senza `classname` rende più semplice l'implementazione di alcuni tipi di modifiche al modello di dati.</span><span class="sxs-lookup"><span data-stu-id="94f1a-182">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="94f1a-183">La proprietà `Grade` è un oggetto`enum`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-183">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="94f1a-184">Il punto interrogativo dopo la dichiarazione del tipo `Grade` indica che la proprietà `Grade`[ammette i valori Null](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="94f1a-184">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="94f1a-185">Un voto con valore Null è diverso da un voto con valore zero. Null significa che un voto è sconosciuto o non è stato ancora assegnato.</span><span class="sxs-lookup"><span data-stu-id="94f1a-185">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="94f1a-186">La proprietà `StudentID` è una chiave esterna e la proprietà di navigazione corrispondente è `Student`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-186">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="94f1a-187">Un'entità `Enrollment` è associata a un'entità `Student`, pertanto la proprietà contiene un'unica entità `Student`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-187">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="94f1a-188">La proprietà `CourseID` è una chiave esterna e la proprietà di navigazione corrispondente è `Course`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-188">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="94f1a-189">Un'entità `Enrollment` è associata a un'entità `Course`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-189">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="94f1a-190">Entity Framework Core interpreta una proprietà come chiave esterna se è denominata `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-190">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="94f1a-191">Ad esempio, `StudentID` è la chiave esterna per la proprietà di navigazione `Student`, dato che la chiave primaria `Student` dell'entità è `ID`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-191">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="94f1a-192">Le proprietà di chiave esterna possono anche essere denominate `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-192">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="94f1a-193">Ad esempio, `CourseID` poiché la chiave primaria `Course` dell'entità è `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-193">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="94f1a-194">Entità Course (Corso)</span><span class="sxs-lookup"><span data-stu-id="94f1a-194">The Course entity</span></span>

![Diagramma entità Course (Corso)](intro/_static/course-entity.png)

<span data-ttu-id="94f1a-196">Creare *Models/Course.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="94f1a-196">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="94f1a-197">La proprietà `Enrollments` rappresenta una proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="94f1a-197">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="94f1a-198">È possibile correlare un'entità `Course` a un numero qualsiasi di entità `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-198">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="94f1a-199">L'attributo `DatabaseGenerated` consente all'app di specificare la chiave primaria anziché chiedere al database di generarla.</span><span class="sxs-lookup"><span data-stu-id="94f1a-199">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="94f1a-200">Compilare il progetto per verificare che non siano presenti errori del compilatore.</span><span class="sxs-lookup"><span data-stu-id="94f1a-200">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="94f1a-201">Scaffolding delle pagine Student</span><span class="sxs-lookup"><span data-stu-id="94f1a-201">Scaffold Student pages</span></span>

<span data-ttu-id="94f1a-202">In questa sezione si userà lo strumento di scaffolding di ASP.NET Core per generare:</span><span class="sxs-lookup"><span data-stu-id="94f1a-202">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="94f1a-203">Classe EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="94f1a-203">An EF Core `DbContext` class.</span></span> <span data-ttu-id="94f1a-204">Il contesto è la classe principale che coordina le funzionalità di Entity Framework per un determinato modello di dati.</span><span class="sxs-lookup"><span data-stu-id="94f1a-204">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="94f1a-205">Deriva dalla classe <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="94f1a-205">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="94f1a-206">:::no-loc(Razor)::: pagine che gestiscono le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD) per l' `Student` entità.</span><span class="sxs-lookup"><span data-stu-id="94f1a-206">:::no-loc(Razor)::: pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94f1a-207">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94f1a-207">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94f1a-208">Creare una cartella *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="94f1a-208">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="94f1a-209">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Pages/Students* e quindi scegliere **Aggiungi** > **Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-209">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="94f1a-210">Nella finestra di dialogo **Aggiungi nuovo elemento di impalcatura** :</span><span class="sxs-lookup"><span data-stu-id="94f1a-210">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="94f1a-211">Nella scheda a sinistra selezionare **installato > :::no-loc(Razor)::: pagine comuni >**</span><span class="sxs-lookup"><span data-stu-id="94f1a-211">In the left tab, select **Installed > Common > :::no-loc(Razor)::: Pages**</span></span>
  * <span data-ttu-id="94f1a-212">Selezionare **:::no-loc(Razor)::: pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-212">Select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="94f1a-213">Nella finestra di dialogo **Aggiungi :::no-loc(Razor)::: pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="94f1a-213">In the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="94f1a-214">Nell'elenco a discesa **Classe modello** selezionare **Student (ContosoUniversity.Models)**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-214">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="94f1a-215">Nella riga **Classe contesto di dati** selezionare il segno più **+**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-215">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="94f1a-216">Modificare il nome del contesto dati in modo `SchoolContext` che termini anziché `ContosoUniversityContext` .</span><span class="sxs-lookup"><span data-stu-id="94f1a-216">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="94f1a-217">Nome del contesto aggiornato: `ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="94f1a-217">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="94f1a-218">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-218">Select **Add**.</span></span>

<span data-ttu-id="94f1a-219">Vengono installati automaticamente i pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="94f1a-219">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="94f1a-220">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94f1a-220">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="94f1a-221">Eseguire i comandi seguenti dell'interfaccia della riga di comando di .NET Core per installare i pacchetti NuGet necessari:</span><span class="sxs-lookup"><span data-stu-id="94f1a-221">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="94f1a-222">Per lo scaffolding è necessario il pacchetto Microsoft.VisualStudio.Web.CodeGeneration.Design.</span><span class="sxs-lookup"><span data-stu-id="94f1a-222">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="94f1a-223">Sebbene l'app non usi SQL Server, lo strumento di scaffolding richiede il pacchetto di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="94f1a-223">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="94f1a-224">Creare una cartella *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="94f1a-224">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="94f1a-225">Eseguire il comando seguente per installare lo [strumento di scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="94f1a-225">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="94f1a-226">Eseguire il comando seguente per eseguire lo scaffolding delle pagine Student.</span><span class="sxs-lookup"><span data-stu-id="94f1a-226">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="94f1a-227">**In Windows**</span><span class="sxs-lookup"><span data-stu-id="94f1a-227">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="94f1a-228">**In macOS o Linux**</span><span class="sxs-lookup"><span data-stu-id="94f1a-228">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="94f1a-229">Se il passaggio precedente ha esito negativo, compilare il progetto e riprovare l'operazione di impalcatura.</span><span class="sxs-lookup"><span data-stu-id="94f1a-229">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="94f1a-230">Il processo di scaffolding:</span><span class="sxs-lookup"><span data-stu-id="94f1a-230">The scaffolding process:</span></span>

* <span data-ttu-id="94f1a-231">Crea :::no-loc(Razor)::: pagine nella cartella *pages/students* :</span><span class="sxs-lookup"><span data-stu-id="94f1a-231">Creates :::no-loc(Razor)::: pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="94f1a-232">*Create.cshtml* e *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="94f1a-232">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="94f1a-233">*Delete.cshtml* e *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="94f1a-233">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="94f1a-234">*Details.cshtml* e *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="94f1a-234">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="94f1a-235">*Edit.cshtml* e *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="94f1a-235">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="94f1a-236">*Index.cshtml* e *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="94f1a-236">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="94f1a-237">Crea *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="94f1a-237">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="94f1a-238">Aggiunge il contesto all'inserimento delle dipendenze in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="94f1a-238">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="94f1a-239">Aggiunge una stringa di connessione al database a *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="94f1a-239">Adds a database connection string to *:::no-loc(appsettings.json):::*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="94f1a-240">Stringa di connessione al database</span><span class="sxs-lookup"><span data-stu-id="94f1a-240">Database connection string</span></span>

<span data-ttu-id="94f1a-241">Lo strumento di impalcatura genera una stringa di connessione nel *:::no-loc(appsettings.json):::* file.</span><span class="sxs-lookup"><span data-stu-id="94f1a-241">The scaffolding tool generates a connection string in the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94f1a-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94f1a-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="94f1a-243">La stringa di connessione specifica [SQL Server database locale](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span><span class="sxs-lookup"><span data-stu-id="94f1a-243">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/:::no-loc(appsettings.json):::?highlight=11)]

<span data-ttu-id="94f1a-244">Local DB è una versione leggera del motore di database di SQL Server Express appositamente pensato per lo sviluppo di app e non per la produzione.</span><span class="sxs-lookup"><span data-stu-id="94f1a-244">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="94f1a-245">Per impostazione predefinita, Local DB crea file con estensione *mdf* nella directory `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-245">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="94f1a-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94f1a-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="94f1a-247">Abbreviare la stringa di connessione SQLite a *cu. DB* :</span><span class="sxs-lookup"><span data-stu-id="94f1a-247">Shorten the SQLite connection string to *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="94f1a-248">Aggiornare la classe del contesto di database</span><span class="sxs-lookup"><span data-stu-id="94f1a-248">Update the database context class</span></span>

<span data-ttu-id="94f1a-249">La classe principale che coordina le funzionalità di EF Core per un determinato modello di dati è la classe del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-249">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="94f1a-250">Il contesto è derivato da [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="94f1a-250">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="94f1a-251">Il contesto specifica le entità incluse nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="94f1a-251">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="94f1a-252">In questo progetto la classe è denominata `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-252">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="94f1a-253">Aggiornare *Data/SchoolContext.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="94f1a-253">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="94f1a-254">Il codice precedente passa da singolare `DbSet<Student> Student` a plurale `DbSet<Student> Students` .</span><span class="sxs-lookup"><span data-stu-id="94f1a-254">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="94f1a-255">Per fare in :::no-loc(Razor)::: modo che il codice delle pagine corrisponda al nuovo `DBSet` nome, apportare una modifica globale da: `_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="94f1a-255">To make the :::no-loc(Razor)::: Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="94f1a-256">A: `_context.Students.`</span><span class="sxs-lookup"><span data-stu-id="94f1a-256">to: `_context.Students.`</span></span>

<span data-ttu-id="94f1a-257">Sono presenti 8 occorrenze.</span><span class="sxs-lookup"><span data-stu-id="94f1a-257">There are 8 occurrences.</span></span>

<span data-ttu-id="94f1a-258">Poiché un set di entità contiene più entità, molti sviluppatori preferiscono che i `DBSet` nomi delle proprietà siano plurali.</span><span class="sxs-lookup"><span data-stu-id="94f1a-258">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="94f1a-259">Il codice evidenziato:</span><span class="sxs-lookup"><span data-stu-id="94f1a-259">The highlighted code:</span></span>

* <span data-ttu-id="94f1a-260">Crea una [proprietà \<TEntity> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per ogni set di entità.</span><span class="sxs-lookup"><span data-stu-id="94f1a-260">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="94f1a-261">Nella terminologia di Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="94f1a-261">In EF Core terminology:</span></span>
  * <span data-ttu-id="94f1a-262">Un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-262">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="94f1a-263">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="94f1a-263">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="94f1a-264">Chiama <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="94f1a-264">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="94f1a-265">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="94f1a-265">`OnModelCreating`:</span></span>
  * <span data-ttu-id="94f1a-266">Viene chiamato quando `SchoolContext` è stato inizializzato, ma prima che il modello sia stato bloccato e utilizzato per inizializzare il contesto.</span><span class="sxs-lookup"><span data-stu-id="94f1a-266">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="94f1a-267">È obbligatorio perché, più avanti nell'esercitazione `Student` , l'entità avrà riferimenti alle altre entità.</span><span class="sxs-lookup"><span data-stu-id="94f1a-267">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="94f1a-268">Compilare il progetto per verificare che non siano presenti errori di compilazione.</span><span class="sxs-lookup"><span data-stu-id="94f1a-268">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="94f1a-269">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="94f1a-269">Startup.cs</span></span>

<span data-ttu-id="94f1a-270">ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="94f1a-270">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="94f1a-271">I servizi, ad esempio, `SchoolContext` vengono registrati con l'inserimento delle dipendenze durante l'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="94f1a-271">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="94f1a-272">I componenti che richiedono questi servizi, ad esempio :::no-loc(Razor)::: le pagine, vengono forniti tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="94f1a-272">Components that require these services, such as :::no-loc(Razor)::: Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="94f1a-273">Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-273">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="94f1a-274">Lo strumento di scaffolding ha registrato automaticamente la classe di contesto nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="94f1a-274">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94f1a-275">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94f1a-275">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="94f1a-276">Le seguenti righe evidenziate sono state aggiunte dall'impalcatura:</span><span class="sxs-lookup"><span data-stu-id="94f1a-276">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="94f1a-277">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94f1a-277">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="94f1a-278">Verificare il codice aggiunto dall'impalcatura chiama `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="94f1a-278">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="94f1a-279">Per informazioni sull'uso di un database di produzione [, vedere usare SQLite per lo sviluppo, SQL Server per la produzione](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) .</span><span class="sxs-lookup"><span data-stu-id="94f1a-279">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="94f1a-280">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="94f1a-280">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="94f1a-281">Per lo sviluppo locale, il [sistema di configurazione ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal *:::no-loc(appsettings.json):::* file.</span><span class="sxs-lookup"><span data-stu-id="94f1a-281">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="94f1a-282">Aggiungere il filtro eccezioni del database</span><span class="sxs-lookup"><span data-stu-id="94f1a-282">Add the database exception filter</span></span>

<span data-ttu-id="94f1a-283">Aggiungere <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> a `ConfigureServices` come illustrato nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="94f1a-283">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94f1a-284">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94f1a-284">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="94f1a-285">Aggiungere il pacchetto NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) .</span><span class="sxs-lookup"><span data-stu-id="94f1a-285">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="94f1a-286">In PMC immettere quanto segue per aggiungere il pacchetto NuGet:</span><span class="sxs-lookup"><span data-stu-id="94f1a-286">In the PMC, enter the following to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="94f1a-287">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94f1a-287">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="94f1a-288">Il `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` pacchetto NuGet fornisce ASP.NET Core middleware per Entity Framework Core pagine di errore.</span><span class="sxs-lookup"><span data-stu-id="94f1a-288">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="94f1a-289">Questo middleware consente di rilevare e diagnosticare gli errori con Entity Framework Core migrazioni.</span><span class="sxs-lookup"><span data-stu-id="94f1a-289">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

<span data-ttu-id="94f1a-290">`AddDatabaseDeveloperPageExceptionFilter`Fornisce informazioni utili sull'errore nell' [ambiente di sviluppo](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="94f1a-290">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

## <a name="create-the-database"></a><span data-ttu-id="94f1a-291">Creare il database</span><span class="sxs-lookup"><span data-stu-id="94f1a-291">Create the database</span></span>

<span data-ttu-id="94f1a-292">Aggiornare *Program.cs* per creare il database se non esiste:</span><span class="sxs-lookup"><span data-stu-id="94f1a-292">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="94f1a-293">Il metodo [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) non esegue alcuna azione se esiste un database per il contesto.</span><span class="sxs-lookup"><span data-stu-id="94f1a-293">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="94f1a-294">Se non esiste alcun database, vengono creati il database e lo schema.</span><span class="sxs-lookup"><span data-stu-id="94f1a-294">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="94f1a-295">`EnsureCreated` abilita il flusso di lavoro seguente per la gestione delle modifiche al modello di dati:</span><span class="sxs-lookup"><span data-stu-id="94f1a-295">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="94f1a-296">Eliminare il database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-296">Delete the database.</span></span> <span data-ttu-id="94f1a-297">Tutti i dati esistenti andranno perduti.</span><span class="sxs-lookup"><span data-stu-id="94f1a-297">Any existing data is lost.</span></span>
* <span data-ttu-id="94f1a-298">Modificare il modello di dati.</span><span class="sxs-lookup"><span data-stu-id="94f1a-298">Change the data model.</span></span> <span data-ttu-id="94f1a-299">Ad esempio, aggiungere un campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-299">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="94f1a-300">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="94f1a-300">Run the app.</span></span>
* <span data-ttu-id="94f1a-301">`EnsureCreated` crea un database con il nuovo schema.</span><span class="sxs-lookup"><span data-stu-id="94f1a-301">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="94f1a-302">Questo flusso di lavoro funziona correttamente nelle fasi iniziali dello sviluppo quando lo schema è in rapida evoluzione, purché non sia necessario conservare i dati.</span><span class="sxs-lookup"><span data-stu-id="94f1a-302">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="94f1a-303">La situazione è diversa quando è necessario mantenere i dati immessi nel database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-303">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="94f1a-304">In tal caso, usare le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="94f1a-304">When that is the case, use migrations.</span></span>

<span data-ttu-id="94f1a-305">Più avanti nella serie di esercitazioni si vedrà come eliminare il database creato da `EnsureCreated` e usare invece le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="94f1a-305">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="94f1a-306">Non è possibile aggiornare un database creato da `EnsureCreated` usando le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="94f1a-306">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="94f1a-307">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="94f1a-307">Test the app</span></span>

* <span data-ttu-id="94f1a-308">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="94f1a-308">Run the app.</span></span>
* <span data-ttu-id="94f1a-309">Selezionare il collegamento **Students** (Studenti) e quindi **Crea nuovo**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-309">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="94f1a-310">Eseguire il test dei collegamenti Edit (Modifica), Details (Dettagli) e Delete (Elimina).</span><span class="sxs-lookup"><span data-stu-id="94f1a-310">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="94f1a-311">Specificare il valore di inizializzazione del database</span><span class="sxs-lookup"><span data-stu-id="94f1a-311">Seed the database</span></span>

<span data-ttu-id="94f1a-312">Il metodo `EnsureCreated` crea un database vuoto.</span><span class="sxs-lookup"><span data-stu-id="94f1a-312">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="94f1a-313">In questa sezione viene aggiunto il codice che popola il database con dati di test.</span><span class="sxs-lookup"><span data-stu-id="94f1a-313">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="94f1a-314">Creare *Data/DbInitializer.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="94f1a-314">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="94f1a-315">Il codice controlla se esistono studenti nel database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-315">The code checks if there are any students in the database.</span></span> <span data-ttu-id="94f1a-316">Se non sono presenti studenti, aggiunge i dati di test al database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-316">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="94f1a-317">I dati di test vengono creati in matrici anziché in raccolte `List<T>` per ottimizzare le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="94f1a-317">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="94f1a-318">In *Program.cs* sostituire la chiamata di `EnsureCreated` con una chiamata di `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="94f1a-318">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="94f1a-319">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94f1a-319">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="94f1a-320">Arrestare l'app se è in esecuzione ed eseguire il comando seguente nella **console di Gestione pacchetti** :</span><span class="sxs-lookup"><span data-stu-id="94f1a-320">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="94f1a-321">Rispondere con `Y` per eliminare il database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-321">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="94f1a-322">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94f1a-322">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="94f1a-323">Arrestare l'app se è in esecuzione ed eliminare il file *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="94f1a-323">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="94f1a-324">Riavviare l'app.</span><span class="sxs-lookup"><span data-stu-id="94f1a-324">Restart the app.</span></span>
* <span data-ttu-id="94f1a-325">Selezionare la pagina Students per visualizzare i dati di seeding.</span><span class="sxs-lookup"><span data-stu-id="94f1a-325">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="94f1a-326">Visualizzare il database</span><span class="sxs-lookup"><span data-stu-id="94f1a-326">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94f1a-327">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94f1a-327">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94f1a-328">Aprire **Esplora oggetti di SQL Server** dal menu **Visualizza** in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="94f1a-328">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="94f1a-329">In Esplora oggetti di SQL Server selezionare **(localdb)\MSSQLLocalDB > Database > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-329">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="94f1a-330">Il nome del database viene generato dal nome del contesto specificato in precedenza con l'aggiunta di un trattino e un GUID.</span><span class="sxs-lookup"><span data-stu-id="94f1a-330">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="94f1a-331">Espandere il nodo **Tabelle**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-331">Expand the **Tables** node.</span></span>
* <span data-ttu-id="94f1a-332">Fare clic con il pulsante destro del mouse sulla tabella **Student** (Studente) e fare clic su **Visualizza dati** per visualizzare le colonne create e le righe inserite nella tabella.</span><span class="sxs-lookup"><span data-stu-id="94f1a-332">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="94f1a-333">Fare clic con il pulsante destro del mouse sulla tabella **Student** e scegliere **Visualizza codice** per vedere il mapping tra il modello `Student` e lo schema della tabella `Student`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-333">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="94f1a-334">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94f1a-334">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="94f1a-335">Usare lo strumento SQLite per visualizzare lo schema del database e i dati di seeding.</span><span class="sxs-lookup"><span data-stu-id="94f1a-335">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="94f1a-336">Il file di database è denominato *CU.db* e si trova nella cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="94f1a-336">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="94f1a-337">Codice asincrono</span><span class="sxs-lookup"><span data-stu-id="94f1a-337">Asynchronous code</span></span>

<span data-ttu-id="94f1a-338">La programmazione asincrona è la modalità predefinita per ASP.NET Core ed Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="94f1a-338">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="94f1a-339">Per un server Web è disponibile un numero limitato di thread e in situazioni di carico elevato tutti i thread disponibili potrebbero essere in uso.</span><span class="sxs-lookup"><span data-stu-id="94f1a-339">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="94f1a-340">In queste circostanze il server non può elaborare nuove richieste finché i thread non saranno liberi.</span><span class="sxs-lookup"><span data-stu-id="94f1a-340">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="94f1a-341">Con il codice sincrono, è possibile che molti thread siano collegati mentre non funzionano perché sono in attesa del completamento dell'I/O.</span><span class="sxs-lookup"><span data-stu-id="94f1a-341">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="94f1a-342">Con il codice asincrono, se un processo è in attesa del completamento dell'operazione I/O, il thread viene liberato e il server lo può usare per l'elaborazione di altre richieste.</span><span class="sxs-lookup"><span data-stu-id="94f1a-342">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="94f1a-343">Di conseguenza, il codice asincrono consente un uso più efficiente delle risorse del server e il server può gestire una maggiore quantità di traffico senza ritardi.</span><span class="sxs-lookup"><span data-stu-id="94f1a-343">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="94f1a-344">Il codice asincrono comporta un minimo sovraccarico in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="94f1a-344">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="94f1a-345">In caso di traffico ridotto, il calo delle prestazioni è irrilevante, mentre nelle situazioni di traffico elevato, è essenziale ottimizzare le prestazioni potenziali.</span><span class="sxs-lookup"><span data-stu-id="94f1a-345">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="94f1a-346">Nel codice seguente la parola chiave [async](/dotnet/csharp/language-reference/keywords/async), il valore restituito `Task<T>`, la parola chiave `await` e il metodo `ToListAsync` consentono di eseguire il codice in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="94f1a-346">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="94f1a-347">La parola chiave `async` indica al compilatore di eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="94f1a-347">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="94f1a-348">Generare callback per parti del corpo del metodo.</span><span class="sxs-lookup"><span data-stu-id="94f1a-348">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="94f1a-349">Creare l'oggetto [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) restituito.</span><span class="sxs-lookup"><span data-stu-id="94f1a-349">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="94f1a-350">Il tipo restituito `Task<T>` rappresenta il lavoro in corso.</span><span class="sxs-lookup"><span data-stu-id="94f1a-350">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="94f1a-351">La parola chiave `await` indica al compilatore di suddividere il metodo in due parti.</span><span class="sxs-lookup"><span data-stu-id="94f1a-351">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="94f1a-352">La prima parte termina con l'operazione avviata in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="94f1a-352">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="94f1a-353">La seconda parte viene inserita in un metodo di callback che viene chiamato al termine dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="94f1a-353">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="94f1a-354">`ToListAsync` è la versione asincrona del metodo di estensione `ToList`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-354">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="94f1a-355">Alcuni aspetti da considerare quando si scrive codice asincrono usato da Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="94f1a-355">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="94f1a-356">Solo le istruzioni che generano query o comandi da inviare al database vengono eseguite in modo asincrono,</span><span class="sxs-lookup"><span data-stu-id="94f1a-356">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="94f1a-357">Sono incluse `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-357">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="94f1a-358">Non sono incluse le istruzioni che modificano solo un oggetto `IQueryable`, ad esempio `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-358">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="94f1a-359">Un contesto di Entity Framework Core non è thread-safe. Non tentare di eseguire più operazioni parallelamente.</span><span class="sxs-lookup"><span data-stu-id="94f1a-359">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="94f1a-360">Per sfruttare i vantaggi del codice asincrono in termini di prestazioni, verificare che i pacchetti della libreria impiegati, ad esempio per il paging, usino la modalità asincrona per chiamare i metodi di EF Core che inviano query al database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-360">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="94f1a-361">Per altre informazioni sulla programmazione asincrona in .NET, vedere [Panoramica della programmazione asincrona](/dotnet/standard/async) e [Programmazione asincrona con async e await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="94f1a-361">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="94f1a-362">Considerazioni sulle prestazioni</span><span class="sxs-lookup"><span data-stu-id="94f1a-362">Performance considerations</span></span>

<span data-ttu-id="94f1a-363">In generale, una pagina Web non deve caricare un numero arbitrario di righe.</span><span class="sxs-lookup"><span data-stu-id="94f1a-363">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="94f1a-364">Una query deve usare il paging o un approccio di limitazione.</span><span class="sxs-lookup"><span data-stu-id="94f1a-364">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="94f1a-365">Ad esempio, la query precedente può utilizzare `Take` per limitare le righe restituite:</span><span class="sxs-lookup"><span data-stu-id="94f1a-365">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="94f1a-366">L'enumerazione di una tabella di grandi dimensioni in una vista può restituire una risposta HTTP 200 parzialmente costruita se un'eccezione del database si verifica in modo parziale attraverso l'enumerazione.</span><span class="sxs-lookup"><span data-stu-id="94f1a-366">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="94f1a-367"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> il valore predefinito è 1024.</span><span class="sxs-lookup"><span data-stu-id="94f1a-367"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="94f1a-368">Il codice seguente imposta `MaxModelBindingCollectionSize` :</span><span class="sxs-lookup"><span data-stu-id="94f1a-368">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="94f1a-369">Il paging verrà trattato più avanti nell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="94f1a-369">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="94f1a-370">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="94f1a-370">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="94f1a-371">Esercitazione successiva</span><span class="sxs-lookup"><span data-stu-id="94f1a-371">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="94f1a-372">Questo è il primo di una serie di esercitazioni che illustrano come usare Entity Framework (EF) Core in un'app [ASP.NET Core :::no-loc(Razor)::: pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="94f1a-372">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="94f1a-373">Con queste esercitazioni viene creato un sito Web per una fittizia Contoso University.</span><span class="sxs-lookup"><span data-stu-id="94f1a-373">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="94f1a-374">Il sito include funzionalità, come ad esempio l'ammissione di studenti, la creazione di corsi e le assegnazioni degli insegnati.</span><span class="sxs-lookup"><span data-stu-id="94f1a-374">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="94f1a-375">Nell'esercitazione viene usato l'approccio Code First.</span><span class="sxs-lookup"><span data-stu-id="94f1a-375">The tutorial uses the code first approach.</span></span> <span data-ttu-id="94f1a-376">Per informazioni su come seguire questa esercitazione usando il primo approccio al database, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="94f1a-376">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="94f1a-377">Scaricare o visualizzare l'app completata.</span><span class="sxs-lookup"><span data-stu-id="94f1a-377">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="94f1a-378">[Istruzioni per il download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="94f1a-378">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="94f1a-379">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="94f1a-379">Prerequisites</span></span>

* <span data-ttu-id="94f1a-380">Se non si ha familiarità con :::no-loc(Razor)::: le pagine, vedere la serie di esercitazioni [introduttive sulle :::no-loc(Razor)::: pagine](xref:tutorials/razor-pages/razor-pages-start) prima di iniziare.</span><span class="sxs-lookup"><span data-stu-id="94f1a-380">If you're new to :::no-loc(Razor)::: Pages, go through the [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94f1a-381">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94f1a-381">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="94f1a-382">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94f1a-382">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="94f1a-383">Motori di database</span><span class="sxs-lookup"><span data-stu-id="94f1a-383">Database engines</span></span>

<span data-ttu-id="94f1a-384">Le istruzioni per Visual Studio usano [SQL Server Local DB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), una versione di SQL Server Express eseguita solo in Windows.</span><span class="sxs-lookup"><span data-stu-id="94f1a-384">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="94f1a-385">Le istruzioni per Visual Studio Code usano [SQLite](https://www.sqlite.org/), un motore di database multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="94f1a-385">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="94f1a-386">Se si sceglie di usare SQLite, scaricare e installare uno strumento di terze parti per la gestione e la visualizzazione di un database SQLite, ad esempio [DB Browser per SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="94f1a-386">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="94f1a-387">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="94f1a-387">Troubleshooting</span></span>

<span data-ttu-id="94f1a-388">Se si verifica un problema che non è possibile risolvere, confrontare il codice con il [progetto completato](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="94f1a-388">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="94f1a-389">Un buon metodo per ottenere assistenza è quello di pubblicare una domanda in StackOverflow.com con il [tag ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o il [tag EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="94f1a-389">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="94f1a-390">App di esempio</span><span class="sxs-lookup"><span data-stu-id="94f1a-390">The sample app</span></span>

<span data-ttu-id="94f1a-391">L'applicazione compilata in queste esercitazioni è il sito Web di base di un'università.</span><span class="sxs-lookup"><span data-stu-id="94f1a-391">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="94f1a-392">Gli utenti possono visualizzare e aggiornare le informazioni che riguardano studenti, corsi e insegnanti.</span><span class="sxs-lookup"><span data-stu-id="94f1a-392">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="94f1a-393">Di seguito sono disponibili alcune schermate dell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="94f1a-393">Here are a few of the screens created in the tutorial.</span></span>

![Pagina Student Index (Indice degli studenti)](intro/_static/students-index30.png)

![Pagina Students Edit (Modifica studenti)](intro/_static/student-edit30.png)

<span data-ttu-id="94f1a-396">Lo stile dell'interfaccia utente del sito è basato sui modelli di progetto predefiniti.</span><span class="sxs-lookup"><span data-stu-id="94f1a-396">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="94f1a-397">L'obiettivo dell'esercitazione è quello di usare EF Core e non di personalizzare l'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="94f1a-397">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="94f1a-398">Seguire il collegamento nella parte superiore della pagina per ottenere il codice sorgente per il progetto completato.</span><span class="sxs-lookup"><span data-stu-id="94f1a-398">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="94f1a-399">La cartella *cu30* contiene il codice per la versione di ASP.NET Core 3.0 dell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="94f1a-399">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="94f1a-400">I file che riflettono lo stato del codice per le esercitazioni 1-7 si trovano nella cartella *cu30snapshots*.</span><span class="sxs-lookup"><span data-stu-id="94f1a-400">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94f1a-401">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94f1a-401">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="94f1a-402">Per eseguire l'app dopo il download del progetto completato:</span><span class="sxs-lookup"><span data-stu-id="94f1a-402">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="94f1a-403">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="94f1a-403">Build the project.</span></span>
* <span data-ttu-id="94f1a-404">Nella console di Gestione pacchetti eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="94f1a-404">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="94f1a-405">Eseguire il progetto per il seeding del database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-405">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="94f1a-406">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94f1a-406">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="94f1a-407">Per eseguire l'app dopo il download del progetto completato:</span><span class="sxs-lookup"><span data-stu-id="94f1a-407">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="94f1a-408">Eliminare *ContosoUniversity.csproj* e rinominare *ContosoUniversitySQLite.csproj* in *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="94f1a-408">Delete *ContosoUniversity.csproj* , and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="94f1a-409">In *Program.cs* , impostare come commento l' `#define Startup` `StartupSQLite` utilizzo di.</span><span class="sxs-lookup"><span data-stu-id="94f1a-409">In *Program.cs* , comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="94f1a-410">Eliminare *appSettings.json* e rinominare *appSettingsSQLite.json* in *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="94f1a-410">Delete *appSettings.json* , and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="94f1a-411">Eliminare la cartella *Migrations* e rinominare *MigrationsSQL* in *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="94f1a-411">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="94f1a-412">Eseguire una ricerca globale `#if SQLiteVersion` e rimuovere `#if SQLiteVersion` e l'istruzione associata `#endif` .</span><span class="sxs-lookup"><span data-stu-id="94f1a-412">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="94f1a-413">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="94f1a-413">Build the project.</span></span>
* <span data-ttu-id="94f1a-414">Al prompt dei comandi nella cartella del progetto eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="94f1a-414">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="94f1a-415">Nello strumento SQLite eseguire l'istruzione SQL seguente:</span><span class="sxs-lookup"><span data-stu-id="94f1a-415">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="94f1a-416">Eseguire il progetto per il seeding del database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-416">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="94f1a-417">Creare il progetto di app Web</span><span class="sxs-lookup"><span data-stu-id="94f1a-417">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94f1a-418">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94f1a-418">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94f1a-419">Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-419">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="94f1a-420">Selezionare **Applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-420">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="94f1a-421">Denominare il progetto *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="94f1a-421">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="94f1a-422">È importante usare questo nome esatto, incluse le maiuscole, in modo che gli spazi dei nomi corrispondano quando il codice viene copiato e incollato.</span><span class="sxs-lookup"><span data-stu-id="94f1a-422">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="94f1a-423">Selezionare **.NET Core** e **ASP.NET Core 3.0** nell'elenco a discesa, quindi selezionare **Applicazione Web**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-423">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="94f1a-424">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94f1a-424">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="94f1a-425">In un terminale passare alla cartella in cui deve essere creata la cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="94f1a-425">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="94f1a-426">Eseguire i comandi seguenti per creare un :::no-loc(Razor)::: progetto di pagine e `cd` nella nuova cartella di progetto:</span><span class="sxs-lookup"><span data-stu-id="94f1a-426">Run the following commands to create a :::no-loc(Razor)::: Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="94f1a-427">Impostare lo stile del sito</span><span class="sxs-lookup"><span data-stu-id="94f1a-427">Set up the site style</span></span>

<span data-ttu-id="94f1a-428">Per impostare l'intestazione, il piè di pagina e il menu del sito, aggiornare *Pages/Shared/_Layout.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="94f1a-428">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml* :</span></span>

* <span data-ttu-id="94f1a-429">Modificare tutte le occorrenze di "ContosoUniversity" in "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="94f1a-429">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="94f1a-430">Le occorrenze sono tre.</span><span class="sxs-lookup"><span data-stu-id="94f1a-430">There are three occurrences.</span></span>

* <span data-ttu-id="94f1a-431">Eliminare le voci di menu **Home** e **Privacy** , quindi aggiungere le voci per **About** (Informazioni su), **Students** (Studenti), **Courses** (Corsi), **Instructors** (Insegnanti) e **Departments** (Dipartimenti).</span><span class="sxs-lookup"><span data-stu-id="94f1a-431">Delete the **Home** and **Privacy** menu entries, and add entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="94f1a-432">Le modifiche sono evidenziate.</span><span class="sxs-lookup"><span data-stu-id="94f1a-432">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="94f1a-433">In *Pages/Index.cshtml* sostituire il contenuto del file con il codice seguente. In questo modo il testo su ASP.NET sarà sostituito dal testo relativo a questa app:</span><span class="sxs-lookup"><span data-stu-id="94f1a-433">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="94f1a-434">Eseguire l'app per verificare che venga visualizzata la home page.</span><span class="sxs-lookup"><span data-stu-id="94f1a-434">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="94f1a-435">Modello di dati</span><span class="sxs-lookup"><span data-stu-id="94f1a-435">The data model</span></span>

<span data-ttu-id="94f1a-436">Nelle sezioni seguenti viene descritto come creare un modello di dati:</span><span class="sxs-lookup"><span data-stu-id="94f1a-436">The following sections create a data model:</span></span>

![Diagramma modello di dati Course/Enrollment/Student (Corso/Iscrizione/Studente)](intro/_static/data-model-diagram.png)

<span data-ttu-id="94f1a-438">Uno studente può iscriversi a un numero qualsiasi di corsi e un corso può avere un numero qualsiasi di studenti iscritti.</span><span class="sxs-lookup"><span data-stu-id="94f1a-438">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="94f1a-439">Entità Student (Studente)</span><span class="sxs-lookup"><span data-stu-id="94f1a-439">The Student entity</span></span>

![Diagramma entità Student (Studente)](intro/_static/student-entity.png)

* <span data-ttu-id="94f1a-441">Creare una cartella *Models* nella cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="94f1a-441">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="94f1a-442">Creare *Models/Student.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="94f1a-442">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="94f1a-443">La proprietà `ID` diventa la colonna chiave primaria della tabella del database che corrisponde a questa classe.</span><span class="sxs-lookup"><span data-stu-id="94f1a-443">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="94f1a-444">Per impostazione predefinita, Entity Framework Core interpreta una proprietà denominata `ID` o `classnameID` come chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="94f1a-444">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="94f1a-445">Il nome alternativo riconosciuto automaticamente per la chiave primaria della classe `Student` è quindi `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-445">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="94f1a-446">Per altre informazioni, vedere [chiavi EF Core](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="94f1a-446">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="94f1a-447">La proprietà `Enrollments` rappresenta una [proprietà di navigazione](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="94f1a-447">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="94f1a-448">Le proprietà di navigazione contengono altre entità correlate a questa entità.</span><span class="sxs-lookup"><span data-stu-id="94f1a-448">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="94f1a-449">In questo caso la proprietà `Enrollments` di un'entità `Student` contiene tutte le entità `Enrollment` correlate a tale studente.</span><span class="sxs-lookup"><span data-stu-id="94f1a-449">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="94f1a-450">Ad esempio, se una riga Student nel database presenta due righe Enrollment correlate, la proprietà di navigazione `Enrollments` contiene questi due entità Enrollment.</span><span class="sxs-lookup"><span data-stu-id="94f1a-450">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="94f1a-451">Nel database, una riga Enrollment è correlata a una riga Student se la relativa colonna StudentID contiene il valore ID dello studente.</span><span class="sxs-lookup"><span data-stu-id="94f1a-451">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="94f1a-452">Si supponga, ad esempio, che una riga Student abbia ID=1.</span><span class="sxs-lookup"><span data-stu-id="94f1a-452">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="94f1a-453">Le righe Enrollment correlate avranno StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="94f1a-453">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="94f1a-454">StudentID è una *chiave esterna* nella tabella Enrollment.</span><span class="sxs-lookup"><span data-stu-id="94f1a-454">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="94f1a-455">La proprietà `Enrollments` è definita come `ICollection<Enrollment>` perché potrebbero essere presenti più entità Enrollment correlate.</span><span class="sxs-lookup"><span data-stu-id="94f1a-455">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="94f1a-456">È possibile usare altri tipi di raccolta, ad esempio `List<Enrollment>` o `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-456">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="94f1a-457">Se si specifica `ICollection<Enrollment>`, per impostazione predefinita Entity Framework Core crea una raccolta `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-457">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="94f1a-458">Entità Enrollment (Iscrizione)</span><span class="sxs-lookup"><span data-stu-id="94f1a-458">The Enrollment entity</span></span>

![Diagramma entità Enrollment (Iscrizione)](intro/_static/enrollment-entity.png)

<span data-ttu-id="94f1a-460">Creare *Models/Enrollment.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="94f1a-460">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="94f1a-461">La proprietà `EnrollmentID` è la chiave primaria. Questa entità usa il modello `classnameID` anziché `ID` da solo.</span><span class="sxs-lookup"><span data-stu-id="94f1a-461">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="94f1a-462">Per un modello di dati di produzione, scegliere un modello e usarlo in modo coerente.</span><span class="sxs-lookup"><span data-stu-id="94f1a-462">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="94f1a-463">Questa esercitazione usa entrambi solo per illustrare che entrambi funzionano.</span><span class="sxs-lookup"><span data-stu-id="94f1a-463">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="94f1a-464">L'uso di `ID` senza `classname` rende più semplice l'implementazione di alcuni tipi di modifiche al modello di dati.</span><span class="sxs-lookup"><span data-stu-id="94f1a-464">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="94f1a-465">La proprietà `Grade` è un oggetto`enum`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-465">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="94f1a-466">Il punto interrogativo dopo la dichiarazione del tipo `Grade` indica che la proprietà `Grade`[ammette i valori Null](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="94f1a-466">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="94f1a-467">Un voto con valore Null è diverso da un voto con valore zero. Null significa che un voto è sconosciuto o non è stato ancora assegnato.</span><span class="sxs-lookup"><span data-stu-id="94f1a-467">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="94f1a-468">La proprietà `StudentID` è una chiave esterna e la proprietà di navigazione corrispondente è `Student`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-468">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="94f1a-469">Un'entità `Enrollment` è associata a un'entità `Student`, pertanto la proprietà contiene un'unica entità `Student`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-469">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="94f1a-470">La proprietà `CourseID` è una chiave esterna e la proprietà di navigazione corrispondente è `Course`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-470">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="94f1a-471">Un'entità `Enrollment` è associata a un'entità `Course`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-471">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="94f1a-472">Entity Framework Core interpreta una proprietà come chiave esterna se è denominata `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-472">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="94f1a-473">Ad esempio, `StudentID` è la chiave esterna per la proprietà di navigazione `Student`, dato che la chiave primaria `Student` dell'entità è `ID`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-473">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="94f1a-474">Le proprietà di chiave esterna possono anche essere denominate `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-474">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="94f1a-475">Ad esempio, `CourseID` poiché la chiave primaria `Course` dell'entità è `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-475">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="94f1a-476">Entità Course (Corso)</span><span class="sxs-lookup"><span data-stu-id="94f1a-476">The Course entity</span></span>

![Diagramma entità Course (Corso)](intro/_static/course-entity.png)

<span data-ttu-id="94f1a-478">Creare *Models/Course.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="94f1a-478">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="94f1a-479">La proprietà `Enrollments` rappresenta una proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="94f1a-479">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="94f1a-480">È possibile correlare un'entità `Course` a un numero qualsiasi di entità `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-480">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="94f1a-481">L'attributo `DatabaseGenerated` consente all'app di specificare la chiave primaria anziché chiedere al database di generarla.</span><span class="sxs-lookup"><span data-stu-id="94f1a-481">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="94f1a-482">Compilare il progetto per verificare che non siano presenti errori del compilatore.</span><span class="sxs-lookup"><span data-stu-id="94f1a-482">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="94f1a-483">Scaffolding delle pagine Student</span><span class="sxs-lookup"><span data-stu-id="94f1a-483">Scaffold Student pages</span></span>

<span data-ttu-id="94f1a-484">In questa sezione si userà lo strumento di scaffolding di ASP.NET Core per generare:</span><span class="sxs-lookup"><span data-stu-id="94f1a-484">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="94f1a-485">Una classe *contesto* di EF Core.</span><span class="sxs-lookup"><span data-stu-id="94f1a-485">An EF Core *context* class.</span></span> <span data-ttu-id="94f1a-486">Il contesto è la classe principale che coordina le funzionalità di Entity Framework per un determinato modello di dati.</span><span class="sxs-lookup"><span data-stu-id="94f1a-486">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="94f1a-487">Deriva dalla classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-487">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="94f1a-488">:::no-loc(Razor)::: pagine che gestiscono le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD) per l' `Student` entità.</span><span class="sxs-lookup"><span data-stu-id="94f1a-488">:::no-loc(Razor)::: pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94f1a-489">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94f1a-489">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94f1a-490">Creare una cartella *Students* nella cartella *Pages*.</span><span class="sxs-lookup"><span data-stu-id="94f1a-490">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="94f1a-491">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Pages/Students* e quindi scegliere **Aggiungi** > **Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-491">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="94f1a-492">Nella finestra di dialogo **Aggiungi impalcatura** selezionare **:::no-loc(Razor)::: pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-492">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="94f1a-493">Nella finestra di dialogo **Aggiungi :::no-loc(Razor)::: pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="94f1a-493">In the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="94f1a-494">Nell'elenco a discesa **Classe modello** selezionare **Student (ContosoUniversity.Models)**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-494">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="94f1a-495">Nella riga **Classe contesto di dati** selezionare il segno più **+**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-495">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="94f1a-496">Modificare il nome del contesto di dati da *ContosoUniversity.Models.ContosoUniversityContext* a *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="94f1a-496">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="94f1a-497">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-497">Select **Add**.</span></span>

<span data-ttu-id="94f1a-498">Vengono installati automaticamente i pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="94f1a-498">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="94f1a-499">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94f1a-499">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="94f1a-500">Eseguire i comandi seguenti dell'interfaccia della riga di comando di .NET Core per installare i pacchetti NuGet necessari:</span><span class="sxs-lookup"><span data-stu-id="94f1a-500">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="94f1a-501">Per lo scaffolding è necessario il pacchetto Microsoft.VisualStudio.Web.CodeGeneration.Design.</span><span class="sxs-lookup"><span data-stu-id="94f1a-501">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="94f1a-502">Sebbene l'app non usi SQL Server, lo strumento di scaffolding richiede il pacchetto di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="94f1a-502">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="94f1a-503">Creare una cartella *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="94f1a-503">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="94f1a-504">Eseguire il comando seguente per installare lo [strumento di scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="94f1a-504">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="94f1a-505">Eseguire il comando seguente per eseguire lo scaffolding delle pagine Student.</span><span class="sxs-lookup"><span data-stu-id="94f1a-505">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="94f1a-506">**In Windows**</span><span class="sxs-lookup"><span data-stu-id="94f1a-506">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="94f1a-507">**In macOS o Linux**</span><span class="sxs-lookup"><span data-stu-id="94f1a-507">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="94f1a-508">Se si riscontra un problema con il passaggio precedente, compilare il progetto e riprovare a eseguire il passaggio di scaffolding.</span><span class="sxs-lookup"><span data-stu-id="94f1a-508">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="94f1a-509">Il processo di scaffolding:</span><span class="sxs-lookup"><span data-stu-id="94f1a-509">The scaffolding process:</span></span>

* <span data-ttu-id="94f1a-510">Crea :::no-loc(Razor)::: pagine nella cartella *pages/students* :</span><span class="sxs-lookup"><span data-stu-id="94f1a-510">Creates :::no-loc(Razor)::: pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="94f1a-511">*Create.cshtml* e *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="94f1a-511">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="94f1a-512">*Delete.cshtml* e *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="94f1a-512">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="94f1a-513">*Details.cshtml* e *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="94f1a-513">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="94f1a-514">*Edit.cshtml* e *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="94f1a-514">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="94f1a-515">*Index.cshtml* e *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="94f1a-515">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="94f1a-516">Crea *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="94f1a-516">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="94f1a-517">Aggiunge il contesto all'inserimento delle dipendenze in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="94f1a-517">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="94f1a-518">Aggiunge una stringa di connessione al database a *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="94f1a-518">Adds a database connection string to *:::no-loc(appsettings.json):::*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="94f1a-519">Stringa di connessione al database</span><span class="sxs-lookup"><span data-stu-id="94f1a-519">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94f1a-520">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94f1a-520">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="94f1a-521">Il *:::no-loc(appsettings.json):::* file specifica la stringa di connessione [SQL Server database locale](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="94f1a-521">The *:::no-loc(appsettings.json):::* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/:::no-loc(appsettings.json):::?highlight=11)]

<span data-ttu-id="94f1a-522">Local DB è una versione leggera del motore di database di SQL Server Express appositamente pensato per lo sviluppo di app e non per la produzione.</span><span class="sxs-lookup"><span data-stu-id="94f1a-522">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="94f1a-523">Per impostazione predefinita, Local DB crea file con estensione *mdf* nella directory `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-523">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="94f1a-524">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94f1a-524">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="94f1a-525">Modificare la stringa di connessione in modo che punti a un file di database SQLite denominato *CU.db* :</span><span class="sxs-lookup"><span data-stu-id="94f1a-525">Change the connection string to point to a SQLite database file named *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="94f1a-526">Aggiornare la classe del contesto di database</span><span class="sxs-lookup"><span data-stu-id="94f1a-526">Update the database context class</span></span>

<span data-ttu-id="94f1a-527">La classe principale che coordina le funzionalità di EF Core per un determinato modello di dati è la classe del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-527">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="94f1a-528">Il contesto è derivato da [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="94f1a-528">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="94f1a-529">Il contesto specifica le entità incluse nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="94f1a-529">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="94f1a-530">In questo progetto la classe è denominata `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-530">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="94f1a-531">Aggiornare *Data/SchoolContext.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="94f1a-531">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="94f1a-532">Il codice evidenziato crea una proprietà [DbSet \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per ogni set di entità.</span><span class="sxs-lookup"><span data-stu-id="94f1a-532">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="94f1a-533">Nella terminologia di Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="94f1a-533">In EF Core terminology:</span></span>

* <span data-ttu-id="94f1a-534">Un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-534">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="94f1a-535">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="94f1a-535">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="94f1a-536">Dato che un set di entità contiene più entità, le proprietà DBSet devono essere nomi plurali.</span><span class="sxs-lookup"><span data-stu-id="94f1a-536">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="94f1a-537">Dato che lo strumento di scaffolding ha creato un DBSet `Student`, questo passaggio lo modifica nel plurale `Students`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-537">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="94f1a-538">Per fare in modo che il :::no-loc(Razor)::: codice delle pagine corrisponda al nuovo nome del DBSet, apportare una modifica globale nell'intero progetto di `_context.Student` a `_context.Students` .</span><span class="sxs-lookup"><span data-stu-id="94f1a-538">To make the :::no-loc(Razor)::: Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="94f1a-539">Sono presenti 8 occorrenze.</span><span class="sxs-lookup"><span data-stu-id="94f1a-539">There are 8 occurrences.</span></span>

<span data-ttu-id="94f1a-540">Compilare il progetto per verificare che non siano presenti errori di compilazione.</span><span class="sxs-lookup"><span data-stu-id="94f1a-540">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="94f1a-541">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="94f1a-541">Startup.cs</span></span>

<span data-ttu-id="94f1a-542">ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="94f1a-542">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="94f1a-543">I servizi, ad esempio il contesto di database di EF Core, vengono registrati con l'inserimento delle dipendenze durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="94f1a-543">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="94f1a-544">I componenti che richiedono questi servizi, ad esempio :::no-loc(Razor)::: le pagine, vengono forniti tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="94f1a-544">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="94f1a-545">Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-545">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="94f1a-546">Lo strumento di scaffolding ha registrato automaticamente la classe di contesto nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="94f1a-546">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94f1a-547">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94f1a-547">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94f1a-548">In `ConfigureServices` le righe evidenziate sono state aggiunte dallo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="94f1a-548">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="94f1a-549">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94f1a-549">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="94f1a-550">In `ConfigureServices` assicurarsi che il codice aggiunto dallo scaffolder chiami `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-550">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="94f1a-551">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="94f1a-551">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="94f1a-552">Per lo sviluppo locale, il [sistema di configurazione ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal *:::no-loc(appsettings.json):::* file.</span><span class="sxs-lookup"><span data-stu-id="94f1a-552">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="94f1a-553">Creare il database</span><span class="sxs-lookup"><span data-stu-id="94f1a-553">Create the database</span></span>

<span data-ttu-id="94f1a-554">Aggiornare *Program.cs* per creare il database se non esiste:</span><span class="sxs-lookup"><span data-stu-id="94f1a-554">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="94f1a-555">Il metodo [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) non esegue alcuna azione se esiste un database per il contesto.</span><span class="sxs-lookup"><span data-stu-id="94f1a-555">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="94f1a-556">Se non esiste alcun database, vengono creati il database e lo schema.</span><span class="sxs-lookup"><span data-stu-id="94f1a-556">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="94f1a-557">`EnsureCreated` abilita il flusso di lavoro seguente per la gestione delle modifiche al modello di dati:</span><span class="sxs-lookup"><span data-stu-id="94f1a-557">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="94f1a-558">Eliminare il database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-558">Delete the database.</span></span> <span data-ttu-id="94f1a-559">Tutti i dati esistenti andranno perduti.</span><span class="sxs-lookup"><span data-stu-id="94f1a-559">Any existing data is lost.</span></span>
* <span data-ttu-id="94f1a-560">Modificare il modello di dati.</span><span class="sxs-lookup"><span data-stu-id="94f1a-560">Change the data model.</span></span> <span data-ttu-id="94f1a-561">Ad esempio, aggiungere un campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-561">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="94f1a-562">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="94f1a-562">Run the app.</span></span>
* <span data-ttu-id="94f1a-563">`EnsureCreated` crea un database con il nuovo schema.</span><span class="sxs-lookup"><span data-stu-id="94f1a-563">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="94f1a-564">Questo flusso di lavoro funziona correttamente nelle fasi iniziali dello sviluppo quando lo schema è in rapida evoluzione, purché non sia necessario conservare i dati.</span><span class="sxs-lookup"><span data-stu-id="94f1a-564">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="94f1a-565">La situazione è diversa quando è necessario mantenere i dati immessi nel database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-565">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="94f1a-566">In tal caso, usare le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="94f1a-566">When that is the case, use migrations.</span></span>

<span data-ttu-id="94f1a-567">Più avanti nella serie di esercitazioni si vedrà come eliminare il database creato da `EnsureCreated` e usare invece le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="94f1a-567">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="94f1a-568">Non è possibile aggiornare un database creato da `EnsureCreated` usando le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="94f1a-568">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="94f1a-569">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="94f1a-569">Test the app</span></span>

* <span data-ttu-id="94f1a-570">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="94f1a-570">Run the app.</span></span>
* <span data-ttu-id="94f1a-571">Selezionare il collegamento **Students** (Studenti) e quindi **Crea nuovo**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-571">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="94f1a-572">Eseguire il test dei collegamenti Edit (Modifica), Details (Dettagli) e Delete (Elimina).</span><span class="sxs-lookup"><span data-stu-id="94f1a-572">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="94f1a-573">Specificare il valore di inizializzazione del database</span><span class="sxs-lookup"><span data-stu-id="94f1a-573">Seed the database</span></span>

<span data-ttu-id="94f1a-574">Il metodo `EnsureCreated` crea un database vuoto.</span><span class="sxs-lookup"><span data-stu-id="94f1a-574">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="94f1a-575">In questa sezione viene aggiunto il codice che popola il database con dati di test.</span><span class="sxs-lookup"><span data-stu-id="94f1a-575">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="94f1a-576">Creare *Data/DbInitializer.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="94f1a-576">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="94f1a-577">Il codice controlla se esistono studenti nel database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-577">The code checks if there are any students in the database.</span></span> <span data-ttu-id="94f1a-578">Se non sono presenti studenti, aggiunge i dati di test al database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-578">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="94f1a-579">I dati di test vengono creati in matrici anziché in raccolte `List<T>` per ottimizzare le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="94f1a-579">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="94f1a-580">In *Program.cs* sostituire la chiamata di `EnsureCreated` con una chiamata di `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="94f1a-580">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="94f1a-581">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94f1a-581">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="94f1a-582">Arrestare l'app se è in esecuzione ed eseguire il comando seguente nella **console di Gestione pacchetti** :</span><span class="sxs-lookup"><span data-stu-id="94f1a-582">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="94f1a-583">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94f1a-583">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="94f1a-584">Arrestare l'app se è in esecuzione ed eliminare il file *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="94f1a-584">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="94f1a-585">Riavviare l'app.</span><span class="sxs-lookup"><span data-stu-id="94f1a-585">Restart the app.</span></span>

* <span data-ttu-id="94f1a-586">Selezionare la pagina Students per visualizzare i dati di seeding.</span><span class="sxs-lookup"><span data-stu-id="94f1a-586">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="94f1a-587">Visualizzare il database</span><span class="sxs-lookup"><span data-stu-id="94f1a-587">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94f1a-588">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94f1a-588">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94f1a-589">Aprire **Esplora oggetti di SQL Server** dal menu **Visualizza** in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="94f1a-589">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="94f1a-590">In Esplora oggetti di SQL Server selezionare **(localdb)\MSSQLLocalDB > Database > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-590">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="94f1a-591">Il nome del database viene generato dal nome del contesto specificato in precedenza con l'aggiunta di un trattino e un GUID.</span><span class="sxs-lookup"><span data-stu-id="94f1a-591">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="94f1a-592">Espandere il nodo **Tabelle**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-592">Expand the **Tables** node.</span></span>
* <span data-ttu-id="94f1a-593">Fare clic con il pulsante destro del mouse sulla tabella **Student** (Studente) e fare clic su **Visualizza dati** per visualizzare le colonne create e le righe inserite nella tabella.</span><span class="sxs-lookup"><span data-stu-id="94f1a-593">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="94f1a-594">Fare clic con il pulsante destro del mouse sulla tabella **Student** e scegliere **Visualizza codice** per vedere il mapping tra il modello `Student` e lo schema della tabella `Student`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-594">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="94f1a-595">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94f1a-595">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="94f1a-596">Usare lo strumento SQLite per visualizzare lo schema del database e i dati di seeding.</span><span class="sxs-lookup"><span data-stu-id="94f1a-596">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="94f1a-597">Il file di database è denominato *CU.db* e si trova nella cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="94f1a-597">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="94f1a-598">Codice asincrono</span><span class="sxs-lookup"><span data-stu-id="94f1a-598">Asynchronous code</span></span>

<span data-ttu-id="94f1a-599">La programmazione asincrona è la modalità predefinita per ASP.NET Core ed Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="94f1a-599">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="94f1a-600">Per un server Web è disponibile un numero limitato di thread e in situazioni di carico elevato tutti i thread disponibili potrebbero essere in uso.</span><span class="sxs-lookup"><span data-stu-id="94f1a-600">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="94f1a-601">In queste circostanze il server non può elaborare nuove richieste finché i thread non saranno liberi.</span><span class="sxs-lookup"><span data-stu-id="94f1a-601">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="94f1a-602">Con il codice sincrono, può succedere che molti thread siano vincolati nonostante in quel momento non stiano eseguendo alcuna operazione. Rimangono tuttavia in attesa che l'operazione I/O sia completata.</span><span class="sxs-lookup"><span data-stu-id="94f1a-602">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="94f1a-603">Con il codice asincrono, se un processo è in attesa del completamento dell'operazione I/O, il thread viene liberato e il server lo può usare per l'elaborazione di altre richieste.</span><span class="sxs-lookup"><span data-stu-id="94f1a-603">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="94f1a-604">Di conseguenza, il codice asincrono consente un uso più efficiente delle risorse del server e il server può gestire una maggiore quantità di traffico senza ritardi.</span><span class="sxs-lookup"><span data-stu-id="94f1a-604">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="94f1a-605">Il codice asincrono comporta un minimo sovraccarico in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="94f1a-605">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="94f1a-606">In caso di traffico ridotto, il calo delle prestazioni è irrilevante, mentre nelle situazioni di traffico elevato, è essenziale ottimizzare le prestazioni potenziali.</span><span class="sxs-lookup"><span data-stu-id="94f1a-606">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="94f1a-607">Nel codice seguente la parola chiave [async](/dotnet/csharp/language-reference/keywords/async), il valore restituito `Task<T>`, la parola chiave `await` e il metodo `ToListAsync` consentono di eseguire il codice in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="94f1a-607">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="94f1a-608">La parola chiave `async` indica al compilatore di eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="94f1a-608">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="94f1a-609">Generare callback per parti del corpo del metodo.</span><span class="sxs-lookup"><span data-stu-id="94f1a-609">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="94f1a-610">Creare l'oggetto [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) restituito.</span><span class="sxs-lookup"><span data-stu-id="94f1a-610">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="94f1a-611">Il tipo restituito `Task<T>` rappresenta il lavoro in corso.</span><span class="sxs-lookup"><span data-stu-id="94f1a-611">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="94f1a-612">La parola chiave `await` indica al compilatore di suddividere il metodo in due parti.</span><span class="sxs-lookup"><span data-stu-id="94f1a-612">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="94f1a-613">La prima parte termina con l'operazione avviata in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="94f1a-613">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="94f1a-614">La seconda parte viene inserita in un metodo di callback che viene chiamato al termine dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="94f1a-614">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="94f1a-615">`ToListAsync` è la versione asincrona del metodo di estensione `ToList`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-615">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="94f1a-616">Alcuni aspetti da considerare quando si scrive codice asincrono usato da Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="94f1a-616">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="94f1a-617">Solo le istruzioni che generano query o comandi da inviare al database vengono eseguite in modo asincrono,</span><span class="sxs-lookup"><span data-stu-id="94f1a-617">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="94f1a-618">Sono incluse `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-618">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="94f1a-619">Non sono incluse le istruzioni che modificano solo un oggetto `IQueryable`, ad esempio `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-619">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="94f1a-620">Un contesto di Entity Framework Core non è thread-safe. Non tentare di eseguire più operazioni parallelamente.</span><span class="sxs-lookup"><span data-stu-id="94f1a-620">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="94f1a-621">Per sfruttare i vantaggi del codice asincrono in termini di prestazioni, verificare che i pacchetti della libreria impiegati, ad esempio per il paging, usino la modalità asincrona per chiamare i metodi di EF Core che inviano query al database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-621">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="94f1a-622">Per altre informazioni sulla programmazione asincrona in .NET, vedere [Panoramica della programmazione asincrona](/dotnet/standard/async) e [Programmazione asincrona con async e await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="94f1a-622">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="94f1a-623">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="94f1a-623">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="94f1a-624">Esercitazione successiva</span><span class="sxs-lookup"><span data-stu-id="94f1a-624">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="94f1a-625">L'app Web di esempio di Contoso University illustra come creare un' :::no-loc(Razor)::: app ASP.NET Core Pages con Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="94f1a-625">The Contoso University sample web app demonstrates how to create an ASP.NET Core :::no-loc(Razor)::: Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="94f1a-626">L'app di esempio è un sito Web per una fittizia Contoso University.</span><span class="sxs-lookup"><span data-stu-id="94f1a-626">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="94f1a-627">Include funzionalità, come ad esempio l'ammissione di studenti, la creazione di corsi e le assegnazioni di insegnati.</span><span class="sxs-lookup"><span data-stu-id="94f1a-627">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="94f1a-628">Questa pagina è il prima di una serie di esercitazioni che illustrano come compilare l'app di esempio di Contoso University.</span><span class="sxs-lookup"><span data-stu-id="94f1a-628">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="94f1a-629">Scaricare o visualizzare l'app completata.</span><span class="sxs-lookup"><span data-stu-id="94f1a-629">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="94f1a-630">[Istruzioni per il download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="94f1a-630">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="94f1a-631">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="94f1a-631">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94f1a-632">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94f1a-632">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="94f1a-633">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94f1a-633">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="94f1a-634">Familiarità con le [ :::no-loc(Razor)::: pagine](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="94f1a-634">Familiarity with [:::no-loc(Razor)::: Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="94f1a-635">Prima di iniziare questa serie, i nuovi programmatori dovranno completare l' [Introduzione alle :::no-loc(Razor)::: pagine](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="94f1a-635">New programmers should complete [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="94f1a-636">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="94f1a-636">Troubleshooting</span></span>

<span data-ttu-id="94f1a-637">Se si verifica un problema che non si sa come risolvere, è generalmente possibile trovare la soluzione confrontando il codice con il [progetto completato](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="94f1a-637">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="94f1a-638">Un buon metodo per ottenere assistenza è quello di pubblicare una domanda in [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) per [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="94f1a-638">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="94f1a-639">App Web di Contoso University</span><span class="sxs-lookup"><span data-stu-id="94f1a-639">The Contoso University web app</span></span>

<span data-ttu-id="94f1a-640">L'applicazione compilata in queste esercitazioni è il sito Web di base di un'università.</span><span class="sxs-lookup"><span data-stu-id="94f1a-640">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="94f1a-641">Gli utenti possono visualizzare e aggiornare le informazioni che riguardano studenti, corsi e insegnanti.</span><span class="sxs-lookup"><span data-stu-id="94f1a-641">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="94f1a-642">Di seguito sono disponibili alcune schermate dell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="94f1a-642">Here are a few of the screens created in the tutorial.</span></span>

![Pagina Student Index (Indice degli studenti)](intro/_static/students-index.png)

![Pagina Students Edit (Modifica studenti)](intro/_static/student-edit.png)

<span data-ttu-id="94f1a-645">Lo stile dell'interfaccia utente del sito è simile a quanto è stato generato tramite i modelli predefiniti.</span><span class="sxs-lookup"><span data-stu-id="94f1a-645">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="94f1a-646">Lo stato attivo dell'esercitazione è EF Core con le :::no-loc(Razor)::: pagine, non con l'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="94f1a-646">The tutorial focus is on EF Core with :::no-loc(Razor)::: Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="94f1a-647">Creare l' :::no-loc(Razor)::: app Web ContosoUniversity Pages</span><span class="sxs-lookup"><span data-stu-id="94f1a-647">Create the ContosoUniversity :::no-loc(Razor)::: Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94f1a-648">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94f1a-648">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94f1a-649">Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-649">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="94f1a-650">Creare una nuova applicazione Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="94f1a-650">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="94f1a-651">Denominare il progetto **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-651">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="94f1a-652">È importante denominare il progetto *ContosoUniversity* in modo che gli spazi dei nomi corrispondano quando il codice viene copiato/incollato.</span><span class="sxs-lookup"><span data-stu-id="94f1a-652">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="94f1a-653">Selezionare **ASP.NET Core 2.1** nell'elenco a discesa, quindi selezionare **Applicazione Web**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-653">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="94f1a-654">Per le immagini dei passaggi precedenti, vedere [creare un' :::no-loc(Razor)::: app Web](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="94f1a-654">For images of the preceding steps, see [Create a :::no-loc(Razor)::: web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="94f1a-655">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="94f1a-655">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="94f1a-656">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94f1a-656">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="94f1a-657">Impostare lo stile del sito</span><span class="sxs-lookup"><span data-stu-id="94f1a-657">Set up the site style</span></span>

<span data-ttu-id="94f1a-658">Con alcune modifiche è possibile impostare il menu del sito, il layout e la home page.</span><span class="sxs-lookup"><span data-stu-id="94f1a-658">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="94f1a-659">Aggiornare *Pages/Shared/_Layout.cshtml* con le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="94f1a-659">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="94f1a-660">Modificare tutte le occorrenze di "ContosoUniversity" in "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="94f1a-660">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="94f1a-661">Le occorrenze sono tre.</span><span class="sxs-lookup"><span data-stu-id="94f1a-661">There are three occurrences.</span></span>

* <span data-ttu-id="94f1a-662">Aggiungere le voci di menu per **Students** (Studenti), **Courses** (Corsi), **Instructors** (Insegnanti) e **Departments** (Dipartimenti) ed eliminare la voce di menu **Contact** (Contatto).</span><span class="sxs-lookup"><span data-stu-id="94f1a-662">Add menu entries for **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="94f1a-663">Le modifiche sono evidenziate.</span><span class="sxs-lookup"><span data-stu-id="94f1a-663">The changes are highlighted.</span></span> <span data-ttu-id="94f1a-664">*Non* viene visualizzato l'intero markup.</span><span class="sxs-lookup"><span data-stu-id="94f1a-664">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="94f1a-665">In *Pages/Index.cshtml* sostituire il contenuto del file con il codice seguente. In questo modo il testo su ASP.NET e MVC sarà sostituito dal testo relativo a questa app:</span><span class="sxs-lookup"><span data-stu-id="94f1a-665">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="94f1a-666">Creare il modello di dati</span><span class="sxs-lookup"><span data-stu-id="94f1a-666">Create the data model</span></span>

<span data-ttu-id="94f1a-667">Creare le classi delle entità per l'app di Contoso University.</span><span class="sxs-lookup"><span data-stu-id="94f1a-667">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="94f1a-668">Iniziare con le tre entità seguenti:</span><span class="sxs-lookup"><span data-stu-id="94f1a-668">Start with the following three entities:</span></span>

![Diagramma modello di dati Course/Enrollment/Student (Corso/Iscrizione/Studente)](intro/_static/data-model-diagram.png)

<span data-ttu-id="94f1a-670">Esiste una relazione uno-a-molti tra le entità `Student` e `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-670">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="94f1a-671">Esiste una relazione uno-a-molti tra le entità `Course` e `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-671">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="94f1a-672">Uno studente può iscriversi a un numero qualsiasi di corsi.</span><span class="sxs-lookup"><span data-stu-id="94f1a-672">A student can enroll in any number of courses.</span></span> <span data-ttu-id="94f1a-673">Un corso può avere un numero qualsiasi di studenti iscritti.</span><span class="sxs-lookup"><span data-stu-id="94f1a-673">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="94f1a-674">Nelle sezioni seguenti viene creata una classe per ognuna di queste entità.</span><span class="sxs-lookup"><span data-stu-id="94f1a-674">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="94f1a-675">Entità Student (Studente)</span><span class="sxs-lookup"><span data-stu-id="94f1a-675">The Student entity</span></span>

![Diagramma entità Student (Studente)](intro/_static/student-entity.png)

<span data-ttu-id="94f1a-677">Creare una cartella *Models* (Modelli).</span><span class="sxs-lookup"><span data-stu-id="94f1a-677">Create a *Models* folder.</span></span> <span data-ttu-id="94f1a-678">Nella cartella *Models* (Modelli) creare un file di classe denominato *Student.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="94f1a-678">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="94f1a-679">La proprietà `ID` diventa la colonna di chiave primaria della tabella di database (DB) che corrisponde a questa classe.</span><span class="sxs-lookup"><span data-stu-id="94f1a-679">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="94f1a-680">Per impostazione predefinita, Entity Framework Core interpreta una proprietà denominata `ID` o `classnameID` come chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="94f1a-680">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="94f1a-681">In `classnameID``classname` è il nome della classe.</span><span class="sxs-lookup"><span data-stu-id="94f1a-681">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="94f1a-682">La chiave primaria alternativa riconosciuta automaticamente è `StudentID` nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="94f1a-682">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="94f1a-683">La proprietà `Enrollments` rappresenta una [proprietà di navigazione](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="94f1a-683">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="94f1a-684">Le proprietà di navigazione si collegano ad altre entità correlate a questa entità.</span><span class="sxs-lookup"><span data-stu-id="94f1a-684">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="94f1a-685">In questo caso la proprietà `Enrollments` di `Student entity` contiene tutte le entità `Enrollment` correlate a `Student`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-685">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="94f1a-686">Ad esempio, se una riga Student (Studente) nella database presenta due righe Enrollment (Iscrizione) correlate, la proprietà di navigazione `Enrollments` contiene questi due entità `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-686">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="94f1a-687">Una riga `Enrollment` correlata è una riga che contiene il valore della chiave primaria dello studente nella colonna `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-687">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="94f1a-688">Si supponga ad esempio che per lo studente con ID = 1 siano presenti due righe nella tabella `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-688">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="94f1a-689">La tabella `Enrollment` contiene due righe con `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="94f1a-689">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="94f1a-690">`StudentID` è una chiave esterna nella tabella `Enrollment` che specifica lo studente nella tabella `Student`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-690">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="94f1a-691">Se una proprietà di navigazione può contenere più entità, deve essere un tipo elenco, ad esempio `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-691">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="94f1a-692">È possibile specificare `ICollection<T>` o un tipo, ad esempio `List<T>` o `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-692">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="94f1a-693">Se si specifica `ICollection<T>`, per impostazione predefinita Entity Framework Core crea una raccolta `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-693">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="94f1a-694">Le proprietà di navigazione che contengono più entità provengono da relazioni molti-a-molti e uno-a-molti.</span><span class="sxs-lookup"><span data-stu-id="94f1a-694">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="94f1a-695">Entità Enrollment (Iscrizione)</span><span class="sxs-lookup"><span data-stu-id="94f1a-695">The Enrollment entity</span></span>

![Diagramma entità Enrollment (Iscrizione)](intro/_static/enrollment-entity.png)

<span data-ttu-id="94f1a-697">Nella cartella *Models* (Modelli) creare un file *Enrollment.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="94f1a-697">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="94f1a-698">La proprietà `EnrollmentID` è la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="94f1a-698">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="94f1a-699">Questa entità usa il criterio `classnameID` anziché `ID` come l'entità `Student`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-699">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="94f1a-700">In genere gli sviluppatori scelgono un criterio che usano poi in tutto il modello di dati.</span><span class="sxs-lookup"><span data-stu-id="94f1a-700">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="94f1a-701">In un'esercitazione successiva viene illustrato l'uso di ID senza classname. In questo modo si semplifica l'implementazione dell'ereditarietà nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="94f1a-701">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="94f1a-702">La proprietà `Grade` è un oggetto`enum`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-702">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="94f1a-703">Il punto interrogativo dopo la dichiarazione del tipo `Grade` indica che la proprietà `Grade` ammette i valori Null.</span><span class="sxs-lookup"><span data-stu-id="94f1a-703">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="94f1a-704">Un voto il cui valore è Null è diverso da un voto con valore zero. Null significa che un voto è sconosciuto o non è stato ancora assegnato.</span><span class="sxs-lookup"><span data-stu-id="94f1a-704">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="94f1a-705">La proprietà `StudentID` è una chiave esterna e la proprietà di navigazione corrispondente è `Student`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-705">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="94f1a-706">Un'entità `Enrollment` è associata a un'entità `Student`, pertanto la proprietà contiene un'unica entità `Student`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-706">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="94f1a-707">L'entità `Student` si differenzia dalla proprietà di navigazione `Student.Enrollments` che contiene più entità `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-707">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="94f1a-708">La proprietà `CourseID` è una chiave esterna e la proprietà di navigazione corrispondente è `Course`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-708">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="94f1a-709">Un'entità `Enrollment` è associata a un'entità `Course`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-709">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="94f1a-710">Entity Framework Core interpreta una proprietà come chiave esterna se è denominata `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-710">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="94f1a-711">Ad esempio, `StudentID` per la proprietà di navigazione `Student`, poiché la chiave primaria `Student` dell'entità è `ID`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-711">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="94f1a-712">Le proprietà di chiave esterna possono anche essere denominate `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-712">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="94f1a-713">Ad esempio, `CourseID` poiché la chiave primaria `Course` dell'entità è `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-713">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="94f1a-714">Entità Course (Corso)</span><span class="sxs-lookup"><span data-stu-id="94f1a-714">The Course entity</span></span>

![Diagramma entità Course (Corso)](intro/_static/course-entity.png)

<span data-ttu-id="94f1a-716">Nella cartella *Models* (Modelli) creare un file *Course.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="94f1a-716">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="94f1a-717">La proprietà `Enrollments` rappresenta una proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="94f1a-717">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="94f1a-718">È possibile correlare un'entità `Course` a un numero qualsiasi di entità `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-718">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="94f1a-719">L'attributo `DatabaseGenerated` consente all'app di specificare la chiave primaria anziché chiedere al database di generarla.</span><span class="sxs-lookup"><span data-stu-id="94f1a-719">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="94f1a-720">Eseguire lo scaffolding del modello Student (Studente)</span><span class="sxs-lookup"><span data-stu-id="94f1a-720">Scaffold the student model</span></span>

<span data-ttu-id="94f1a-721">In questa sezione viene eseguito lo scaffolding del modello Student (Studente).</span><span class="sxs-lookup"><span data-stu-id="94f1a-721">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="94f1a-722">Lo strumento di scaffolding crea quindi le pagine per le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) per il modello Student (Studente).</span><span class="sxs-lookup"><span data-stu-id="94f1a-722">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="94f1a-723">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="94f1a-723">Build the project.</span></span>
* <span data-ttu-id="94f1a-724">Creare la cartella *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="94f1a-724">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94f1a-725">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94f1a-725">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94f1a-726">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Pages/Students* > **Aggiungi** > **Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-726">In **Solution Explorer** , right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="94f1a-727">Nella finestra di dialogo **Aggiungi impalcatura** selezionare **:::no-loc(Razor)::: pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-727">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="94f1a-728">Completare la finestra di dialogo **Aggiungi :::no-loc(Razor)::: pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="94f1a-728">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="94f1a-729">Nell'elenco a discesa **Classe modello** selezionare **Student (ContosoUniversity.Models)**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-729">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="94f1a-730">Nella riga **Classe contesto di dati** selezionare il segno **+** (più) e modificare il nome generato in **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-730">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="94f1a-731">Nell'elenco a discesa **Classe contesto di dati** selezionare **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-731">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="94f1a-732">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-732">Select **Add**.</span></span>

![Finestra di dialogo CRUD](intro/_static/s1.png)

<span data-ttu-id="94f1a-734">Vedere [Eseguire lo scaffolding del modello di filmato](xref:tutorials/razor-pages/model#scaffold-the-movie-model) se si riscontrano problemi con il passaggio precedente.</span><span class="sxs-lookup"><span data-stu-id="94f1a-734">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="94f1a-735">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94f1a-735">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="94f1a-736">Eseguire i comandi seguenti per eseguire lo scaffolding del modello Student (Studente).</span><span class="sxs-lookup"><span data-stu-id="94f1a-736">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="94f1a-737">Il processo di scaffolding ha creato e modificato i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="94f1a-737">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="94f1a-738">File creati</span><span class="sxs-lookup"><span data-stu-id="94f1a-738">Files created</span></span>

* <span data-ttu-id="94f1a-739">Pagine Create (Crea), Delete (Elimina), Details (Dettagli), Edit (Modifica) e Index (Indice) di *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="94f1a-739">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="94f1a-740">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="94f1a-740">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="94f1a-741">Aggiornamenti dei file</span><span class="sxs-lookup"><span data-stu-id="94f1a-741">File updates</span></span>

* <span data-ttu-id="94f1a-742">*Startup.cs* : le modifiche a questo file sono descritte in dettaglio nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="94f1a-742">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="94f1a-743">*:::no-loc(appsettings.json):::* : Viene aggiunta la stringa di connessione utilizzata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="94f1a-743">*:::no-loc(appsettings.json):::* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="94f1a-744">Esaminare il contesto registrato con l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="94f1a-744">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="94f1a-745">ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="94f1a-745">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="94f1a-746">I servizi, ad esempio il contesto di database di Entity Framework Core, vengono registrati tramite dependency injection durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="94f1a-746">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="94f1a-747">I componenti che richiedono questi servizi, ad esempio :::no-loc(Razor)::: le pagine, vengono forniti tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="94f1a-747">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="94f1a-748">Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-748">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="94f1a-749">Lo strumento di scaffolding ha creato automaticamente un contesto del database e lo ha registrato con il contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="94f1a-749">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="94f1a-750">Esaminare il metodo `ConfigureServices` in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="94f1a-750">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="94f1a-751">La riga evidenziata è stata aggiunta dallo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="94f1a-751">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="94f1a-752">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="94f1a-752">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="94f1a-753">Per lo sviluppo locale, il [sistema di configurazione ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal *:::no-loc(appsettings.json):::* file.</span><span class="sxs-lookup"><span data-stu-id="94f1a-753">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="94f1a-754">Aggiornare il metodo Main</span><span class="sxs-lookup"><span data-stu-id="94f1a-754">Update main</span></span>

<span data-ttu-id="94f1a-755">In *Program.cs* modificare il metodo `Main` per eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="94f1a-755">In *Program.cs* , modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="94f1a-756">Ottenere un'istanza del contesto di database dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="94f1a-756">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="94f1a-757">Chiamare [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="94f1a-757">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="94f1a-758">Eliminare il contesto dopo che il metodo `EnsureCreated` è stato completato.</span><span class="sxs-lookup"><span data-stu-id="94f1a-758">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="94f1a-759">Il codice seguente illustra il file *Program.cs* aggiornato.</span><span class="sxs-lookup"><span data-stu-id="94f1a-759">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="94f1a-760">`EnsureCreated` assicura che esista il database per il contesto.</span><span class="sxs-lookup"><span data-stu-id="94f1a-760">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="94f1a-761">Se esiste, non viene eseguita alcuna azione.</span><span class="sxs-lookup"><span data-stu-id="94f1a-761">If it exists, no action is taken.</span></span> <span data-ttu-id="94f1a-762">Se non esiste, vengono creati il database e tutti i relativi schemi.</span><span class="sxs-lookup"><span data-stu-id="94f1a-762">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="94f1a-763">`EnsureCreated` non usa le migrazioni per creare il database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-763">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="94f1a-764">Un database creato con `EnsureCreated` non potrà essere aggiornato successivamente usando le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="94f1a-764">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="94f1a-765">`EnsureCreated` viene chiamato all'avvio dell'app e consente il flusso di lavoro seguente:</span><span class="sxs-lookup"><span data-stu-id="94f1a-765">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="94f1a-766">Eliminare il database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-766">Delete the DB.</span></span>
* <span data-ttu-id="94f1a-767">Modificare lo schema di database, ad esempio, aggiungere un campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-767">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="94f1a-768">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="94f1a-768">Run the app.</span></span>
* <span data-ttu-id="94f1a-769">`EnsureCreated` crea un database con la colonna `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-769">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="94f1a-770">`EnsureCreated` è utile nelle prime fasi di sviluppo quando lo schema è in rapida evoluzione.</span><span class="sxs-lookup"><span data-stu-id="94f1a-770">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="94f1a-771">Più avanti nell'esercitazione il database verrà eliminato e si useranno le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="94f1a-771">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="94f1a-772">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="94f1a-772">Test the app</span></span>

<span data-ttu-id="94f1a-773">Eseguire l'app e accettare i :::no-loc(cookie)::: criteri.</span><span class="sxs-lookup"><span data-stu-id="94f1a-773">Run the app and accept the :::no-loc(cookie)::: policy.</span></span> <span data-ttu-id="94f1a-774">Questa app non memorizza informazioni personali.</span><span class="sxs-lookup"><span data-stu-id="94f1a-774">This app doesn't keep personal information.</span></span> <span data-ttu-id="94f1a-775">Per informazioni sui criteri, vedere la pagina relativa :::no-loc(cookie)::: al [supporto per regolamento generale sulla protezione dei dati UE (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="94f1a-775">You can read about the :::no-loc(cookie)::: policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="94f1a-776">Selezionare il collegamento **Students** (Studenti) e quindi **Crea nuovo**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-776">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="94f1a-777">Eseguire il test dei collegamenti Edit (Modifica), Details (Dettagli) e Delete (Elimina).</span><span class="sxs-lookup"><span data-stu-id="94f1a-777">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="94f1a-778">Esaminare il contesto di database SchoolContext</span><span class="sxs-lookup"><span data-stu-id="94f1a-778">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="94f1a-779">La classe principale che coordina le funzionalità di Entity Framework Core per un determinato modello di dati è la classe del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-779">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="94f1a-780">Il contesto dei dati è derivato da [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="94f1a-780">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="94f1a-781">Il contesto dei dati specifica le entità incluse nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="94f1a-781">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="94f1a-782">In questo progetto la classe è denominata `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-782">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="94f1a-783">Aggiornare *SchoolContext.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="94f1a-783">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="94f1a-784">Il codice evidenziato crea una proprietà [DbSet \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per ogni set di entità.</span><span class="sxs-lookup"><span data-stu-id="94f1a-784">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="94f1a-785">Nella terminologia di Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="94f1a-785">In EF Core terminology:</span></span>

* <span data-ttu-id="94f1a-786">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-786">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="94f1a-787">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="94f1a-787">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="94f1a-788">`DbSet<Enrollment>` e `DbSet<Course>` potrebbero essere omessi.</span><span class="sxs-lookup"><span data-stu-id="94f1a-788">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="94f1a-789">Core Entity Framework le include in modo implicito perché l'entità `Student` fa riferimento all'entità `Enrollment` e l'entità `Enrollment` fa riferimento all'entità `Course`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-789">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="94f1a-790">Per questa esercitazione, mantenere `DbSet<Enrollment>` e `DbSet<Course>` in `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-790">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="94f1a-791">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="94f1a-791">SQL Server Express LocalDB</span></span>

<span data-ttu-id="94f1a-792">La stringa di connessione specifica [SQL Server Local DB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="94f1a-792">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="94f1a-793">Local DB è una versione leggera del motore di database di SQL Server Express appositamente pensato per lo sviluppo di app e non per la produzione.</span><span class="sxs-lookup"><span data-stu-id="94f1a-793">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="94f1a-794">Local DB viene avviato su richiesta ed eseguito in modalità utente; non richiede quindi una configurazione complessa.</span><span class="sxs-lookup"><span data-stu-id="94f1a-794">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="94f1a-795">Per impostazione predefinita, Local DB crea file di database con estensione *mdf* nella directory `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-795">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="94f1a-796">Aggiungere il codice per inizializzare il database con dati di test</span><span class="sxs-lookup"><span data-stu-id="94f1a-796">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="94f1a-797">Entity Framework Core crea un database vuoto.</span><span class="sxs-lookup"><span data-stu-id="94f1a-797">EF Core creates an empty DB.</span></span> <span data-ttu-id="94f1a-798">In questa sezione viene scritto un metodo `Initialize` per popolare il database con i dati di test.</span><span class="sxs-lookup"><span data-stu-id="94f1a-798">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="94f1a-799">Nella cartella *Data* (Dati) creare un nuovo file di classe denominato *DbInitializer.cs* e aggiungere il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="94f1a-799">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="94f1a-800">Nota: il codice precedente USA `Models` per lo spazio dei nomi ( `namespace ContosoUniversity.Models` ) anziché `Data` .</span><span class="sxs-lookup"><span data-stu-id="94f1a-800">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="94f1a-801">`Models` è coerente con il codice generato dallo scaffolder.</span><span class="sxs-lookup"><span data-stu-id="94f1a-801">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="94f1a-802">Per altre informazioni, vedere [questo problema relativo allo scaffolding in GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="94f1a-802">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="94f1a-803">Il codice controlla se esistono studenti nel database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-803">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="94f1a-804">Se il database non contiene studenti, il database viene inizializzato con i dati di test.</span><span class="sxs-lookup"><span data-stu-id="94f1a-804">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="94f1a-805">I dati di test vengono caricati in matrici anziché in raccolte `List<T>` per ottimizzare le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="94f1a-805">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="94f1a-806">Il metodo `EnsureCreated` crea automaticamente il database per il contesto di database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-806">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="94f1a-807">Se il database esiste, `EnsureCreated` restituisce senza modificare il database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-807">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="94f1a-808">In *Program.cs* modificare il metodo `Main` per chiamare `Initialize`:</span><span class="sxs-lookup"><span data-stu-id="94f1a-808">In *Program.cs* , modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="94f1a-809">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94f1a-809">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="94f1a-810">Arrestare l'app se è in esecuzione ed eseguire il comando seguente nella **console di Gestione pacchetti** :</span><span class="sxs-lookup"><span data-stu-id="94f1a-810">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="94f1a-811">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94f1a-811">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="94f1a-812">Arrestare l'app se è in esecuzione ed eliminare il file *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="94f1a-812">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="94f1a-813">Visualizzare il database</span><span class="sxs-lookup"><span data-stu-id="94f1a-813">View the DB</span></span>

<span data-ttu-id="94f1a-814">Il nome del database viene generato dal nome del contesto specificato in precedenza con l'aggiunta di un trattino e un GUID.</span><span class="sxs-lookup"><span data-stu-id="94f1a-814">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="94f1a-815">Di conseguenza, il nome del database sarà "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="94f1a-815">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="94f1a-816">Il GUID sarà diverso per ogni utente.</span><span class="sxs-lookup"><span data-stu-id="94f1a-816">The GUID will be different for each user.</span></span>
<span data-ttu-id="94f1a-817">Aprire **Esplora oggetti di SQL Server** dal menu **Visualizza** in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="94f1a-817">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="94f1a-818">In Esplora oggetti di SQL Server fare clic su **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-818">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="94f1a-819">Espandere il nodo **Tabelle**.</span><span class="sxs-lookup"><span data-stu-id="94f1a-819">Expand the **Tables** node.</span></span>

<span data-ttu-id="94f1a-820">Fare clic con il pulsante destro del mouse sulla tabella **Student** (Studente) e fare clic su **Visualizza dati** per visualizzare le colonne create e le righe inserite nella tabella.</span><span class="sxs-lookup"><span data-stu-id="94f1a-820">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="94f1a-821">Codice asincrono</span><span class="sxs-lookup"><span data-stu-id="94f1a-821">Asynchronous code</span></span>

<span data-ttu-id="94f1a-822">La programmazione asincrona è la modalità predefinita per ASP.NET Core ed Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="94f1a-822">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="94f1a-823">Per un server Web è disponibile un numero limitato di thread e in situazioni di carico elevato tutti i thread disponibili potrebbero essere in uso.</span><span class="sxs-lookup"><span data-stu-id="94f1a-823">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="94f1a-824">In queste circostanze il server non può elaborare nuove richieste finché i thread non saranno liberi.</span><span class="sxs-lookup"><span data-stu-id="94f1a-824">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="94f1a-825">Con il codice sincrono, può succedere che molti thread siano vincolati nonostante in quel momento non stiano eseguendo alcuna operazione. Rimangono tuttavia in attesa che l'operazione I/O sia completata.</span><span class="sxs-lookup"><span data-stu-id="94f1a-825">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="94f1a-826">Con il codice asincrono, se un processo è in attesa del completamento dell'operazione I/O, il thread viene liberato e il server lo può usare per l'elaborazione di altre richieste.</span><span class="sxs-lookup"><span data-stu-id="94f1a-826">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="94f1a-827">Di conseguenza, il codice asincrono consente un uso più efficiente delle risorse e il server è abilitato per gestire una maggiore quantità di traffico senza ritardi.</span><span class="sxs-lookup"><span data-stu-id="94f1a-827">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="94f1a-828">Il codice asincrono comporta un minimo sovraccarico in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="94f1a-828">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="94f1a-829">In caso di traffico ridotto, il calo delle prestazioni è irrilevante, mentre nelle situazioni di traffico elevato, è essenziale ottimizzare le prestazioni potenziali.</span><span class="sxs-lookup"><span data-stu-id="94f1a-829">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="94f1a-830">Nel codice seguente la parola chiave [async](/dotnet/csharp/language-reference/keywords/async), il valore restituito `Task<T>`, la parola chiave `await` e il metodo `ToListAsync` consentono di eseguire il codice in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="94f1a-830">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="94f1a-831">La parola chiave `async` indica al compilatore di eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="94f1a-831">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="94f1a-832">Generare callback per parti del corpo del metodo.</span><span class="sxs-lookup"><span data-stu-id="94f1a-832">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="94f1a-833">Creare automaticamente l' oggetto [Task](/dotnet/api/system.threading.tasks.task) restituito.</span><span class="sxs-lookup"><span data-stu-id="94f1a-833">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="94f1a-834">Per altre informazioni, vedere [Tipo restituito Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="94f1a-834">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="94f1a-835">Il tipo restituito implicito `Task` rappresenta il lavoro in corso.</span><span class="sxs-lookup"><span data-stu-id="94f1a-835">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="94f1a-836">La parola chiave `await` indica al compilatore di suddividere il metodo in due parti.</span><span class="sxs-lookup"><span data-stu-id="94f1a-836">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="94f1a-837">La prima parte termina con l'operazione avviata in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="94f1a-837">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="94f1a-838">La seconda parte viene inserita in un metodo di callback che viene chiamato al termine dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="94f1a-838">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="94f1a-839">`ToListAsync` è la versione asincrona del metodo di estensione `ToList`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-839">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="94f1a-840">Alcuni aspetti da considerare quando si scrive codice asincrono usato da Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="94f1a-840">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="94f1a-841">Solo le istruzioni che generano query o comandi da inviare al database vengono eseguite in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="94f1a-841">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="94f1a-842">Sono incluse `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-842">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="94f1a-843">Non sono incluse le istruzioni che modificano solo un oggetto `IQueryable`, ad esempio `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="94f1a-843">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="94f1a-844">Un contesto di Entity Framework Core non è thread-safe. Non tentare di eseguire più operazioni parallelamente.</span><span class="sxs-lookup"><span data-stu-id="94f1a-844">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="94f1a-845">Per sfruttare i vantaggi del codice asincrono in termini di prestazioni, verificare che i pacchetti della libreria impiegati, ad esempio per il paging, usino la modalità asincrona per chiamare i metodi di Entity Framework Core che generano query da inviare al database.</span><span class="sxs-lookup"><span data-stu-id="94f1a-845">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="94f1a-846">Per altre informazioni sulla programmazione asincrona in .NET, vedere [Panoramica della programmazione asincrona](/dotnet/standard/async) e [Programmazione asincrona con async e await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="94f1a-846">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="94f1a-847">Nella prossima esercitazione, vengono esaminate le operazioni CRUD per creare, leggere, aggiornare, eliminare ed elencare.</span><span class="sxs-lookup"><span data-stu-id="94f1a-847">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="94f1a-848">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="94f1a-848">Additional resources</span></span>

* [<span data-ttu-id="94f1a-849">Versione YouTube dell'esercitazione</span><span class="sxs-lookup"><span data-stu-id="94f1a-849">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="94f1a-850">Avanti</span><span class="sxs-lookup"><span data-stu-id="94f1a-850">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
