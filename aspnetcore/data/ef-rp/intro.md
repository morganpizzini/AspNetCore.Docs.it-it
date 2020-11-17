---
title: Razor Pagine con Entity Framework Core in ASP.NET Core-esercitazione 1 di 8
author: rick-anderson
description: Mostra come creare un'app per le Razor pagine usando Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
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
uid: data/ef-rp/intro
ms.openlocfilehash: 7323cf11ad4556443def4068873e6805b449058a
ms.sourcegitcommit: bce62ceaac7782e22d185814f2e8532c84efa472
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94674017"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="3eb5a-103">Razor Pagine con Entity Framework Core in ASP.NET Core-esercitazione 1 di 8</span><span class="sxs-lookup"><span data-stu-id="3eb5a-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="3eb5a-104">Di [Tom Dykstra](https://github.com/tdykstra) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3eb5a-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3eb5a-105">Questo è il primo di una serie di esercitazioni che illustrano come usare Entity Framework (EF) Core in un'app [ASP.NET Core Razor pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="3eb5a-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="3eb5a-106">Con queste esercitazioni viene creato un sito Web per una fittizia Contoso University.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="3eb5a-107">Il sito include funzionalità, come ad esempio l'ammissione di studenti, la creazione di corsi e le assegnazioni degli insegnati.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="3eb5a-108">Nell'esercitazione viene usato l'approccio Code First.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="3eb5a-109">Per informazioni su come seguire questa esercitazione usando il primo approccio al database, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="3eb5a-110">Scaricare o visualizzare l'app completata.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="3eb5a-111">[Istruzioni per il download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3eb5a-112">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="3eb5a-112">Prerequisites</span></span>

* <span data-ttu-id="3eb5a-113">Se non si ha familiarità con Razor le pagine, vedere la serie di esercitazioni [introduttive sulle Razor pagine](xref:tutorials/razor-pages/razor-pages-start) prima di iniziare.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3eb5a-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3eb5a-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3eb5a-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3eb5a-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="3eb5a-116">Motori di database</span><span class="sxs-lookup"><span data-stu-id="3eb5a-116">Database engines</span></span>

<span data-ttu-id="3eb5a-117">Le istruzioni per Visual Studio usano [SQL Server Local DB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), una versione di SQL Server Express eseguita solo in Windows.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="3eb5a-118">Le istruzioni per Visual Studio Code usano [SQLite](https://www.sqlite.org/), un motore di database multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="3eb5a-119">Se si sceglie di usare SQLite, scaricare e installare uno strumento di terze parti per la gestione e la visualizzazione di un database SQLite, ad esempio [DB Browser per SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="3eb5a-120">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="3eb5a-120">Troubleshooting</span></span>

<span data-ttu-id="3eb5a-121">Se si verifica un problema che non è possibile risolvere, confrontare il codice con il [progetto completato](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="3eb5a-122">Un buon metodo per ottenere assistenza è quello di pubblicare una domanda in StackOverflow.com con il [tag ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o il [tag EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="3eb5a-123">App di esempio</span><span class="sxs-lookup"><span data-stu-id="3eb5a-123">The sample app</span></span>

<span data-ttu-id="3eb5a-124">L'applicazione compilata in queste esercitazioni è il sito Web di base di un'università.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="3eb5a-125">Gli utenti possono visualizzare e aggiornare le informazioni che riguardano studenti, corsi e insegnanti.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="3eb5a-126">Di seguito sono disponibili alcune schermate dell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-126">Here are a few of the screens created in the tutorial.</span></span>

![Pagina Student Index (Indice degli studenti)](intro/_static/students-index30.png)

![Pagina Students Edit (Modifica studenti)](intro/_static/student-edit30.png)

<span data-ttu-id="3eb5a-129">Lo stile dell'interfaccia utente del sito è basato sui modelli di progetto predefiniti.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="3eb5a-130">L'obiettivo dell'esercitazione è quello di usare EF Core con ASP.NET Core, non come personalizzare l'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

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

## <a name="create-the-web-app-project"></a><span data-ttu-id="3eb5a-131">Creare il progetto di app Web</span><span class="sxs-lookup"><span data-stu-id="3eb5a-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3eb5a-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3eb5a-132">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3eb5a-133">Avviare Visual Studio e selezionare **Crea un nuovo progetto**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-133">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="3eb5a-134">Nella finestra di dialogo **Crea un nuovo progetto** selezionare **ASP.NET Core applicazione Web** > **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-134">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="3eb5a-135">Nella finestra di dialogo **Configura nuovo progetto** immettere `ContosoUniversity` per **nome progetto**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-135">In the **Configure your new project** dialog, enter `ContosoUniversity` for **Project name**.</span></span> <span data-ttu-id="3eb5a-136">È importante usare questo nome esatto, incluse le maiuscole, in modo che ogni `namespace` corrisponda al momento della copia del codice.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-136">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="3eb5a-137">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-137">Select **Create**.</span></span>
1. <span data-ttu-id="3eb5a-138">Nella finestra di dialogo **Crea una nuova applicazione web ASP.NET Core** selezionare:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-138">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="3eb5a-139">**.NET Core** e **ASP.NET Core 5,0** negli elenchi a discesa.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-139">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="3eb5a-140">**ASP.NET Core app Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-140">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
    1. <span data-ttu-id="3eb5a-141">**Create** 
       Crea ![ Finestra di dialogo nuovo progetto ASP.NET Core](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="3eb5a-141">**Create**
![New ASP.NET Core Project dialog](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span></span>
    
# <a name="visual-studio-code"></a>[<span data-ttu-id="3eb5a-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3eb5a-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3eb5a-143">In un terminale passare alla cartella in cui deve essere creata la cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-143">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="3eb5a-144">Eseguire i comandi seguenti per creare un Razor progetto di pagine e `cd` nella nuova cartella di progetto:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-144">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="3eb5a-145">Impostare lo stile del sito</span><span class="sxs-lookup"><span data-stu-id="3eb5a-145">Set up the site style</span></span>

<span data-ttu-id="3eb5a-146">Copiare e incollare il codice seguente nel file *pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3eb5a-146">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="3eb5a-147">Il file di layout imposta l'intestazione, il piè di pagina e il menu del sito.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-147">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="3eb5a-148">Il codice precedente apporta le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="3eb5a-149">Ogni occorrenza di "ContosoUniversity" in "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="3eb5a-149">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="3eb5a-150">Le occorrenze sono tre.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-150">There are three occurrences.</span></span>
* <span data-ttu-id="3eb5a-151">Le voci del menu **Home** e **privacy** verranno eliminate.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-151">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="3eb5a-152">Vengono aggiunte voci per **informazioni su**, **studenti**, **corsi**, **docenti** e **reparti**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-152">Entries are added for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="3eb5a-153">In *pages/index. cshtml* sostituire il contenuto del file con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-153">In *Pages/Index.cshtml*, replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="3eb5a-154">Il codice precedente sostituisce il testo relativo ASP.NET Core con il testo relativo a questa app.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-154">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="3eb5a-155">Eseguire l'app per verificare che venga visualizzata la home page.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-155">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="3eb5a-156">Modello di dati</span><span class="sxs-lookup"><span data-stu-id="3eb5a-156">The data model</span></span>

<span data-ttu-id="3eb5a-157">Nelle sezioni seguenti viene descritto come creare un modello di dati:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-157">The following sections create a data model:</span></span>

![Diagramma modello di dati Course/Enrollment/Student (Corso/Iscrizione/Studente)](intro/_static/data-model-diagram.png)

<span data-ttu-id="3eb5a-159">Uno studente può iscriversi a un numero qualsiasi di corsi e un corso può avere un numero qualsiasi di studenti iscritti.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-159">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="3eb5a-160">Entità Student (Studente)</span><span class="sxs-lookup"><span data-stu-id="3eb5a-160">The Student entity</span></span>

![Diagramma entità Student (Studente)](intro/_static/student-entity.png)

* <span data-ttu-id="3eb5a-162">Creare una cartella *Models* nella cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-162">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="3eb5a-163">Creare *Models/Student.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-163">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="3eb5a-164">La proprietà `ID` diventa la colonna chiave primaria della tabella del database che corrisponde a questa classe.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-164">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="3eb5a-165">Per impostazione predefinita, Entity Framework Core interpreta una proprietà denominata `ID` o `classnameID` come chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-165">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="3eb5a-166">Il nome alternativo riconosciuto automaticamente per la chiave primaria della classe `Student` è quindi `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-166">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="3eb5a-167">Per altre informazioni, vedere [chiavi EF Core](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-167">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="3eb5a-168">La proprietà `Enrollments` rappresenta una [proprietà di navigazione](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-168">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="3eb5a-169">Le proprietà di navigazione contengono altre entità correlate a questa entità.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-169">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="3eb5a-170">In questo caso la proprietà `Enrollments` di un'entità `Student` contiene tutte le entità `Enrollment` correlate a tale studente.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-170">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="3eb5a-171">Ad esempio, se una riga Student nel database presenta due righe Enrollment correlate, la proprietà di navigazione `Enrollments` contiene questi due entità Enrollment.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-171">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="3eb5a-172">Nel database, una riga Enrollment è correlata a una riga Student se la relativa colonna StudentID contiene il valore ID dello studente.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-172">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="3eb5a-173">Si supponga, ad esempio, che una riga Student abbia ID=1.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-173">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="3eb5a-174">Le righe Enrollment correlate avranno StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-174">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="3eb5a-175">StudentID è una *chiave esterna* nella tabella Enrollment.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-175">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="3eb5a-176">La proprietà `Enrollments` è definita come `ICollection<Enrollment>` perché potrebbero essere presenti più entità Enrollment correlate.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-176">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="3eb5a-177">È possibile usare altri tipi di raccolta, ad esempio `List<Enrollment>` o `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-177">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="3eb5a-178">Se si specifica `ICollection<Enrollment>`, per impostazione predefinita Entity Framework Core crea una raccolta `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-178">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="3eb5a-179">Entità Enrollment (Iscrizione)</span><span class="sxs-lookup"><span data-stu-id="3eb5a-179">The Enrollment entity</span></span>

![Diagramma entità Enrollment (Iscrizione)](intro/_static/enrollment-entity.png)

<span data-ttu-id="3eb5a-181">Creare *Models/Enrollment.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-181">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="3eb5a-182">La proprietà `EnrollmentID` è la chiave primaria. Questa entità usa il modello `classnameID` anziché `ID` da solo.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-182">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="3eb5a-183">Per un modello di dati di produzione, scegliere un modello e usarlo in modo coerente.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-183">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="3eb5a-184">Questa esercitazione usa entrambi solo per illustrare che entrambi funzionano.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-184">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="3eb5a-185">L'uso di `ID` senza `classname` rende più semplice l'implementazione di alcuni tipi di modifiche al modello di dati.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-185">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="3eb5a-186">La proprietà `Grade` è un oggetto`enum`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-186">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="3eb5a-187">Il punto interrogativo dopo la dichiarazione del tipo `Grade` indica che la proprietà `Grade`[ammette i valori Null](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-187">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="3eb5a-188">Un voto con valore Null è diverso da un voto con valore zero. Null significa che un voto è sconosciuto o non è stato ancora assegnato.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-188">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="3eb5a-189">La proprietà `StudentID` è una chiave esterna e la proprietà di navigazione corrispondente è `Student`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-189">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="3eb5a-190">Un'entità `Enrollment` è associata a un'entità `Student`, pertanto la proprietà contiene un'unica entità `Student`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-190">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="3eb5a-191">La proprietà `CourseID` è una chiave esterna e la proprietà di navigazione corrispondente è `Course`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-191">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="3eb5a-192">Un'entità `Enrollment` è associata a un'entità `Course`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-192">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="3eb5a-193">Entity Framework Core interpreta una proprietà come chiave esterna se è denominata `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-193">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="3eb5a-194">Ad esempio, `StudentID` è la chiave esterna per la proprietà di navigazione `Student`, dato che la chiave primaria `Student` dell'entità è `ID`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-194">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="3eb5a-195">Le proprietà di chiave esterna possono anche essere denominate `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-195">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="3eb5a-196">Ad esempio, `CourseID` poiché la chiave primaria `Course` dell'entità è `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-196">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="3eb5a-197">Entità Course (Corso)</span><span class="sxs-lookup"><span data-stu-id="3eb5a-197">The Course entity</span></span>

![Diagramma entità Course (Corso)](intro/_static/course-entity.png)

<span data-ttu-id="3eb5a-199">Creare *Models/Course.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-199">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="3eb5a-200">La proprietà `Enrollments` rappresenta una proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-200">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="3eb5a-201">È possibile correlare un'entità `Course` a un numero qualsiasi di entità `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-201">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="3eb5a-202">L'attributo `DatabaseGenerated` consente all'app di specificare la chiave primaria anziché chiedere al database di generarla.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-202">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="3eb5a-203">Compilare il progetto per verificare che non siano presenti errori del compilatore.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-203">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="3eb5a-204">Scaffolding delle pagine Student</span><span class="sxs-lookup"><span data-stu-id="3eb5a-204">Scaffold Student pages</span></span>

<span data-ttu-id="3eb5a-205">In questa sezione si userà lo strumento di scaffolding di ASP.NET Core per generare:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-205">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="3eb5a-206">Classe EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="3eb5a-206">An EF Core `DbContext` class.</span></span> <span data-ttu-id="3eb5a-207">Il contesto è la classe principale che coordina le funzionalità di Entity Framework per un determinato modello di dati.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-207">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="3eb5a-208">Deriva dalla classe <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-208">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="3eb5a-209">Razor pagine che gestiscono le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD) per l' `Student` entità.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-209">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3eb5a-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3eb5a-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3eb5a-211">Creare una cartella *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-211">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="3eb5a-212">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Pages/Students* e quindi scegliere **Aggiungi** > **Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-212">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="3eb5a-213">Nella finestra di dialogo **Aggiungi nuovo elemento di impalcatura** :</span><span class="sxs-lookup"><span data-stu-id="3eb5a-213">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="3eb5a-214">Nella scheda a sinistra selezionare **installato > Razor pagine comuni >**</span><span class="sxs-lookup"><span data-stu-id="3eb5a-214">In the left tab, select **Installed > Common > Razor Pages**</span></span>
  * <span data-ttu-id="3eb5a-215">Selezionare **Razor pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-215">Select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="3eb5a-216">Nella finestra di dialogo **Aggiungi Razor pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="3eb5a-216">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="3eb5a-217">Nell'elenco a discesa **Classe modello** selezionare **Student (ContosoUniversity.Models)**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-217">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="3eb5a-218">Nella riga **Classe contesto di dati** selezionare il segno più **+**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-218">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="3eb5a-219">Modificare il nome del contesto dati in modo `SchoolContext` che termini anziché `ContosoUniversityContext` .</span><span class="sxs-lookup"><span data-stu-id="3eb5a-219">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="3eb5a-220">Nome del contesto aggiornato: `ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="3eb5a-220">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="3eb5a-221">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-221">Select **Add**.</span></span>

<span data-ttu-id="3eb5a-222">Vengono installati automaticamente i pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-222">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="3eb5a-223">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3eb5a-223">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3eb5a-224">Eseguire i comandi seguenti dell'interfaccia della riga di comando di .NET Core per installare i pacchetti NuGet necessari:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-224">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="3eb5a-225">Per lo scaffolding è necessario il pacchetto Microsoft.VisualStudio.Web.CodeGeneration.Design.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-225">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="3eb5a-226">Sebbene l'app non usi SQL Server, lo strumento di scaffolding richiede il pacchetto di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-226">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="3eb5a-227">Creare una cartella *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-227">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="3eb5a-228">Eseguire il comando seguente per installare lo [strumento di scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-228">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="3eb5a-229">Eseguire il comando seguente per eseguire lo scaffolding delle pagine Student.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-229">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="3eb5a-230">**In Windows**</span><span class="sxs-lookup"><span data-stu-id="3eb5a-230">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="3eb5a-231">**In macOS o Linux**</span><span class="sxs-lookup"><span data-stu-id="3eb5a-231">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="3eb5a-232">Se il passaggio precedente ha esito negativo, compilare il progetto e riprovare l'operazione di impalcatura.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-232">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="3eb5a-233">Il processo di scaffolding:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-233">The scaffolding process:</span></span>

* <span data-ttu-id="3eb5a-234">Crea Razor pagine nella cartella *pages/students* :</span><span class="sxs-lookup"><span data-stu-id="3eb5a-234">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="3eb5a-235">*Create.cshtml* e *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3eb5a-235">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="3eb5a-236">*Delete.cshtml* e *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3eb5a-236">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="3eb5a-237">*Details.cshtml* e *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3eb5a-237">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="3eb5a-238">*Edit.cshtml* e *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3eb5a-238">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="3eb5a-239">*Index.cshtml* e *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3eb5a-239">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="3eb5a-240">Crea *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-240">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="3eb5a-241">Aggiunge il contesto all'inserimento delle dipendenze in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-241">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="3eb5a-242">Aggiunge una stringa di connessione al database a *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="3eb5a-242">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="3eb5a-243">Stringa di connessione al database</span><span class="sxs-lookup"><span data-stu-id="3eb5a-243">Database connection string</span></span>

<span data-ttu-id="3eb5a-244">Lo strumento di impalcatura genera una stringa di connessione nel *appsettings.json* file.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-244">The scaffolding tool generates a connection string in the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3eb5a-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3eb5a-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3eb5a-246">La stringa di connessione specifica [SQL Server database locale](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span><span class="sxs-lookup"><span data-stu-id="3eb5a-246">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

<span data-ttu-id="3eb5a-247">Local DB è una versione leggera del motore di database di SQL Server Express appositamente pensato per lo sviluppo di app e non per la produzione.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-247">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="3eb5a-248">Per impostazione predefinita, Local DB crea file con estensione *mdf* nella directory `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-248">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3eb5a-249">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3eb5a-249">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3eb5a-250">Abbreviare la stringa di connessione SQLite a *cu. DB*:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-250">Shorten the SQLite connection string to *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="3eb5a-251">Aggiornare la classe del contesto di database</span><span class="sxs-lookup"><span data-stu-id="3eb5a-251">Update the database context class</span></span>

<span data-ttu-id="3eb5a-252">La classe principale che coordina le funzionalità di EF Core per un determinato modello di dati è la classe del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-252">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="3eb5a-253">Il contesto è derivato da [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-253">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="3eb5a-254">Il contesto specifica le entità incluse nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-254">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="3eb5a-255">In questo progetto la classe è denominata `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-255">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="3eb5a-256">Aggiornare *Data/SchoolContext.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-256">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="3eb5a-257">Il codice precedente passa da singolare `DbSet<Student> Student` a plurale `DbSet<Student> Students` .</span><span class="sxs-lookup"><span data-stu-id="3eb5a-257">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="3eb5a-258">Per fare in Razor modo che il codice delle pagine corrisponda al nuovo `DBSet` nome, apportare una modifica globale da: `_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="3eb5a-258">To make the Razor Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="3eb5a-259">A: `_context.Students.`</span><span class="sxs-lookup"><span data-stu-id="3eb5a-259">to: `_context.Students.`</span></span>

<span data-ttu-id="3eb5a-260">Sono presenti 8 occorrenze.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-260">There are 8 occurrences.</span></span>

<span data-ttu-id="3eb5a-261">Poiché un set di entità contiene più entità, molti sviluppatori preferiscono che i `DBSet` nomi delle proprietà siano plurali.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-261">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="3eb5a-262">Il codice evidenziato:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-262">The highlighted code:</span></span>

* <span data-ttu-id="3eb5a-263">Crea una [proprietà \<TEntity> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per ogni set di entità.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-263">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="3eb5a-264">Nella terminologia di Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-264">In EF Core terminology:</span></span>
  * <span data-ttu-id="3eb5a-265">Un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-265">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="3eb5a-266">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-266">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="3eb5a-267">Chiama <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-267">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="3eb5a-268">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-268">`OnModelCreating`:</span></span>
  * <span data-ttu-id="3eb5a-269">Viene chiamato quando `SchoolContext` è stato inizializzato, ma prima che il modello sia stato bloccato e utilizzato per inizializzare il contesto.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-269">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="3eb5a-270">È obbligatorio perché, più avanti nell'esercitazione `Student` , l'entità avrà riferimenti alle altre entità.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-270">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="3eb5a-271">Compilare il progetto per verificare che non siano presenti errori di compilazione.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-271">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="3eb5a-272">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="3eb5a-272">Startup.cs</span></span>

<span data-ttu-id="3eb5a-273">ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-273">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3eb5a-274">I servizi, ad esempio, `SchoolContext` vengono registrati con l'inserimento delle dipendenze durante l'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-274">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="3eb5a-275">I componenti che richiedono questi servizi, ad esempio Razor le pagine, vengono forniti tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-275">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="3eb5a-276">Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-276">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="3eb5a-277">Lo strumento di scaffolding ha registrato automaticamente la classe di contesto nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-277">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3eb5a-278">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3eb5a-278">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3eb5a-279">Le seguenti righe evidenziate sono state aggiunte dall'impalcatura:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-279">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3eb5a-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3eb5a-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3eb5a-281">Verificare il codice aggiunto dall'impalcatura chiama `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="3eb5a-281">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="3eb5a-282">Per informazioni sull'uso di un database di produzione [, vedere usare SQLite per lo sviluppo, SQL Server per la produzione](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) .</span><span class="sxs-lookup"><span data-stu-id="3eb5a-282">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="3eb5a-283">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-283">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="3eb5a-284">Per lo sviluppo locale, il [sistema di configurazione ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal *appsettings.json* file.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-284">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="3eb5a-285">Aggiungere il filtro eccezioni del database</span><span class="sxs-lookup"><span data-stu-id="3eb5a-285">Add the database exception filter</span></span>

<span data-ttu-id="3eb5a-286">Aggiungere <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> a `ConfigureServices` come illustrato nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-286">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3eb5a-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3eb5a-287">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="3eb5a-288">Aggiungere il pacchetto NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) .</span><span class="sxs-lookup"><span data-stu-id="3eb5a-288">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="3eb5a-289">In PMC immettere quanto segue per aggiungere il pacchetto NuGet:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-289">In the PMC, enter the following to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="3eb5a-290">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3eb5a-290">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="3eb5a-291">Il `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` pacchetto NuGet fornisce ASP.NET Core middleware per Entity Framework Core pagine di errore.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-291">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="3eb5a-292">Questo middleware consente di rilevare e diagnosticare gli errori con Entity Framework Core migrazioni.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-292">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

<span data-ttu-id="3eb5a-293">`AddDatabaseDeveloperPageExceptionFilter`Fornisce informazioni utili sull'errore nell' [ambiente di sviluppo](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-293">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

## <a name="create-the-database"></a><span data-ttu-id="3eb5a-294">Creare il database</span><span class="sxs-lookup"><span data-stu-id="3eb5a-294">Create the database</span></span>

<span data-ttu-id="3eb5a-295">Aggiornare *Program.cs* per creare il database se non esiste:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-295">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="3eb5a-296">Il metodo [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) non esegue alcuna azione se esiste un database per il contesto.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-296">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="3eb5a-297">Se non esiste alcun database, vengono creati il database e lo schema.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-297">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="3eb5a-298">`EnsureCreated` abilita il flusso di lavoro seguente per la gestione delle modifiche al modello di dati:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-298">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="3eb5a-299">Eliminare il database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-299">Delete the database.</span></span> <span data-ttu-id="3eb5a-300">Tutti i dati esistenti andranno perduti.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-300">Any existing data is lost.</span></span>
* <span data-ttu-id="3eb5a-301">Modificare il modello di dati.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-301">Change the data model.</span></span> <span data-ttu-id="3eb5a-302">Ad esempio, aggiungere un campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-302">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="3eb5a-303">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-303">Run the app.</span></span>
* <span data-ttu-id="3eb5a-304">`EnsureCreated` crea un database con il nuovo schema.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-304">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="3eb5a-305">Questo flusso di lavoro funziona correttamente nelle fasi iniziali dello sviluppo quando lo schema è in rapida evoluzione, purché non sia necessario conservare i dati.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-305">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="3eb5a-306">La situazione è diversa quando è necessario mantenere i dati immessi nel database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-306">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="3eb5a-307">In tal caso, usare le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-307">When that is the case, use migrations.</span></span>

<span data-ttu-id="3eb5a-308">Più avanti nella serie di esercitazioni si vedrà come eliminare il database creato da `EnsureCreated` e usare invece le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-308">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="3eb5a-309">Non è possibile aggiornare un database creato da `EnsureCreated` usando le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-309">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="3eb5a-310">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="3eb5a-310">Test the app</span></span>

* <span data-ttu-id="3eb5a-311">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-311">Run the app.</span></span>
* <span data-ttu-id="3eb5a-312">Selezionare il collegamento **Students** (Studenti) e quindi **Crea nuovo**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-312">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="3eb5a-313">Eseguire il test dei collegamenti Edit (Modifica), Details (Dettagli) e Delete (Elimina).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-313">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="3eb5a-314">Specificare il valore di inizializzazione del database</span><span class="sxs-lookup"><span data-stu-id="3eb5a-314">Seed the database</span></span>

<span data-ttu-id="3eb5a-315">Il metodo `EnsureCreated` crea un database vuoto.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-315">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="3eb5a-316">In questa sezione viene aggiunto il codice che popola il database con dati di test.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-316">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="3eb5a-317">Creare *Data/DbInitializer.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-317">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="3eb5a-318">Il codice controlla se esistono studenti nel database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-318">The code checks if there are any students in the database.</span></span> <span data-ttu-id="3eb5a-319">Se non sono presenti studenti, aggiunge i dati di test al database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-319">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="3eb5a-320">I dati di test vengono creati in matrici anziché in raccolte `List<T>` per ottimizzare le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-320">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="3eb5a-321">In *Program.cs* sostituire la chiamata di `EnsureCreated` con una chiamata di `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-321">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="3eb5a-322">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3eb5a-322">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3eb5a-323">Arrestare l'app se è in esecuzione ed eseguire il comando seguente nella **console di Gestione pacchetti**:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-323">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="3eb5a-324">Rispondere con `Y` per eliminare il database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-324">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3eb5a-325">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3eb5a-325">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3eb5a-326">Arrestare l'app se è in esecuzione ed eliminare il file *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-326">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="3eb5a-327">Riavviare l'app.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-327">Restart the app.</span></span>
* <span data-ttu-id="3eb5a-328">Selezionare la pagina Students per visualizzare i dati di seeding.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-328">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="3eb5a-329">Visualizzare il database</span><span class="sxs-lookup"><span data-stu-id="3eb5a-329">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3eb5a-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3eb5a-330">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3eb5a-331">Aprire **Esplora oggetti di SQL Server** dal menu **Visualizza** in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-331">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="3eb5a-332">In Esplora oggetti di SQL Server selezionare **(localdb)\MSSQLLocalDB > Database > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-332">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="3eb5a-333">Il nome del database viene generato dal nome del contesto specificato in precedenza con l'aggiunta di un trattino e un GUID.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-333">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="3eb5a-334">Espandere il nodo **Tabelle**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-334">Expand the **Tables** node.</span></span>
* <span data-ttu-id="3eb5a-335">Fare clic con il pulsante destro del mouse sulla tabella **Student** (Studente) e fare clic su **Visualizza dati** per visualizzare le colonne create e le righe inserite nella tabella.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-335">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="3eb5a-336">Fare clic con il pulsante destro del mouse sulla tabella **Student** e scegliere **Visualizza codice** per vedere il mapping tra il modello `Student` e lo schema della tabella `Student`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-336">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3eb5a-337">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3eb5a-337">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3eb5a-338">Usare lo strumento SQLite per visualizzare lo schema del database e i dati di seeding.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-338">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="3eb5a-339">Il file di database è denominato *CU.db* e si trova nella cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-339">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="3eb5a-340">Codice asincrono</span><span class="sxs-lookup"><span data-stu-id="3eb5a-340">Asynchronous code</span></span>

<span data-ttu-id="3eb5a-341">La programmazione asincrona è la modalità predefinita per ASP.NET Core ed Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-341">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="3eb5a-342">Per un server Web è disponibile un numero limitato di thread e in situazioni di carico elevato tutti i thread disponibili potrebbero essere in uso.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-342">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="3eb5a-343">In queste circostanze il server non può elaborare nuove richieste finché i thread non saranno liberi.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-343">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="3eb5a-344">Con il codice sincrono, è possibile che molti thread siano collegati mentre non funzionano perché sono in attesa del completamento dell'I/O.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-344">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="3eb5a-345">Con il codice asincrono, se un processo è in attesa del completamento dell'operazione I/O, il thread viene liberato e il server lo può usare per l'elaborazione di altre richieste.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-345">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="3eb5a-346">Di conseguenza, il codice asincrono consente un uso più efficiente delle risorse del server e il server può gestire una maggiore quantità di traffico senza ritardi.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-346">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="3eb5a-347">Il codice asincrono comporta un minimo sovraccarico in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-347">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="3eb5a-348">In caso di traffico ridotto, il calo delle prestazioni è irrilevante, mentre nelle situazioni di traffico elevato, è essenziale ottimizzare le prestazioni potenziali.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-348">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="3eb5a-349">Nel codice seguente la parola chiave [async](/dotnet/csharp/language-reference/keywords/async), il valore restituito `Task<T>`, la parola chiave `await` e il metodo `ToListAsync` consentono di eseguire il codice in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-349">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="3eb5a-350">La parola chiave `async` indica al compilatore di eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-350">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="3eb5a-351">Generare callback per parti del corpo del metodo.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-351">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="3eb5a-352">Creare l'oggetto [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) restituito.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-352">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="3eb5a-353">Il tipo restituito `Task<T>` rappresenta il lavoro in corso.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-353">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="3eb5a-354">La parola chiave `await` indica al compilatore di suddividere il metodo in due parti.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-354">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="3eb5a-355">La prima parte termina con l'operazione avviata in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-355">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="3eb5a-356">La seconda parte viene inserita in un metodo di callback che viene chiamato al termine dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-356">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="3eb5a-357">`ToListAsync` è la versione asincrona del metodo di estensione `ToList`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-357">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="3eb5a-358">Alcuni aspetti da considerare quando si scrive codice asincrono usato da Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-358">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="3eb5a-359">Solo le istruzioni che generano query o comandi da inviare al database vengono eseguite in modo asincrono,</span><span class="sxs-lookup"><span data-stu-id="3eb5a-359">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="3eb5a-360">Sono incluse `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-360">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="3eb5a-361">Non sono incluse le istruzioni che modificano solo un oggetto `IQueryable`, ad esempio `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-361">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="3eb5a-362">Un contesto di Entity Framework Core non è thread-safe. Non tentare di eseguire più operazioni parallelamente.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-362">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="3eb5a-363">Per sfruttare i vantaggi del codice asincrono in termini di prestazioni, verificare che i pacchetti della libreria impiegati, ad esempio per il paging, usino la modalità asincrona per chiamare i metodi di EF Core che inviano query al database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-363">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="3eb5a-364">Per altre informazioni sulla programmazione asincrona in .NET, vedere [Panoramica della programmazione asincrona](/dotnet/standard/async) e [Programmazione asincrona con async e await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-364">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="3eb5a-365">Considerazioni sulle prestazioni</span><span class="sxs-lookup"><span data-stu-id="3eb5a-365">Performance considerations</span></span>

<span data-ttu-id="3eb5a-366">In generale, una pagina Web non deve caricare un numero arbitrario di righe.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-366">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="3eb5a-367">Una query deve usare il paging o un approccio di limitazione.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-367">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="3eb5a-368">Ad esempio, la query precedente può utilizzare `Take` per limitare le righe restituite:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-368">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="3eb5a-369">L'enumerazione di una tabella di grandi dimensioni in una vista può restituire una risposta HTTP 200 parzialmente costruita se un'eccezione del database si verifica in modo parziale attraverso l'enumerazione.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-369">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="3eb5a-370"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> il valore predefinito è 1024.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-370"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="3eb5a-371">Il codice seguente imposta `MaxModelBindingCollectionSize` :</span><span class="sxs-lookup"><span data-stu-id="3eb5a-371">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="3eb5a-372">Il paging verrà trattato più avanti nell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-372">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3eb5a-373">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="3eb5a-373">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="3eb5a-374">Esercitazione successiva</span><span class="sxs-lookup"><span data-stu-id="3eb5a-374">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="3eb5a-375">Questo è il primo di una serie di esercitazioni che illustrano come usare Entity Framework (EF) Core in un'app [ASP.NET Core Razor pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="3eb5a-375">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="3eb5a-376">Con queste esercitazioni viene creato un sito Web per una fittizia Contoso University.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-376">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="3eb5a-377">Il sito include funzionalità, come ad esempio l'ammissione di studenti, la creazione di corsi e le assegnazioni degli insegnati.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-377">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="3eb5a-378">Nell'esercitazione viene usato l'approccio Code First.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-378">The tutorial uses the code first approach.</span></span> <span data-ttu-id="3eb5a-379">Per informazioni su come seguire questa esercitazione usando il primo approccio al database, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-379">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="3eb5a-380">Scaricare o visualizzare l'app completata.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-380">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="3eb5a-381">[Istruzioni per il download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-381">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3eb5a-382">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="3eb5a-382">Prerequisites</span></span>

* <span data-ttu-id="3eb5a-383">Se non si ha familiarità con Razor le pagine, vedere la serie di esercitazioni [introduttive sulle Razor pagine](xref:tutorials/razor-pages/razor-pages-start) prima di iniziare.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-383">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3eb5a-384">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3eb5a-384">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3eb5a-385">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3eb5a-385">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="3eb5a-386">Motori di database</span><span class="sxs-lookup"><span data-stu-id="3eb5a-386">Database engines</span></span>

<span data-ttu-id="3eb5a-387">Le istruzioni per Visual Studio usano [SQL Server Local DB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), una versione di SQL Server Express eseguita solo in Windows.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-387">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="3eb5a-388">Le istruzioni per Visual Studio Code usano [SQLite](https://www.sqlite.org/), un motore di database multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-388">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="3eb5a-389">Se si sceglie di usare SQLite, scaricare e installare uno strumento di terze parti per la gestione e la visualizzazione di un database SQLite, ad esempio [DB Browser per SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-389">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="3eb5a-390">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="3eb5a-390">Troubleshooting</span></span>

<span data-ttu-id="3eb5a-391">Se si verifica un problema che non è possibile risolvere, confrontare il codice con il [progetto completato](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-391">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="3eb5a-392">Un buon metodo per ottenere assistenza è quello di pubblicare una domanda in StackOverflow.com con il [tag ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o il [tag EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-392">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="3eb5a-393">App di esempio</span><span class="sxs-lookup"><span data-stu-id="3eb5a-393">The sample app</span></span>

<span data-ttu-id="3eb5a-394">L'applicazione compilata in queste esercitazioni è il sito Web di base di un'università.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-394">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="3eb5a-395">Gli utenti possono visualizzare e aggiornare le informazioni che riguardano studenti, corsi e insegnanti.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-395">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="3eb5a-396">Di seguito sono disponibili alcune schermate dell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-396">Here are a few of the screens created in the tutorial.</span></span>

![Pagina Student Index (Indice degli studenti)](intro/_static/students-index30.png)

![Pagina Students Edit (Modifica studenti)](intro/_static/student-edit30.png)

<span data-ttu-id="3eb5a-399">Lo stile dell'interfaccia utente del sito è basato sui modelli di progetto predefiniti.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-399">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="3eb5a-400">L'obiettivo dell'esercitazione è quello di usare EF Core e non di personalizzare l'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-400">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="3eb5a-401">Seguire il collegamento nella parte superiore della pagina per ottenere il codice sorgente per il progetto completato.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-401">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="3eb5a-402">La cartella *cu30* contiene il codice per la versione di ASP.NET Core 3.0 dell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-402">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="3eb5a-403">I file che riflettono lo stato del codice per le esercitazioni 1-7 si trovano nella cartella *cu30snapshots*.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-403">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3eb5a-404">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3eb5a-404">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3eb5a-405">Per eseguire l'app dopo il download del progetto completato:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-405">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="3eb5a-406">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-406">Build the project.</span></span>
* <span data-ttu-id="3eb5a-407">Nella console di Gestione pacchetti eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-407">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="3eb5a-408">Eseguire il progetto per il seeding del database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-408">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3eb5a-409">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3eb5a-409">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3eb5a-410">Per eseguire l'app dopo il download del progetto completato:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-410">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="3eb5a-411">Eliminare *ContosoUniversity.csproj* e rinominare *ContosoUniversitySQLite.csproj* in *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-411">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="3eb5a-412">In *Program.cs*, impostare come commento l' `#define Startup` `StartupSQLite` utilizzo di.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-412">In *Program.cs*, comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="3eb5a-413">Eliminare *appSettings.json* e rinominare *appSettingsSQLite.json* in *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-413">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="3eb5a-414">Eliminare la cartella *Migrations* e rinominare *MigrationsSQL* in *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-414">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="3eb5a-415">Eseguire una ricerca globale `#if SQLiteVersion` e rimuovere `#if SQLiteVersion` e l'istruzione associata `#endif` .</span><span class="sxs-lookup"><span data-stu-id="3eb5a-415">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="3eb5a-416">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-416">Build the project.</span></span>
* <span data-ttu-id="3eb5a-417">Al prompt dei comandi nella cartella del progetto eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-417">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="3eb5a-418">Nello strumento SQLite eseguire l'istruzione SQL seguente:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-418">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="3eb5a-419">Eseguire il progetto per il seeding del database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-419">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="3eb5a-420">Creare il progetto di app Web</span><span class="sxs-lookup"><span data-stu-id="3eb5a-420">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3eb5a-421">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3eb5a-421">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3eb5a-422">Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-422">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3eb5a-423">Selezionare **Applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-423">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="3eb5a-424">Denominare il progetto *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-424">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="3eb5a-425">È importante usare questo nome esatto, incluse le maiuscole, in modo che gli spazi dei nomi corrispondano quando il codice viene copiato e incollato.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-425">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="3eb5a-426">Selezionare **.NET Core** e **ASP.NET Core 3.0** nell'elenco a discesa, quindi selezionare **Applicazione Web**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-426">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3eb5a-427">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3eb5a-427">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3eb5a-428">In un terminale passare alla cartella in cui deve essere creata la cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-428">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="3eb5a-429">Eseguire i comandi seguenti per creare un Razor progetto di pagine e `cd` nella nuova cartella di progetto:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-429">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="3eb5a-430">Impostare lo stile del sito</span><span class="sxs-lookup"><span data-stu-id="3eb5a-430">Set up the site style</span></span>

<span data-ttu-id="3eb5a-431">Per impostare l'intestazione, il piè di pagina e il menu del sito, aggiornare *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-431">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="3eb5a-432">Modificare tutte le occorrenze di "ContosoUniversity" in "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="3eb5a-432">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="3eb5a-433">Le occorrenze sono tre.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-433">There are three occurrences.</span></span>

* <span data-ttu-id="3eb5a-434">Eliminare le voci di menu **Home** e **Privacy**, quindi aggiungere le voci per **About** (Informazioni su), **Students** (Studenti), **Courses** (Corsi), **Instructors** (Insegnanti) e **Departments** (Dipartimenti).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-434">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="3eb5a-435">Le modifiche sono evidenziate.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-435">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="3eb5a-436">In *Pages/Index.cshtml* sostituire il contenuto del file con il codice seguente. In questo modo il testo su ASP.NET sarà sostituito dal testo relativo a questa app:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-436">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="3eb5a-437">Eseguire l'app per verificare che venga visualizzata la home page.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-437">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="3eb5a-438">Modello di dati</span><span class="sxs-lookup"><span data-stu-id="3eb5a-438">The data model</span></span>

<span data-ttu-id="3eb5a-439">Nelle sezioni seguenti viene descritto come creare un modello di dati:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-439">The following sections create a data model:</span></span>

![Diagramma modello di dati Course/Enrollment/Student (Corso/Iscrizione/Studente)](intro/_static/data-model-diagram.png)

<span data-ttu-id="3eb5a-441">Uno studente può iscriversi a un numero qualsiasi di corsi e un corso può avere un numero qualsiasi di studenti iscritti.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-441">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="3eb5a-442">Entità Student (Studente)</span><span class="sxs-lookup"><span data-stu-id="3eb5a-442">The Student entity</span></span>

![Diagramma entità Student (Studente)](intro/_static/student-entity.png)

* <span data-ttu-id="3eb5a-444">Creare una cartella *Models* nella cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-444">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="3eb5a-445">Creare *Models/Student.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-445">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="3eb5a-446">La proprietà `ID` diventa la colonna chiave primaria della tabella del database che corrisponde a questa classe.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-446">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="3eb5a-447">Per impostazione predefinita, Entity Framework Core interpreta una proprietà denominata `ID` o `classnameID` come chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-447">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="3eb5a-448">Il nome alternativo riconosciuto automaticamente per la chiave primaria della classe `Student` è quindi `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-448">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="3eb5a-449">Per altre informazioni, vedere [chiavi EF Core](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-449">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="3eb5a-450">La proprietà `Enrollments` rappresenta una [proprietà di navigazione](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-450">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="3eb5a-451">Le proprietà di navigazione contengono altre entità correlate a questa entità.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-451">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="3eb5a-452">In questo caso la proprietà `Enrollments` di un'entità `Student` contiene tutte le entità `Enrollment` correlate a tale studente.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-452">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="3eb5a-453">Ad esempio, se una riga Student nel database presenta due righe Enrollment correlate, la proprietà di navigazione `Enrollments` contiene questi due entità Enrollment.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-453">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="3eb5a-454">Nel database, una riga Enrollment è correlata a una riga Student se la relativa colonna StudentID contiene il valore ID dello studente.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-454">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="3eb5a-455">Si supponga, ad esempio, che una riga Student abbia ID=1.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-455">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="3eb5a-456">Le righe Enrollment correlate avranno StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-456">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="3eb5a-457">StudentID è una *chiave esterna* nella tabella Enrollment.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-457">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="3eb5a-458">La proprietà `Enrollments` è definita come `ICollection<Enrollment>` perché potrebbero essere presenti più entità Enrollment correlate.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-458">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="3eb5a-459">È possibile usare altri tipi di raccolta, ad esempio `List<Enrollment>` o `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-459">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="3eb5a-460">Se si specifica `ICollection<Enrollment>`, per impostazione predefinita Entity Framework Core crea una raccolta `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-460">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="3eb5a-461">Entità Enrollment (Iscrizione)</span><span class="sxs-lookup"><span data-stu-id="3eb5a-461">The Enrollment entity</span></span>

![Diagramma entità Enrollment (Iscrizione)](intro/_static/enrollment-entity.png)

<span data-ttu-id="3eb5a-463">Creare *Models/Enrollment.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-463">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="3eb5a-464">La proprietà `EnrollmentID` è la chiave primaria. Questa entità usa il modello `classnameID` anziché `ID` da solo.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-464">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="3eb5a-465">Per un modello di dati di produzione, scegliere un modello e usarlo in modo coerente.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-465">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="3eb5a-466">Questa esercitazione usa entrambi solo per illustrare che entrambi funzionano.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-466">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="3eb5a-467">L'uso di `ID` senza `classname` rende più semplice l'implementazione di alcuni tipi di modifiche al modello di dati.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-467">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="3eb5a-468">La proprietà `Grade` è un oggetto`enum`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-468">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="3eb5a-469">Il punto interrogativo dopo la dichiarazione del tipo `Grade` indica che la proprietà `Grade`[ammette i valori Null](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-469">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="3eb5a-470">Un voto con valore Null è diverso da un voto con valore zero. Null significa che un voto è sconosciuto o non è stato ancora assegnato.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-470">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="3eb5a-471">La proprietà `StudentID` è una chiave esterna e la proprietà di navigazione corrispondente è `Student`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-471">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="3eb5a-472">Un'entità `Enrollment` è associata a un'entità `Student`, pertanto la proprietà contiene un'unica entità `Student`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-472">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="3eb5a-473">La proprietà `CourseID` è una chiave esterna e la proprietà di navigazione corrispondente è `Course`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-473">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="3eb5a-474">Un'entità `Enrollment` è associata a un'entità `Course`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-474">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="3eb5a-475">Entity Framework Core interpreta una proprietà come chiave esterna se è denominata `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-475">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="3eb5a-476">Ad esempio, `StudentID` è la chiave esterna per la proprietà di navigazione `Student`, dato che la chiave primaria `Student` dell'entità è `ID`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-476">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="3eb5a-477">Le proprietà di chiave esterna possono anche essere denominate `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-477">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="3eb5a-478">Ad esempio, `CourseID` poiché la chiave primaria `Course` dell'entità è `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-478">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="3eb5a-479">Entità Course (Corso)</span><span class="sxs-lookup"><span data-stu-id="3eb5a-479">The Course entity</span></span>

![Diagramma entità Course (Corso)](intro/_static/course-entity.png)

<span data-ttu-id="3eb5a-481">Creare *Models/Course.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-481">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="3eb5a-482">La proprietà `Enrollments` rappresenta una proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-482">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="3eb5a-483">È possibile correlare un'entità `Course` a un numero qualsiasi di entità `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-483">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="3eb5a-484">L'attributo `DatabaseGenerated` consente all'app di specificare la chiave primaria anziché chiedere al database di generarla.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-484">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="3eb5a-485">Compilare il progetto per verificare che non siano presenti errori del compilatore.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-485">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="3eb5a-486">Scaffolding delle pagine Student</span><span class="sxs-lookup"><span data-stu-id="3eb5a-486">Scaffold Student pages</span></span>

<span data-ttu-id="3eb5a-487">In questa sezione si userà lo strumento di scaffolding di ASP.NET Core per generare:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-487">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="3eb5a-488">Una classe *contesto* di EF Core.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-488">An EF Core *context* class.</span></span> <span data-ttu-id="3eb5a-489">Il contesto è la classe principale che coordina le funzionalità di Entity Framework per un determinato modello di dati.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-489">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="3eb5a-490">Deriva dalla classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-490">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="3eb5a-491">Razor pagine che gestiscono le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD) per l' `Student` entità.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-491">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3eb5a-492">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3eb5a-492">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3eb5a-493">Creare una cartella *Students* nella cartella *Pages*.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-493">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="3eb5a-494">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Pages/Students* e quindi scegliere **Aggiungi** > **Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-494">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="3eb5a-495">Nella finestra di dialogo **Aggiungi impalcatura** selezionare **Razor pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-495">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="3eb5a-496">Nella finestra di dialogo **Aggiungi Razor pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="3eb5a-496">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="3eb5a-497">Nell'elenco a discesa **Classe modello** selezionare **Student (ContosoUniversity.Models)**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-497">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="3eb5a-498">Nella riga **Classe contesto di dati** selezionare il segno più **+**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-498">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="3eb5a-499">Modificare il nome del contesto di dati da *ContosoUniversity.Models.ContosoUniversityContext* a *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-499">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="3eb5a-500">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-500">Select **Add**.</span></span>

<span data-ttu-id="3eb5a-501">Vengono installati automaticamente i pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-501">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="3eb5a-502">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3eb5a-502">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3eb5a-503">Eseguire i comandi seguenti dell'interfaccia della riga di comando di .NET Core per installare i pacchetti NuGet necessari:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-503">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="3eb5a-504">Per lo scaffolding è necessario il pacchetto Microsoft.VisualStudio.Web.CodeGeneration.Design.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-504">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="3eb5a-505">Sebbene l'app non usi SQL Server, lo strumento di scaffolding richiede il pacchetto di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-505">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="3eb5a-506">Creare una cartella *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-506">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="3eb5a-507">Eseguire il comando seguente per installare lo [strumento di scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-507">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="3eb5a-508">Eseguire il comando seguente per eseguire lo scaffolding delle pagine Student.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-508">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="3eb5a-509">**In Windows**</span><span class="sxs-lookup"><span data-stu-id="3eb5a-509">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="3eb5a-510">**In macOS o Linux**</span><span class="sxs-lookup"><span data-stu-id="3eb5a-510">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="3eb5a-511">Se si riscontra un problema con il passaggio precedente, compilare il progetto e riprovare a eseguire il passaggio di scaffolding.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-511">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="3eb5a-512">Il processo di scaffolding:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-512">The scaffolding process:</span></span>

* <span data-ttu-id="3eb5a-513">Crea Razor pagine nella cartella *pages/students* :</span><span class="sxs-lookup"><span data-stu-id="3eb5a-513">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="3eb5a-514">*Create.cshtml* e *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3eb5a-514">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="3eb5a-515">*Delete.cshtml* e *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3eb5a-515">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="3eb5a-516">*Details.cshtml* e *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3eb5a-516">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="3eb5a-517">*Edit.cshtml* e *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3eb5a-517">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="3eb5a-518">*Index.cshtml* e *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3eb5a-518">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="3eb5a-519">Crea *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-519">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="3eb5a-520">Aggiunge il contesto all'inserimento delle dipendenze in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-520">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="3eb5a-521">Aggiunge una stringa di connessione al database a *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="3eb5a-521">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="3eb5a-522">Stringa di connessione al database</span><span class="sxs-lookup"><span data-stu-id="3eb5a-522">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3eb5a-523">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3eb5a-523">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3eb5a-524">Il *appsettings.json* file specifica la stringa di connessione [SQL Server database locale](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-524">The *appsettings.json* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="3eb5a-525">Local DB è una versione leggera del motore di database di SQL Server Express appositamente pensato per lo sviluppo di app e non per la produzione.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-525">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="3eb5a-526">Per impostazione predefinita, Local DB crea file con estensione *mdf* nella directory `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-526">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3eb5a-527">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3eb5a-527">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3eb5a-528">Modificare la stringa di connessione in modo che punti a un file di database SQLite denominato *CU.db*:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-528">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="3eb5a-529">Aggiornare la classe del contesto di database</span><span class="sxs-lookup"><span data-stu-id="3eb5a-529">Update the database context class</span></span>

<span data-ttu-id="3eb5a-530">La classe principale che coordina le funzionalità di EF Core per un determinato modello di dati è la classe del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-530">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="3eb5a-531">Il contesto è derivato da [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-531">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="3eb5a-532">Il contesto specifica le entità incluse nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-532">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="3eb5a-533">In questo progetto la classe è denominata `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-533">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="3eb5a-534">Aggiornare *Data/SchoolContext.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-534">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="3eb5a-535">Il codice evidenziato crea una proprietà [DbSet \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per ogni set di entità.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-535">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="3eb5a-536">Nella terminologia di Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-536">In EF Core terminology:</span></span>

* <span data-ttu-id="3eb5a-537">Un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-537">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="3eb5a-538">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-538">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="3eb5a-539">Dato che un set di entità contiene più entità, le proprietà DBSet devono essere nomi plurali.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-539">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="3eb5a-540">Dato che lo strumento di scaffolding ha creato un DBSet `Student`, questo passaggio lo modifica nel plurale `Students`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-540">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="3eb5a-541">Per fare in modo che il Razor codice delle pagine corrisponda al nuovo nome del DBSet, apportare una modifica globale nell'intero progetto di `_context.Student` a `_context.Students` .</span><span class="sxs-lookup"><span data-stu-id="3eb5a-541">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="3eb5a-542">Sono presenti 8 occorrenze.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-542">There are 8 occurrences.</span></span>

<span data-ttu-id="3eb5a-543">Compilare il progetto per verificare che non siano presenti errori di compilazione.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-543">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="3eb5a-544">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="3eb5a-544">Startup.cs</span></span>

<span data-ttu-id="3eb5a-545">ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-545">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3eb5a-546">I servizi, ad esempio il contesto di database di EF Core, vengono registrati con l'inserimento delle dipendenze durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-546">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="3eb5a-547">I componenti che richiedono questi servizi, ad esempio Razor le pagine, vengono forniti tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-547">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="3eb5a-548">Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-548">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="3eb5a-549">Lo strumento di scaffolding ha registrato automaticamente la classe di contesto nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-549">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3eb5a-550">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3eb5a-550">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3eb5a-551">In `ConfigureServices` le righe evidenziate sono state aggiunte dallo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-551">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3eb5a-552">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3eb5a-552">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3eb5a-553">In `ConfigureServices` assicurarsi che il codice aggiunto dallo scaffolder chiami `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-553">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="3eb5a-554">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-554">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="3eb5a-555">Per lo sviluppo locale, il [sistema di configurazione ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal *appsettings.json* file.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-555">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="3eb5a-556">Creare il database</span><span class="sxs-lookup"><span data-stu-id="3eb5a-556">Create the database</span></span>

<span data-ttu-id="3eb5a-557">Aggiornare *Program.cs* per creare il database se non esiste:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-557">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="3eb5a-558">Il metodo [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) non esegue alcuna azione se esiste un database per il contesto.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-558">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="3eb5a-559">Se non esiste alcun database, vengono creati il database e lo schema.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-559">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="3eb5a-560">`EnsureCreated` abilita il flusso di lavoro seguente per la gestione delle modifiche al modello di dati:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-560">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="3eb5a-561">Eliminare il database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-561">Delete the database.</span></span> <span data-ttu-id="3eb5a-562">Tutti i dati esistenti andranno perduti.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-562">Any existing data is lost.</span></span>
* <span data-ttu-id="3eb5a-563">Modificare il modello di dati.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-563">Change the data model.</span></span> <span data-ttu-id="3eb5a-564">Ad esempio, aggiungere un campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-564">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="3eb5a-565">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-565">Run the app.</span></span>
* <span data-ttu-id="3eb5a-566">`EnsureCreated` crea un database con il nuovo schema.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-566">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="3eb5a-567">Questo flusso di lavoro funziona correttamente nelle fasi iniziali dello sviluppo quando lo schema è in rapida evoluzione, purché non sia necessario conservare i dati.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-567">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="3eb5a-568">La situazione è diversa quando è necessario mantenere i dati immessi nel database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-568">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="3eb5a-569">In tal caso, usare le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-569">When that is the case, use migrations.</span></span>

<span data-ttu-id="3eb5a-570">Più avanti nella serie di esercitazioni si vedrà come eliminare il database creato da `EnsureCreated` e usare invece le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-570">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="3eb5a-571">Non è possibile aggiornare un database creato da `EnsureCreated` usando le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-571">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="3eb5a-572">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="3eb5a-572">Test the app</span></span>

* <span data-ttu-id="3eb5a-573">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-573">Run the app.</span></span>
* <span data-ttu-id="3eb5a-574">Selezionare il collegamento **Students** (Studenti) e quindi **Crea nuovo**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-574">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="3eb5a-575">Eseguire il test dei collegamenti Edit (Modifica), Details (Dettagli) e Delete (Elimina).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-575">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="3eb5a-576">Specificare il valore di inizializzazione del database</span><span class="sxs-lookup"><span data-stu-id="3eb5a-576">Seed the database</span></span>

<span data-ttu-id="3eb5a-577">Il metodo `EnsureCreated` crea un database vuoto.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-577">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="3eb5a-578">In questa sezione viene aggiunto il codice che popola il database con dati di test.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-578">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="3eb5a-579">Creare *Data/DbInitializer.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-579">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="3eb5a-580">Il codice controlla se esistono studenti nel database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-580">The code checks if there are any students in the database.</span></span> <span data-ttu-id="3eb5a-581">Se non sono presenti studenti, aggiunge i dati di test al database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-581">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="3eb5a-582">I dati di test vengono creati in matrici anziché in raccolte `List<T>` per ottimizzare le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-582">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="3eb5a-583">In *Program.cs* sostituire la chiamata di `EnsureCreated` con una chiamata di `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-583">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="3eb5a-584">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3eb5a-584">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3eb5a-585">Arrestare l'app se è in esecuzione ed eseguire il comando seguente nella **console di Gestione pacchetti**:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-585">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="3eb5a-586">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3eb5a-586">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3eb5a-587">Arrestare l'app se è in esecuzione ed eliminare il file *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-587">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="3eb5a-588">Riavviare l'app.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-588">Restart the app.</span></span>

* <span data-ttu-id="3eb5a-589">Selezionare la pagina Students per visualizzare i dati di seeding.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-589">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="3eb5a-590">Visualizzare il database</span><span class="sxs-lookup"><span data-stu-id="3eb5a-590">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3eb5a-591">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3eb5a-591">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3eb5a-592">Aprire **Esplora oggetti di SQL Server** dal menu **Visualizza** in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-592">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="3eb5a-593">In Esplora oggetti di SQL Server selezionare **(localdb)\MSSQLLocalDB > Database > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-593">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="3eb5a-594">Il nome del database viene generato dal nome del contesto specificato in precedenza con l'aggiunta di un trattino e un GUID.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-594">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="3eb5a-595">Espandere il nodo **Tabelle**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-595">Expand the **Tables** node.</span></span>
* <span data-ttu-id="3eb5a-596">Fare clic con il pulsante destro del mouse sulla tabella **Student** (Studente) e fare clic su **Visualizza dati** per visualizzare le colonne create e le righe inserite nella tabella.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-596">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="3eb5a-597">Fare clic con il pulsante destro del mouse sulla tabella **Student** e scegliere **Visualizza codice** per vedere il mapping tra il modello `Student` e lo schema della tabella `Student`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-597">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3eb5a-598">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3eb5a-598">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3eb5a-599">Usare lo strumento SQLite per visualizzare lo schema del database e i dati di seeding.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-599">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="3eb5a-600">Il file di database è denominato *CU.db* e si trova nella cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-600">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="3eb5a-601">Codice asincrono</span><span class="sxs-lookup"><span data-stu-id="3eb5a-601">Asynchronous code</span></span>

<span data-ttu-id="3eb5a-602">La programmazione asincrona è la modalità predefinita per ASP.NET Core ed Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-602">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="3eb5a-603">Per un server Web è disponibile un numero limitato di thread e in situazioni di carico elevato tutti i thread disponibili potrebbero essere in uso.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-603">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="3eb5a-604">In queste circostanze il server non può elaborare nuove richieste finché i thread non saranno liberi.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-604">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="3eb5a-605">Con il codice sincrono, può succedere che molti thread siano vincolati nonostante in quel momento non stiano eseguendo alcuna operazione. Rimangono tuttavia in attesa che l'operazione I/O sia completata.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-605">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="3eb5a-606">Con il codice asincrono, se un processo è in attesa del completamento dell'operazione I/O, il thread viene liberato e il server lo può usare per l'elaborazione di altre richieste.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-606">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="3eb5a-607">Di conseguenza, il codice asincrono consente un uso più efficiente delle risorse del server e il server può gestire una maggiore quantità di traffico senza ritardi.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-607">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="3eb5a-608">Il codice asincrono comporta un minimo sovraccarico in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-608">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="3eb5a-609">In caso di traffico ridotto, il calo delle prestazioni è irrilevante, mentre nelle situazioni di traffico elevato, è essenziale ottimizzare le prestazioni potenziali.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-609">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="3eb5a-610">Nel codice seguente la parola chiave [async](/dotnet/csharp/language-reference/keywords/async), il valore restituito `Task<T>`, la parola chiave `await` e il metodo `ToListAsync` consentono di eseguire il codice in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-610">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="3eb5a-611">La parola chiave `async` indica al compilatore di eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-611">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="3eb5a-612">Generare callback per parti del corpo del metodo.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-612">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="3eb5a-613">Creare l'oggetto [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) restituito.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-613">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="3eb5a-614">Il tipo restituito `Task<T>` rappresenta il lavoro in corso.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-614">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="3eb5a-615">La parola chiave `await` indica al compilatore di suddividere il metodo in due parti.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-615">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="3eb5a-616">La prima parte termina con l'operazione avviata in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-616">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="3eb5a-617">La seconda parte viene inserita in un metodo di callback che viene chiamato al termine dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-617">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="3eb5a-618">`ToListAsync` è la versione asincrona del metodo di estensione `ToList`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-618">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="3eb5a-619">Alcuni aspetti da considerare quando si scrive codice asincrono usato da Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-619">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="3eb5a-620">Solo le istruzioni che generano query o comandi da inviare al database vengono eseguite in modo asincrono,</span><span class="sxs-lookup"><span data-stu-id="3eb5a-620">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="3eb5a-621">Sono incluse `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-621">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="3eb5a-622">Non sono incluse le istruzioni che modificano solo un oggetto `IQueryable`, ad esempio `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-622">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="3eb5a-623">Un contesto di Entity Framework Core non è thread-safe. Non tentare di eseguire più operazioni parallelamente.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-623">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="3eb5a-624">Per sfruttare i vantaggi del codice asincrono in termini di prestazioni, verificare che i pacchetti della libreria impiegati, ad esempio per il paging, usino la modalità asincrona per chiamare i metodi di EF Core che inviano query al database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-624">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="3eb5a-625">Per altre informazioni sulla programmazione asincrona in .NET, vedere [Panoramica della programmazione asincrona](/dotnet/standard/async) e [Programmazione asincrona con async e await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-625">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="3eb5a-626">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="3eb5a-626">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="3eb5a-627">Esercitazione successiva</span><span class="sxs-lookup"><span data-stu-id="3eb5a-627">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3eb5a-628">L'app Web di esempio di Contoso University illustra come creare un' Razor app ASP.NET Core Pages con Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-628">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="3eb5a-629">L'app di esempio è un sito Web per una fittizia Contoso University.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-629">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="3eb5a-630">Include funzionalità, come ad esempio l'ammissione di studenti, la creazione di corsi e le assegnazioni di insegnati.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-630">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="3eb5a-631">Questa pagina è il prima di una serie di esercitazioni che illustrano come compilare l'app di esempio di Contoso University.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-631">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="3eb5a-632">Scaricare o visualizzare l'app completata.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-632">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="3eb5a-633">[Istruzioni per il download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-633">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3eb5a-634">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="3eb5a-634">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3eb5a-635">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3eb5a-635">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3eb5a-636">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3eb5a-636">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="3eb5a-637">Familiarità con le [ Razor pagine](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-637">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="3eb5a-638">Prima di iniziare questa serie, i nuovi programmatori dovranno completare l' [Introduzione alle Razor pagine](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="3eb5a-638">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="3eb5a-639">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="3eb5a-639">Troubleshooting</span></span>

<span data-ttu-id="3eb5a-640">Se si verifica un problema che non si sa come risolvere, è generalmente possibile trovare la soluzione confrontando il codice con il [progetto completato](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-640">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="3eb5a-641">Un buon metodo per ottenere assistenza è quello di pubblicare una domanda in [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) per [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-641">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="3eb5a-642">App Web di Contoso University</span><span class="sxs-lookup"><span data-stu-id="3eb5a-642">The Contoso University web app</span></span>

<span data-ttu-id="3eb5a-643">L'applicazione compilata in queste esercitazioni è il sito Web di base di un'università.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-643">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="3eb5a-644">Gli utenti possono visualizzare e aggiornare le informazioni che riguardano studenti, corsi e insegnanti.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-644">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="3eb5a-645">Di seguito sono disponibili alcune schermate dell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-645">Here are a few of the screens created in the tutorial.</span></span>

![Pagina Student Index (Indice degli studenti)](intro/_static/students-index.png)

![Pagina Students Edit (Modifica studenti)](intro/_static/student-edit.png)

<span data-ttu-id="3eb5a-648">Lo stile dell'interfaccia utente del sito è simile a quanto è stato generato tramite i modelli predefiniti.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-648">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="3eb5a-649">Lo stato attivo dell'esercitazione è EF Core con le Razor pagine, non con l'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-649">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="3eb5a-650">Creare l' Razor app Web ContosoUniversity Pages</span><span class="sxs-lookup"><span data-stu-id="3eb5a-650">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3eb5a-651">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3eb5a-651">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3eb5a-652">Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-652">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3eb5a-653">Creare una nuova applicazione Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-653">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="3eb5a-654">Denominare il progetto **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-654">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="3eb5a-655">È importante denominare il progetto *ContosoUniversity* in modo che gli spazi dei nomi corrispondano quando il codice viene copiato/incollato.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-655">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="3eb5a-656">Selezionare **ASP.NET Core 2.1** nell'elenco a discesa, quindi selezionare **Applicazione Web**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-656">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="3eb5a-657">Per le immagini dei passaggi precedenti, vedere [creare un' Razor app Web](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-657">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="3eb5a-658">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-658">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3eb5a-659">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3eb5a-659">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="3eb5a-660">Impostare lo stile del sito</span><span class="sxs-lookup"><span data-stu-id="3eb5a-660">Set up the site style</span></span>

<span data-ttu-id="3eb5a-661">Con alcune modifiche è possibile impostare il menu del sito, il layout e la home page.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-661">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="3eb5a-662">Aggiornare *Pages/Shared/_Layout.cshtml* con le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-662">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="3eb5a-663">Modificare tutte le occorrenze di "ContosoUniversity" in "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="3eb5a-663">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="3eb5a-664">Le occorrenze sono tre.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-664">There are three occurrences.</span></span>

* <span data-ttu-id="3eb5a-665">Aggiungere le voci di menu per **Students** (Studenti), **Courses** (Corsi), **Instructors** (Insegnanti) e **Departments** (Dipartimenti) ed eliminare la voce di menu **Contact** (Contatto).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-665">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="3eb5a-666">Le modifiche sono evidenziate.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-666">The changes are highlighted.</span></span> <span data-ttu-id="3eb5a-667">*Non* viene visualizzato l'intero markup.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-667">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="3eb5a-668">In *Pages/Index.cshtml* sostituire il contenuto del file con il codice seguente. In questo modo il testo su ASP.NET e MVC sarà sostituito dal testo relativo a questa app:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-668">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="3eb5a-669">Creare il modello di dati</span><span class="sxs-lookup"><span data-stu-id="3eb5a-669">Create the data model</span></span>

<span data-ttu-id="3eb5a-670">Creare le classi delle entità per l'app di Contoso University.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-670">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="3eb5a-671">Iniziare con le tre entità seguenti:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-671">Start with the following three entities:</span></span>

![Diagramma modello di dati Course/Enrollment/Student (Corso/Iscrizione/Studente)](intro/_static/data-model-diagram.png)

<span data-ttu-id="3eb5a-673">Esiste una relazione uno-a-molti tra le entità `Student` e `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-673">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="3eb5a-674">Esiste una relazione uno-a-molti tra le entità `Course` e `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-674">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="3eb5a-675">Uno studente può iscriversi a un numero qualsiasi di corsi.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-675">A student can enroll in any number of courses.</span></span> <span data-ttu-id="3eb5a-676">Un corso può avere un numero qualsiasi di studenti iscritti.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-676">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="3eb5a-677">Nelle sezioni seguenti viene creata una classe per ognuna di queste entità.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-677">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="3eb5a-678">Entità Student (Studente)</span><span class="sxs-lookup"><span data-stu-id="3eb5a-678">The Student entity</span></span>

![Diagramma entità Student (Studente)](intro/_static/student-entity.png)

<span data-ttu-id="3eb5a-680">Creare una cartella *Models* (Modelli).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-680">Create a *Models* folder.</span></span> <span data-ttu-id="3eb5a-681">Nella cartella *Models* (Modelli) creare un file di classe denominato *Student.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-681">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="3eb5a-682">La proprietà `ID` diventa la colonna di chiave primaria della tabella di database (DB) che corrisponde a questa classe.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-682">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="3eb5a-683">Per impostazione predefinita, Entity Framework Core interpreta una proprietà denominata `ID` o `classnameID` come chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-683">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="3eb5a-684">In `classnameID``classname` è il nome della classe.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-684">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="3eb5a-685">La chiave primaria alternativa riconosciuta automaticamente è `StudentID` nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-685">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="3eb5a-686">La proprietà `Enrollments` rappresenta una [proprietà di navigazione](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-686">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="3eb5a-687">Le proprietà di navigazione si collegano ad altre entità correlate a questa entità.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-687">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="3eb5a-688">In questo caso la proprietà `Enrollments` di `Student entity` contiene tutte le entità `Enrollment` correlate a `Student`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-688">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="3eb5a-689">Ad esempio, se una riga Student (Studente) nella database presenta due righe Enrollment (Iscrizione) correlate, la proprietà di navigazione `Enrollments` contiene questi due entità `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-689">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="3eb5a-690">Una riga `Enrollment` correlata è una riga che contiene il valore della chiave primaria dello studente nella colonna `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-690">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="3eb5a-691">Si supponga ad esempio che per lo studente con ID = 1 siano presenti due righe nella tabella `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-691">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="3eb5a-692">La tabella `Enrollment` contiene due righe con `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-692">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="3eb5a-693">`StudentID` è una chiave esterna nella tabella `Enrollment` che specifica lo studente nella tabella `Student`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-693">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="3eb5a-694">Se una proprietà di navigazione può contenere più entità, deve essere un tipo elenco, ad esempio `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-694">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="3eb5a-695">È possibile specificare `ICollection<T>` o un tipo, ad esempio `List<T>` o `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-695">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="3eb5a-696">Se si specifica `ICollection<T>`, per impostazione predefinita Entity Framework Core crea una raccolta `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-696">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="3eb5a-697">Le proprietà di navigazione che contengono più entità provengono da relazioni molti-a-molti e uno-a-molti.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-697">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="3eb5a-698">Entità Enrollment (Iscrizione)</span><span class="sxs-lookup"><span data-stu-id="3eb5a-698">The Enrollment entity</span></span>

![Diagramma entità Enrollment (Iscrizione)](intro/_static/enrollment-entity.png)

<span data-ttu-id="3eb5a-700">Nella cartella *Models* (Modelli) creare un file *Enrollment.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-700">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="3eb5a-701">La proprietà `EnrollmentID` è la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-701">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="3eb5a-702">Questa entità usa il criterio `classnameID` anziché `ID` come l'entità `Student`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-702">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="3eb5a-703">In genere gli sviluppatori scelgono un criterio che usano poi in tutto il modello di dati.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-703">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="3eb5a-704">In un'esercitazione successiva viene illustrato l'uso di ID senza classname. In questo modo si semplifica l'implementazione dell'ereditarietà nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-704">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="3eb5a-705">La proprietà `Grade` è un oggetto`enum`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-705">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="3eb5a-706">Il punto interrogativo dopo la dichiarazione del tipo `Grade` indica che la proprietà `Grade` ammette i valori Null.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-706">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="3eb5a-707">Un voto il cui valore è Null è diverso da un voto con valore zero. Null significa che un voto è sconosciuto o non è stato ancora assegnato.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-707">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="3eb5a-708">La proprietà `StudentID` è una chiave esterna e la proprietà di navigazione corrispondente è `Student`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-708">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="3eb5a-709">Un'entità `Enrollment` è associata a un'entità `Student`, pertanto la proprietà contiene un'unica entità `Student`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-709">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="3eb5a-710">L'entità `Student` si differenzia dalla proprietà di navigazione `Student.Enrollments` che contiene più entità `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-710">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="3eb5a-711">La proprietà `CourseID` è una chiave esterna e la proprietà di navigazione corrispondente è `Course`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-711">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="3eb5a-712">Un'entità `Enrollment` è associata a un'entità `Course`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-712">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="3eb5a-713">Entity Framework Core interpreta una proprietà come chiave esterna se è denominata `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-713">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="3eb5a-714">Ad esempio, `StudentID` per la proprietà di navigazione `Student`, poiché la chiave primaria `Student` dell'entità è `ID`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-714">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="3eb5a-715">Le proprietà di chiave esterna possono anche essere denominate `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-715">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="3eb5a-716">Ad esempio, `CourseID` poiché la chiave primaria `Course` dell'entità è `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-716">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="3eb5a-717">Entità Course (Corso)</span><span class="sxs-lookup"><span data-stu-id="3eb5a-717">The Course entity</span></span>

![Diagramma entità Course (Corso)](intro/_static/course-entity.png)

<span data-ttu-id="3eb5a-719">Nella cartella *Models* (Modelli) creare un file *Course.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-719">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="3eb5a-720">La proprietà `Enrollments` rappresenta una proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-720">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="3eb5a-721">È possibile correlare un'entità `Course` a un numero qualsiasi di entità `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-721">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="3eb5a-722">L'attributo `DatabaseGenerated` consente all'app di specificare la chiave primaria anziché chiedere al database di generarla.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-722">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="3eb5a-723">Eseguire lo scaffolding del modello Student (Studente)</span><span class="sxs-lookup"><span data-stu-id="3eb5a-723">Scaffold the student model</span></span>

<span data-ttu-id="3eb5a-724">In questa sezione viene eseguito lo scaffolding del modello Student (Studente).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-724">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="3eb5a-725">Lo strumento di scaffolding crea quindi le pagine per le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) per il modello Student (Studente).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-725">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="3eb5a-726">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-726">Build the project.</span></span>
* <span data-ttu-id="3eb5a-727">Creare la cartella *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-727">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3eb5a-728">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3eb5a-728">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3eb5a-729">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Pages/Students* > **Aggiungi** > **Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-729">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="3eb5a-730">Nella finestra di dialogo **Aggiungi impalcatura** selezionare **Razor pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-730">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="3eb5a-731">Completare la finestra di dialogo **Aggiungi Razor pagine con Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="3eb5a-731">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="3eb5a-732">Nell'elenco a discesa **Classe modello** selezionare **Student (ContosoUniversity.Models)**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-732">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="3eb5a-733">Nella riga **Classe contesto di dati** selezionare il segno **+** (più) e modificare il nome generato in **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-733">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="3eb5a-734">Nell'elenco a discesa **Classe contesto di dati** selezionare **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-734">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="3eb5a-735">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-735">Select **Add**.</span></span>

![Finestra di dialogo CRUD](intro/_static/s1.png)

<span data-ttu-id="3eb5a-737">Vedere [Eseguire lo scaffolding del modello di filmato](xref:tutorials/razor-pages/model#scaffold-the-movie-model) se si riscontrano problemi con il passaggio precedente.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-737">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3eb5a-738">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3eb5a-738">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3eb5a-739">Eseguire i comandi seguenti per eseguire lo scaffolding del modello Student (Studente).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-739">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="3eb5a-740">Il processo di scaffolding ha creato e modificato i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-740">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="3eb5a-741">File creati</span><span class="sxs-lookup"><span data-stu-id="3eb5a-741">Files created</span></span>

* <span data-ttu-id="3eb5a-742">Pagine Create (Crea), Delete (Elimina), Details (Dettagli), Edit (Modifica) e Index (Indice) di *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-742">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="3eb5a-743">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="3eb5a-743">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="3eb5a-744">Aggiornamenti dei file</span><span class="sxs-lookup"><span data-stu-id="3eb5a-744">File updates</span></span>

* <span data-ttu-id="3eb5a-745">*Startup.cs*: le modifiche a questo file sono descritte in dettaglio nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-745">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="3eb5a-746">*appsettings.json* : Viene aggiunta la stringa di connessione utilizzata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-746">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="3eb5a-747">Esaminare il contesto registrato con l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="3eb5a-747">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="3eb5a-748">ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-748">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3eb5a-749">I servizi, ad esempio il contesto di database di Entity Framework Core, vengono registrati tramite dependency injection durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-749">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="3eb5a-750">I componenti che richiedono questi servizi, ad esempio Razor le pagine, vengono forniti tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-750">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="3eb5a-751">Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-751">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="3eb5a-752">Lo strumento di scaffolding ha creato automaticamente un contesto del database e lo ha registrato con il contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-752">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="3eb5a-753">Esaminare il metodo `ConfigureServices` in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-753">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="3eb5a-754">La riga evidenziata è stata aggiunta dallo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-754">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="3eb5a-755">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-755">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="3eb5a-756">Per lo sviluppo locale, il [sistema di configurazione ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal *appsettings.json* file.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-756">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="3eb5a-757">Aggiornare il metodo Main</span><span class="sxs-lookup"><span data-stu-id="3eb5a-757">Update main</span></span>

<span data-ttu-id="3eb5a-758">In *Program.cs* modificare il metodo `Main` per eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-758">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="3eb5a-759">Ottenere un'istanza del contesto di database dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-759">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="3eb5a-760">Chiamare [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-760">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="3eb5a-761">Eliminare il contesto dopo che il metodo `EnsureCreated` è stato completato.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-761">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="3eb5a-762">Il codice seguente illustra il file *Program.cs* aggiornato.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-762">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="3eb5a-763">`EnsureCreated` assicura che esista il database per il contesto.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-763">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="3eb5a-764">Se esiste, non viene eseguita alcuna azione.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-764">If it exists, no action is taken.</span></span> <span data-ttu-id="3eb5a-765">Se non esiste, vengono creati il database e tutti i relativi schemi.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-765">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="3eb5a-766">`EnsureCreated` non usa le migrazioni per creare il database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-766">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="3eb5a-767">Un database creato con `EnsureCreated` non potrà essere aggiornato successivamente usando le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-767">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="3eb5a-768">`EnsureCreated` viene chiamato all'avvio dell'app e consente il flusso di lavoro seguente:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-768">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="3eb5a-769">Eliminare il database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-769">Delete the DB.</span></span>
* <span data-ttu-id="3eb5a-770">Modificare lo schema di database, ad esempio, aggiungere un campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-770">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="3eb5a-771">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-771">Run the app.</span></span>
* <span data-ttu-id="3eb5a-772">`EnsureCreated` crea un database con la colonna `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-772">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="3eb5a-773">`EnsureCreated` è utile nelle prime fasi di sviluppo quando lo schema è in rapida evoluzione.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-773">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="3eb5a-774">Più avanti nell'esercitazione il database verrà eliminato e si useranno le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-774">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="3eb5a-775">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="3eb5a-775">Test the app</span></span>

<span data-ttu-id="3eb5a-776">Eseguire l'app e accettare i cookie criteri.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-776">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="3eb5a-777">Questa app non memorizza informazioni personali.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-777">This app doesn't keep personal information.</span></span> <span data-ttu-id="3eb5a-778">Per informazioni sui criteri, vedere la pagina relativa cookie al [supporto per regolamento generale sulla protezione dei dati UE (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-778">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="3eb5a-779">Selezionare il collegamento **Students** (Studenti) e quindi **Crea nuovo**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-779">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="3eb5a-780">Eseguire il test dei collegamenti Edit (Modifica), Details (Dettagli) e Delete (Elimina).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-780">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="3eb5a-781">Esaminare il contesto di database SchoolContext</span><span class="sxs-lookup"><span data-stu-id="3eb5a-781">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="3eb5a-782">La classe principale che coordina le funzionalità di Entity Framework Core per un determinato modello di dati è la classe del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-782">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="3eb5a-783">Il contesto dei dati è derivato da [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-783">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="3eb5a-784">Il contesto dei dati specifica le entità incluse nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-784">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="3eb5a-785">In questo progetto la classe è denominata `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-785">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="3eb5a-786">Aggiornare *SchoolContext.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-786">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="3eb5a-787">Il codice evidenziato crea una proprietà [DbSet \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per ogni set di entità.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-787">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="3eb5a-788">Nella terminologia di Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-788">In EF Core terminology:</span></span>

* <span data-ttu-id="3eb5a-789">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-789">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="3eb5a-790">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-790">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="3eb5a-791">`DbSet<Enrollment>` e `DbSet<Course>` potrebbero essere omessi.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-791">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="3eb5a-792">Core Entity Framework le include in modo implicito perché l'entità `Student` fa riferimento all'entità `Enrollment` e l'entità `Enrollment` fa riferimento all'entità `Course`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-792">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="3eb5a-793">Per questa esercitazione, mantenere `DbSet<Enrollment>` e `DbSet<Course>` in `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-793">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="3eb5a-794">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="3eb5a-794">SQL Server Express LocalDB</span></span>

<span data-ttu-id="3eb5a-795">La stringa di connessione specifica [SQL Server Local DB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-795">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="3eb5a-796">Local DB è una versione leggera del motore di database di SQL Server Express appositamente pensato per lo sviluppo di app e non per la produzione.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-796">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="3eb5a-797">Local DB viene avviato su richiesta ed eseguito in modalità utente; non richiede quindi una configurazione complessa.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-797">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="3eb5a-798">Per impostazione predefinita, Local DB crea file di database con estensione *mdf* nella directory `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-798">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="3eb5a-799">Aggiungere il codice per inizializzare il database con dati di test</span><span class="sxs-lookup"><span data-stu-id="3eb5a-799">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="3eb5a-800">Entity Framework Core crea un database vuoto.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-800">EF Core creates an empty DB.</span></span> <span data-ttu-id="3eb5a-801">In questa sezione viene scritto un metodo `Initialize` per popolare il database con i dati di test.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-801">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="3eb5a-802">Nella cartella *Data* (Dati) creare un nuovo file di classe denominato *DbInitializer.cs* e aggiungere il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-802">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="3eb5a-803">Nota: il codice precedente USA `Models` per lo spazio dei nomi ( `namespace ContosoUniversity.Models` ) anziché `Data` .</span><span class="sxs-lookup"><span data-stu-id="3eb5a-803">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="3eb5a-804">`Models` è coerente con il codice generato dallo scaffolder.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-804">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="3eb5a-805">Per altre informazioni, vedere [questo problema relativo allo scaffolding in GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-805">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="3eb5a-806">Il codice controlla se esistono studenti nel database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-806">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="3eb5a-807">Se il database non contiene studenti, il database viene inizializzato con i dati di test.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-807">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="3eb5a-808">I dati di test vengono caricati in matrici anziché in raccolte `List<T>` per ottimizzare le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-808">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="3eb5a-809">Il metodo `EnsureCreated` crea automaticamente il database per il contesto di database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-809">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="3eb5a-810">Se il database esiste, `EnsureCreated` restituisce senza modificare il database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-810">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="3eb5a-811">In *Program.cs* modificare il metodo `Main` per chiamare `Initialize`:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-811">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="3eb5a-812">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3eb5a-812">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3eb5a-813">Arrestare l'app se è in esecuzione ed eseguire il comando seguente nella **console di Gestione pacchetti**:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-813">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="3eb5a-814">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3eb5a-814">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3eb5a-815">Arrestare l'app se è in esecuzione ed eliminare il file *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-815">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="3eb5a-816">Visualizzare il database</span><span class="sxs-lookup"><span data-stu-id="3eb5a-816">View the DB</span></span>

<span data-ttu-id="3eb5a-817">Il nome del database viene generato dal nome del contesto specificato in precedenza con l'aggiunta di un trattino e un GUID.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-817">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="3eb5a-818">Di conseguenza, il nome del database sarà "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="3eb5a-818">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="3eb5a-819">Il GUID sarà diverso per ogni utente.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-819">The GUID will be different for each user.</span></span>
<span data-ttu-id="3eb5a-820">Aprire **Esplora oggetti di SQL Server** dal menu **Visualizza** in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-820">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="3eb5a-821">In Esplora oggetti di SQL Server fare clic su **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-821">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="3eb5a-822">Espandere il nodo **Tabelle**.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-822">Expand the **Tables** node.</span></span>

<span data-ttu-id="3eb5a-823">Fare clic con il pulsante destro del mouse sulla tabella **Student** (Studente) e fare clic su **Visualizza dati** per visualizzare le colonne create e le righe inserite nella tabella.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-823">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="3eb5a-824">Codice asincrono</span><span class="sxs-lookup"><span data-stu-id="3eb5a-824">Asynchronous code</span></span>

<span data-ttu-id="3eb5a-825">La programmazione asincrona è la modalità predefinita per ASP.NET Core ed Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-825">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="3eb5a-826">Per un server Web è disponibile un numero limitato di thread e in situazioni di carico elevato tutti i thread disponibili potrebbero essere in uso.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-826">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="3eb5a-827">In queste circostanze il server non può elaborare nuove richieste finché i thread non saranno liberi.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-827">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="3eb5a-828">Con il codice sincrono, può succedere che molti thread siano vincolati nonostante in quel momento non stiano eseguendo alcuna operazione. Rimangono tuttavia in attesa che l'operazione I/O sia completata.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-828">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="3eb5a-829">Con il codice asincrono, se un processo è in attesa del completamento dell'operazione I/O, il thread viene liberato e il server lo può usare per l'elaborazione di altre richieste.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-829">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="3eb5a-830">Di conseguenza, il codice asincrono consente un uso più efficiente delle risorse e il server è abilitato per gestire una maggiore quantità di traffico senza ritardi.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-830">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="3eb5a-831">Il codice asincrono comporta un minimo sovraccarico in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-831">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="3eb5a-832">In caso di traffico ridotto, il calo delle prestazioni è irrilevante, mentre nelle situazioni di traffico elevato, è essenziale ottimizzare le prestazioni potenziali.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-832">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="3eb5a-833">Nel codice seguente la parola chiave [async](/dotnet/csharp/language-reference/keywords/async), il valore restituito `Task<T>`, la parola chiave `await` e il metodo `ToListAsync` consentono di eseguire il codice in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-833">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="3eb5a-834">La parola chiave `async` indica al compilatore di eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-834">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="3eb5a-835">Generare callback per parti del corpo del metodo.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-835">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="3eb5a-836">Creare automaticamente l' oggetto [Task](/dotnet/api/system.threading.tasks.task) restituito.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-836">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="3eb5a-837">Per altre informazioni, vedere [Tipo restituito Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-837">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="3eb5a-838">Il tipo restituito implicito `Task` rappresenta il lavoro in corso.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-838">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="3eb5a-839">La parola chiave `await` indica al compilatore di suddividere il metodo in due parti.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-839">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="3eb5a-840">La prima parte termina con l'operazione avviata in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-840">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="3eb5a-841">La seconda parte viene inserita in un metodo di callback che viene chiamato al termine dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-841">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="3eb5a-842">`ToListAsync` è la versione asincrona del metodo di estensione `ToList`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-842">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="3eb5a-843">Alcuni aspetti da considerare quando si scrive codice asincrono usato da Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="3eb5a-843">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="3eb5a-844">Solo le istruzioni che generano query o comandi da inviare al database vengono eseguite in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-844">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="3eb5a-845">Sono incluse `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-845">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="3eb5a-846">Non sono incluse le istruzioni che modificano solo un oggetto `IQueryable`, ad esempio `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-846">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="3eb5a-847">Un contesto di Entity Framework Core non è thread-safe. Non tentare di eseguire più operazioni parallelamente.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-847">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="3eb5a-848">Per sfruttare i vantaggi del codice asincrono in termini di prestazioni, verificare che i pacchetti della libreria impiegati, ad esempio per il paging, usino la modalità asincrona per chiamare i metodi di Entity Framework Core che generano query da inviare al database.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-848">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="3eb5a-849">Per altre informazioni sulla programmazione asincrona in .NET, vedere [Panoramica della programmazione asincrona](/dotnet/standard/async) e [Programmazione asincrona con async e await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="3eb5a-849">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="3eb5a-850">Nella prossima esercitazione, vengono esaminate le operazioni CRUD per creare, leggere, aggiornare, eliminare ed elencare.</span><span class="sxs-lookup"><span data-stu-id="3eb5a-850">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="3eb5a-851">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="3eb5a-851">Additional resources</span></span>

* [<span data-ttu-id="3eb5a-852">Versione YouTube dell'esercitazione</span><span class="sxs-lookup"><span data-stu-id="3eb5a-852">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="3eb5a-853">Avanti</span><span class="sxs-lookup"><span data-stu-id="3eb5a-853">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
