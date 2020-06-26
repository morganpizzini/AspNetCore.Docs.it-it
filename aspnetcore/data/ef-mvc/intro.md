---
title: "Esercitazione: Introduzione a EF Core in un'app Web MVC ASP.NET"
description: Questa è la prima di una serie di esercitazioni che illustrano come compilare da zero l'app di esempio di Contoso University.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 02/06/2019
ms.topic: tutorial
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/intro
ms.openlocfilehash: 3a42ce1773bef74fab35884025765d147c534dd2
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403222"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a><span data-ttu-id="e37cb-103">Esercitazione: Introduzione a EF Core in un'app Web MVC ASP.NET</span><span class="sxs-lookup"><span data-stu-id="e37cb-103">Tutorial: Get started with EF Core in an ASP.NET MVC web app</span></span>

<span data-ttu-id="e37cb-104">Questa esercitazione **non** è stata aggiornata ad ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="e37cb-104">This tutorial has **not** been updated to ASP.NET Core 3.0.</span></span> <span data-ttu-id="e37cb-105">La [ Razor versione delle pagine](xref:data/ef-rp/intro) è stata aggiornata.</span><span class="sxs-lookup"><span data-stu-id="e37cb-105">The [Razor Pages version](xref:data/ef-rp/intro) has been updated.</span></span> <span data-ttu-id="e37cb-106">La maggior parte delle modifiche del codice per la ASP.NET Core 3,0 e la versione successiva di questa esercitazione:</span><span class="sxs-lookup"><span data-stu-id="e37cb-106">Most of the code changes for the ASP.NET Core 3.0 and later version of this tutorial:</span></span>

* <span data-ttu-id="e37cb-107">Si trovano nei file *Startup.cs* e *Program.cs* .</span><span class="sxs-lookup"><span data-stu-id="e37cb-107">Are in the *Startup.cs* and *Program.cs* files.</span></span>
* <span data-ttu-id="e37cb-108">Si trova nella versione delle [ Razor pagine](xref:data/ef-rp/intro).</span><span class="sxs-lookup"><span data-stu-id="e37cb-108">Can be found in the [Razor Pages version](xref:data/ef-rp/intro).</span></span> 

<span data-ttu-id="e37cb-109">Per informazioni su quando questo potrebbe essere aggiornato, vedere [questo problema in GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/13920).</span><span class="sxs-lookup"><span data-stu-id="e37cb-109">For information on when this might be updated, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/13920).</span></span>

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="e37cb-110">L'applicazione Web di esempio di Contoso University illustra come creare applicazioni Web ASP.NET Core 2.2 MVC con Entity Framework (EF) Core 2.2 e Visual Studio 2017 o 2019.</span><span class="sxs-lookup"><span data-stu-id="e37cb-110">The Contoso University sample web application demonstrates how to create ASP.NET Core 2.2 MVC web applications using Entity Framework (EF) Core 2.2 and Visual Studio 2017 or 2019.</span></span>

<span data-ttu-id="e37cb-111">L'applicazione di esempio è un sito Web per una fittizia Contoso University.</span><span class="sxs-lookup"><span data-stu-id="e37cb-111">The sample application is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="e37cb-112">Include funzionalità, come ad esempio l'ammissione di studenti, la creazione di corsi e le assegnazioni di insegnati.</span><span class="sxs-lookup"><span data-stu-id="e37cb-112">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="e37cb-113">Questa è la prima di una serie di esercitazioni che illustrano come compilare da zero l'app di esempio di Contoso University.</span><span class="sxs-lookup"><span data-stu-id="e37cb-113">This is the first in a series of tutorials that explain how to build the Contoso University sample application from scratch.</span></span>

<span data-ttu-id="e37cb-114">In questa esercitazione:</span><span class="sxs-lookup"><span data-stu-id="e37cb-114">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e37cb-115">Creare un'app Web ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="e37cb-115">Create an ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="e37cb-116">Impostare lo stile del sito</span><span class="sxs-lookup"><span data-stu-id="e37cb-116">Set up the site style</span></span>
> * <span data-ttu-id="e37cb-117">Scoprire di più sui pacchetti NuGet di EF Core</span><span class="sxs-lookup"><span data-stu-id="e37cb-117">Learn about EF Core NuGet packages</span></span>
> * <span data-ttu-id="e37cb-118">Creare il modello di dati</span><span class="sxs-lookup"><span data-stu-id="e37cb-118">Create the data model</span></span>
> * <span data-ttu-id="e37cb-119">Creare il contesto di database</span><span class="sxs-lookup"><span data-stu-id="e37cb-119">Create the database context</span></span>
> * <span data-ttu-id="e37cb-120">Registrare il contesto per l'inserimento delle dipendenze</span><span class="sxs-lookup"><span data-stu-id="e37cb-120">Register the context for dependency injection</span></span>
> * <span data-ttu-id="e37cb-121">Inizializzare il database con i dati di test</span><span class="sxs-lookup"><span data-stu-id="e37cb-121">Initialize the database with test data</span></span>
> * <span data-ttu-id="e37cb-122">Creare controller e visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="e37cb-122">Create a controller and views</span></span>
> * <span data-ttu-id="e37cb-123">Visualizzare il database</span><span class="sxs-lookup"><span data-stu-id="e37cb-123">View the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e37cb-124">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="e37cb-124">Prerequisites</span></span>

* [<span data-ttu-id="e37cb-125">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="e37cb-125">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download)
* <span data-ttu-id="e37cb-126">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con i carichi di lavoro seguenti:</span><span class="sxs-lookup"><span data-stu-id="e37cb-126">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the following workloads:</span></span>
  * <span data-ttu-id="e37cb-127">**ASP.NET e carico di lavoro di sviluppo Web**</span><span class="sxs-lookup"><span data-stu-id="e37cb-127">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="e37cb-128">Carico di lavoro di **sviluppo multipiattaforma .NET Core**</span><span class="sxs-lookup"><span data-stu-id="e37cb-128">**.NET Core cross-platform development** workload</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="e37cb-129">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="e37cb-129">Troubleshooting</span></span>

<span data-ttu-id="e37cb-130">Se si verifica un problema che non si sa come risolvere, è generalmente possibile trovare la soluzione confrontando il codice con il [progetto completato](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final).</span><span class="sxs-lookup"><span data-stu-id="e37cb-130">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final).</span></span> <span data-ttu-id="e37cb-131">Per un elenco degli errori comuni e delle relative soluzioni, vedere [la sezione relativa alla risoluzione dei problemi nell'ultima esercitazione della serie](advanced.md#common-errors).</span><span class="sxs-lookup"><span data-stu-id="e37cb-131">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="e37cb-132">Se non si trova la soluzione, è possibile inviare una domanda a StackOverflow.com per [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o [Entity Framework Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="e37cb-132">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="e37cb-133">In questa serie di 10 esercitazioni ogni esercitazione si basa su quanto viene eseguito nelle esercitazioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="e37cb-133">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="e37cb-134">È consigliabile salvare una copia del progetto dopo aver completato ogni esercitazione.</span><span class="sxs-lookup"><span data-stu-id="e37cb-134">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="e37cb-135">Se si verificano problemi, è possibile ricominciare dall'esercitazione precedente anziché tornare all'inizio della serie.</span><span class="sxs-lookup"><span data-stu-id="e37cb-135">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="e37cb-136">App Web di Contoso University</span><span class="sxs-lookup"><span data-stu-id="e37cb-136">Contoso University web app</span></span>

<span data-ttu-id="e37cb-137">L'applicazione che sarà compilata in queste esercitazioni è un semplice sito Web universitario.</span><span class="sxs-lookup"><span data-stu-id="e37cb-137">The application you'll be building in these tutorials is a simple university web site.</span></span>

<span data-ttu-id="e37cb-138">Gli utenti possono visualizzare e aggiornare le informazioni che riguardano studenti, corsi e insegnanti.</span><span class="sxs-lookup"><span data-stu-id="e37cb-138">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="e37cb-139">Di seguito sono disponibili alcune schermate che saranno create.</span><span class="sxs-lookup"><span data-stu-id="e37cb-139">Here are a few of the screens you'll create.</span></span>

![Pagina Student Index (Indice degli studenti)](intro/_static/students-index.png)

![Pagina Students Edit (Modifica studenti)](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="e37cb-142">Crea app Web</span><span class="sxs-lookup"><span data-stu-id="e37cb-142">Create web app</span></span>

* <span data-ttu-id="e37cb-143">Aprire Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e37cb-143">Open Visual Studio.</span></span>

* <span data-ttu-id="e37cb-144">Scegliere **Nuovo > Progetto** dal menu **File**.</span><span class="sxs-lookup"><span data-stu-id="e37cb-144">From the **File** menu, select **New > Project**.</span></span>

* <span data-ttu-id="e37cb-145">Nel riquadro sinistro selezionare **Installato > Visual C# > Web**.</span><span class="sxs-lookup"><span data-stu-id="e37cb-145">From the left pane, select **Installed > Visual C# > Web**.</span></span>

* <span data-ttu-id="e37cb-146">Selezionare il modello di progetto **Applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="e37cb-146">Select the **ASP.NET Core Web Application** project template.</span></span>

* <span data-ttu-id="e37cb-147">Immettere **ContosoUniversity** come nome e fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="e37cb-147">Enter **ContosoUniversity** as the name and click **OK**.</span></span>

  ![Finestra di dialogo Nuovo progetto](intro/_static/new-project2.png)

* <span data-ttu-id="e37cb-149">Attendere che venga visualizzata la finestra di dialogo **Nuova applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="e37cb-149">Wait for the **New ASP.NET Core Web Application** dialog to appear.</span></span>

* <span data-ttu-id="e37cb-150">Selezionare **.NET Core**, **ASP.NET Core 2.2** e il modello **Applicazione Web (MVC)**.</span><span class="sxs-lookup"><span data-stu-id="e37cb-150">Select **.NET Core**, **ASP.NET Core 2.2** and the **Web Application (Model-View-Controller)** template.</span></span>

* <span data-ttu-id="e37cb-151">Assicurarsi che **l'autenticazione** sia impostata su **Nessuna autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="e37cb-151">Make sure **Authentication** is set to **No Authentication**.</span></span>

* <span data-ttu-id="e37cb-152">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="e37cb-152">Select **OK**</span></span>

  ![Finestra di dialogo Nuovo progetto ASP.NET Core](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a><span data-ttu-id="e37cb-154">Impostare lo stile del sito</span><span class="sxs-lookup"><span data-stu-id="e37cb-154">Set up the site style</span></span>

<span data-ttu-id="e37cb-155">Con alcune modifiche è possibile impostare il menu del sito, il layout e la home page.</span><span class="sxs-lookup"><span data-stu-id="e37cb-155">A few simple changes will set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="e37cb-156">Aprire *Views/Shared/_Layout.cshtml* e apportare le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="e37cb-156">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="e37cb-157">Modificare tutte le occorrenze di "ContosoUniversity" in "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="e37cb-157">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="e37cb-158">Le occorrenze sono tre.</span><span class="sxs-lookup"><span data-stu-id="e37cb-158">There are three occurrences.</span></span>

* <span data-ttu-id="e37cb-159">Aggiungere le voci di menu per **About** (Informazioni su), **Students** (Studenti), **Courses** (Corsi), **Instructors** (Insegnanti) e **Departments** (Dipartimenti) ed eliminare la voce di menu **Privacy**.</span><span class="sxs-lookup"><span data-stu-id="e37cb-159">Add menu entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="e37cb-160">Le modifiche sono evidenziate.</span><span class="sxs-lookup"><span data-stu-id="e37cb-160">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

<span data-ttu-id="e37cb-161">In *Views/Home/Index.cshtml* sostituire il contenuto del file con il codice seguente. In questo modo il testo su ASP.NET e MVC sarà sostituito dal testo relativo a questa applicazione:</span><span class="sxs-lookup"><span data-stu-id="e37cb-161">In *Views/Home/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

<span data-ttu-id="e37cb-162">Premere CTRL+F5 per eseguire il progetto oppure selezionare **Debug > Avvia senza eseguire debug** dal menu.</span><span class="sxs-lookup"><span data-stu-id="e37cb-162">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="e37cb-163">La home page sarà visualizzata con le schede create in queste esercitazioni.</span><span class="sxs-lookup"><span data-stu-id="e37cb-163">You see the home page with tabs for the pages you'll create in these tutorials.</span></span>

![Home page di Contoso University](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a><span data-ttu-id="e37cb-165">Informazioni sui pacchetti NuGet di EF Core</span><span class="sxs-lookup"><span data-stu-id="e37cb-165">About EF Core NuGet packages</span></span>

<span data-ttu-id="e37cb-166">Per aggiungere il supporto di Entity Framework Core a un progetto, installare il provider di database di destinazione.</span><span class="sxs-lookup"><span data-stu-id="e37cb-166">To add EF Core support to a project, install the database provider that you want to target.</span></span> <span data-ttu-id="e37cb-167">Questa esercitazione usa SQL Server e il pacchetto del provider è [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="e37cb-167">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span> <span data-ttu-id="e37cb-168">Poiché il pacchetto è incluso nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), non è necessario inserire alcun riferimento al pacchetto.</span><span class="sxs-lookup"><span data-stu-id="e37cb-168">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to reference the package.</span></span>

<span data-ttu-id="e37cb-169">Il pacchetto EF SQL Server e le relative dipendenze (`Microsoft.EntityFrameworkCore` e `Microsoft.EntityFrameworkCore.Relational`) offrono supporto di runtime per Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="e37cb-169">The EF SQL Server package and its dependencies (`Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`) provide runtime support for EF.</span></span> <span data-ttu-id="e37cb-170">Nell'esercitazione [Migrazioni](migrations.md) viene aggiunto un pacchetto di strumenti.</span><span class="sxs-lookup"><span data-stu-id="e37cb-170">You'll add a tooling package later, in the [Migrations](migrations.md) tutorial.</span></span>

<span data-ttu-id="e37cb-171">Per informazioni su altri provider di database disponibili per Entity Framework Core, vedere [Provider di database](/ef/core/providers/).</span><span class="sxs-lookup"><span data-stu-id="e37cb-171">For information about other database providers that are available for Entity Framework Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="e37cb-172">Creare il modello di dati</span><span class="sxs-lookup"><span data-stu-id="e37cb-172">Create the data model</span></span>

<span data-ttu-id="e37cb-173">A questo punto è possibile creare le classi delle entità per l'applicazione di Contoso University.</span><span class="sxs-lookup"><span data-stu-id="e37cb-173">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="e37cb-174">Si inizia con le tre entità seguenti.</span><span class="sxs-lookup"><span data-stu-id="e37cb-174">You'll start with the following three entities.</span></span>

![Diagramma modello di dati Course/Enrollment/Student (Corso/Iscrizione/Studente)](intro/_static/data-model-diagram.png)

<span data-ttu-id="e37cb-176">Esiste una relazione uno-a-molti tra le entità `Student` e `Enrollment` ed esiste una relazione uno-a-molti tra le entità `Course` e `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-176">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="e37cb-177">In altre parole, uno studente può iscriversi a un numero qualsiasi di corsi e un corso può avere un numero qualsiasi di studenti iscritti.</span><span class="sxs-lookup"><span data-stu-id="e37cb-177">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="e37cb-178">Nelle sezioni seguenti viene creata una classe per ognuna di queste entità.</span><span class="sxs-lookup"><span data-stu-id="e37cb-178">In the following sections you'll create a class for each one of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="e37cb-179">Entità Student (Studente)</span><span class="sxs-lookup"><span data-stu-id="e37cb-179">The Student entity</span></span>

![Diagramma entità Student (Studente)](intro/_static/student-entity.png)

<span data-ttu-id="e37cb-181">Nella cartella *Models* (Modelli) creare un file di classe denominato *Student.cs* e sostituire il codice del modello con il codice seguente.</span><span class="sxs-lookup"><span data-stu-id="e37cb-181">In the *Models* folder, create a class file named *Student.cs* and replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="e37cb-182">La proprietà `ID` diventa la colonna di chiave primaria della tabella di database che corrisponde a questa classe.</span><span class="sxs-lookup"><span data-stu-id="e37cb-182">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="e37cb-183">Per impostazione predefinita, Entity Framework interpreta una proprietà denominata `ID` o `classnameID` come chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="e37cb-183">By default, the Entity Framework interprets a property that's named `ID` or `classnameID` as the primary key.</span></span>

<span data-ttu-id="e37cb-184">La proprietà `Enrollments` rappresenta una [proprietà di navigazione](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="e37cb-184">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="e37cb-185">Le proprietà di navigazione contengono altre entità correlate a questa entità.</span><span class="sxs-lookup"><span data-stu-id="e37cb-185">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="e37cb-186">In questo caso la proprietà `Enrollments` di `Student entity` contiene tutte le entità `Enrollment` correlate all'entità `Student`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-186">In this case, the `Enrollments` property of a `Student entity` will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="e37cb-187">In altre parole, se una determinata riga Student (Studente) nel database contiene due righe Enrollment (Iscrizione) correlate (le righe che contengono il valore della chiave primaria dello studente nella colonna della chiave esterna StudentID), la proprietà di navigazione `Enrollments` dell'entità `Student` contiene quelle due entità `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-187">In other words, if a given Student row in the database has two related Enrollment rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="e37cb-188">Se una proprietà di navigazione può contenere più entità (come nel caso di relazioni molti-a-molti e uno-a-molti), il tipo della proprietà deve essere un elenco in cui le voci possono essere aggiunte, eliminate e aggiornate, come ad esempio `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-188">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection<T>`.</span></span> <span data-ttu-id="e37cb-189">È possibile specificare `ICollection<T>` o un tipo come `List<T>` o `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-189">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="e37cb-190">Se si specifica `ICollection<T>`, per impostazione predefinita EF crea una raccolta `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-190">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="e37cb-191">Entità Enrollment (Iscrizione)</span><span class="sxs-lookup"><span data-stu-id="e37cb-191">The Enrollment entity</span></span>

![Diagramma entità Enrollment (Iscrizione)](intro/_static/enrollment-entity.png)

<span data-ttu-id="e37cb-193">Nella cartella *Models* creare *Enrollment.cs* e sostituire il codice esistente con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="e37cb-193">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="e37cb-194">La proprietà `EnrollmentID` sarà la chiave primaria. Questa entità usa il criterio `classnameID` anziché `ID` come descritto per l'entità `Student`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-194">The `EnrollmentID` property will be the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="e37cb-195">In genere si sceglie un criterio e lo si usa poi in tutto il modello di dati.</span><span class="sxs-lookup"><span data-stu-id="e37cb-195">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="e37cb-196">In questo caso la variazione illustra che è possibile sia uno sia l'altro criterio.</span><span class="sxs-lookup"><span data-stu-id="e37cb-196">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="e37cb-197">In un'[esercitazione successiva](inheritance.md) viene illustrato l'uso di ID senza classname. In questo modo si semplifica l'implementazione dell'ereditarietà nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="e37cb-197">In a [later tutorial](inheritance.md), you'll see how using ID without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="e37cb-198">La proprietà `Grade` è un oggetto`enum`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-198">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="e37cb-199">Il punto interrogativo dopo la dichiarazione del tipo `Grade` indica che la proprietà `Grade` ammette i valori Null.</span><span class="sxs-lookup"><span data-stu-id="e37cb-199">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="e37cb-200">Un voto il cui valore è Null è diverso da un voto con valore zero. Null significa che un voto è sconosciuto o non è stato ancora assegnato.</span><span class="sxs-lookup"><span data-stu-id="e37cb-200">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="e37cb-201">La proprietà `StudentID` è una chiave esterna e la proprietà di navigazione corrispondente è `Student`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-201">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="e37cb-202">Un'entità `Enrollment` è associata a un'entità `Student`, pertanto la proprietà può contenere un'unica entità `Student`, a differenza della proprietà di navigazione `Student.Enrollments` vista in precedenza, che può contenere più entità `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-202">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="e37cb-203">La proprietà `CourseID` è una chiave esterna e la proprietà di navigazione corrispondente è `Course`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-203">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="e37cb-204">Un'entità `Enrollment` è associata a un'entità `Course`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-204">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="e37cb-205">Entity Framework interpretata una proprietà come proprietà di chiave esterna se è denominata `<navigation property name><primary key property name>`, ad esempio `StudentID` per la proprietà di navigazione `Student` poiché la chiave primaria dell'entità `Student` è `ID`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-205">Entity Framework interprets a property as a foreign key property if it's named `<navigation property name><primary key property name>` (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="e37cb-206">Le proprietà di una chiave esterna possono anche essere denominate semplicemente `<primary key property name>`, ad esempio `CourseID` poiché la chiave primaria dell'entità `Course` è `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-206">Foreign key properties can also be named simply `<primary key property name>` (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="e37cb-207">Entità Course (Corso)</span><span class="sxs-lookup"><span data-stu-id="e37cb-207">The Course entity</span></span>

![Diagramma entità Course (Corso)](intro/_static/course-entity.png)

<span data-ttu-id="e37cb-209">Nella cartella *Models* creare *Course.cs* e sostituire il codice esistente con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="e37cb-209">In the *Models* folder, create *Course.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="e37cb-210">La proprietà `Enrollments` rappresenta una proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="e37cb-210">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="e37cb-211">È possibile correlare un'entità `Course` a un numero qualsiasi di entità `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-211">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="e37cb-212">Altre informazioni sull'attributo `DatabaseGenerated` sono disponibili nell'[esercitazione successiva](complex-data-model.md) di questa serie.</span><span class="sxs-lookup"><span data-stu-id="e37cb-212">We'll say more about the `DatabaseGenerated` attribute in a [later tutorial](complex-data-model.md) in this series.</span></span> <span data-ttu-id="e37cb-213">In pratica, questo attributo consente di immettere la chiave primaria per il corso invece di essere generata dal database.</span><span class="sxs-lookup"><span data-stu-id="e37cb-213">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="e37cb-214">Creare il contesto di database</span><span class="sxs-lookup"><span data-stu-id="e37cb-214">Create the database context</span></span>

<span data-ttu-id="e37cb-215">La classe del contesto di database è la classe principale che coordina le funzionalità di Entity Framework per un determinato modello di dati.</span><span class="sxs-lookup"><span data-stu-id="e37cb-215">The main class that coordinates Entity Framework functionality for a given data model is the database context class.</span></span> <span data-ttu-id="e37cb-216">Questa classe viene creata mediante derivazione dalla classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-216">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="e37cb-217">Nel codice vengono specificate le entità incluse nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="e37cb-217">In your code you specify which entities are included in the data model.</span></span> <span data-ttu-id="e37cb-218">È anche possibile personalizzare un determinato comportamento di Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="e37cb-218">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="e37cb-219">In questo progetto la classe è denominata `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-219">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="e37cb-220">Creare una cartella denominata *Data* (Dati) nella cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="e37cb-220">In the project folder, create a folder named *Data*.</span></span>

<span data-ttu-id="e37cb-221">Nella cartella *Data* (Dati) creare un file di classe denominato *SchoolContext.cs* e sostituire il codice del modello con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="e37cb-221">In the *Data* folder create a new class file named *SchoolContext.cs*, and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="e37cb-222">Questo codice crea una proprietà `DbSet` per ogni set di entità.</span><span class="sxs-lookup"><span data-stu-id="e37cb-222">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="e37cb-223">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database e un'entità corrisponde a una riga della tabella.</span><span class="sxs-lookup"><span data-stu-id="e37cb-223">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e37cb-224">Se sono state omesse le istruzioni `DbSet<Enrollment>` e `DbSet<Course>`, potrebbe comunque funzionare.</span><span class="sxs-lookup"><span data-stu-id="e37cb-224">You could've omitted the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="e37cb-225">Entity Framework le include in modo implicito perché l'entità `Student` fa riferimento all'entità `Enrollment` e l'entità `Enrollment` fa riferimento all'entità `Course`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-225">The Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="e37cb-226">Quando viene creato il database, Entity Framework crea tabelle i cui nomi sono gli stessi della proprietà `DbSet`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-226">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="e37cb-227">I nomi di proprietà per le raccolte sono generalmente usati al plurale (Students anziché Student). Gli sviluppatori non hanno tuttavia un'opinione unanime sul fatto che i nomi di tabella debbano essere usati al plurale.</span><span class="sxs-lookup"><span data-stu-id="e37cb-227">Property names for collections are typically plural (Students rather than Student), but developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="e37cb-228">Per queste esercitazioni, viene eseguito l'override del comportamento predefinito specificando nomi di tabella al singolare in DbContext.</span><span class="sxs-lookup"><span data-stu-id="e37cb-228">For these tutorials you'll override the default behavior by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="e37cb-229">A tale scopo, aggiungere il codice evidenziato seguente dopo l'ultima proprietà DbSet.</span><span class="sxs-lookup"><span data-stu-id="e37cb-229">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a><span data-ttu-id="e37cb-230">Registrare SchoolContext</span><span class="sxs-lookup"><span data-stu-id="e37cb-230">Register the SchoolContext</span></span>

<span data-ttu-id="e37cb-231">ASP.NET Core implementa l'[inserimento delle dipendenze](../../fundamentals/dependency-injection.md) per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="e37cb-231">ASP.NET Core implements [dependency injection](../../fundamentals/dependency-injection.md) by default.</span></span> <span data-ttu-id="e37cb-232">I servizi, ad esempio il contesto di database di Entity Framework, vengono registrati con l'inserimento delle dipendenze durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="e37cb-232">Services (such as the EF database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e37cb-233">Questi servizi vengono quindi offerti ai componenti per cui sono necessari (ad esempio i controller MVC) tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="e37cb-233">Components that require these services (such as MVC controllers) are provided these services via constructor parameters.</span></span> <span data-ttu-id="e37cb-234">Il codice del costruttore del controller che ottiene un'istanza di contesto viene illustrato più avanti in questa esercitazione.</span><span class="sxs-lookup"><span data-stu-id="e37cb-234">You'll see the controller constructor code that gets a context instance later in this tutorial.</span></span>

<span data-ttu-id="e37cb-235">Per registrare `SchoolContext` come servizio, aprire *Startup.cs* e aggiungere le righe evidenziate al metodo `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-235">To register `SchoolContext` as a service, open *Startup.cs*, and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

<span data-ttu-id="e37cb-236">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto `DbContextOptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-236">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="e37cb-237">Per lo sviluppo locale, il [sistema di configurazione di ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="e37cb-237">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="e37cb-238">Aggiungere le istruzioni `using` per gli spazi dei nomi `ContosoUniversity.Data` e `Microsoft.EntityFrameworkCore`, quindi compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="e37cb-238">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces, and then build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="e37cb-239">Aprire il file *appsettings.json* e aggiungere una stringa di connessione come illustrato nel codice seguente.</span><span class="sxs-lookup"><span data-stu-id="e37cb-239">Open the *appsettings.json* file and add a connection string as shown in the following example.</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a><span data-ttu-id="e37cb-240">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="e37cb-240">SQL Server Express LocalDB</span></span>

<span data-ttu-id="e37cb-241">La stringa di connessione specifica un database Local DB di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="e37cb-241">The connection string specifies a SQL Server LocalDB database.</span></span> <span data-ttu-id="e37cb-242">Local DB è una versione leggera del motore di database di SQL Server Express appositamente pensato per lo sviluppo di applicazioni e non per la produzione.</span><span class="sxs-lookup"><span data-stu-id="e37cb-242">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for application development, not production use.</span></span> <span data-ttu-id="e37cb-243">Local DB viene avviato su richiesta ed eseguito in modalità utente; non richiede quindi una configurazione complessa.</span><span class="sxs-lookup"><span data-stu-id="e37cb-243">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="e37cb-244">Per impostazione predefinita, Local DB crea file di database con estensione *mdf* nella directory `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-244">By default, LocalDB creates *.mdf* database files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="e37cb-245">Inizializzare il database con dati di test</span><span class="sxs-lookup"><span data-stu-id="e37cb-245">Initialize DB with test data</span></span>

<span data-ttu-id="e37cb-246">Entity Framework creerà un database vuoto.</span><span class="sxs-lookup"><span data-stu-id="e37cb-246">The Entity Framework will create an empty database for you.</span></span> <span data-ttu-id="e37cb-247">In questa sezione viene scritto un metodo che viene chiamato dopo aver creato il database al fine di popolare il database con i dati di test.</span><span class="sxs-lookup"><span data-stu-id="e37cb-247">In this section, you write a method that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="e37cb-248">Per creare automaticamente il database, viene usato il metodo `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-248">Here you'll use the `EnsureCreated` method to automatically create the database.</span></span> <span data-ttu-id="e37cb-249">In un'[esercitazione successiva](migrations.md) viene spiegato come gestire le modifiche al modello usando Migrazioni Code First, che consente di modificare lo schema del database anziché dover eliminare e ricreare il database.</span><span class="sxs-lookup"><span data-stu-id="e37cb-249">In a [later tutorial](migrations.md) you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="e37cb-250">Nella cartella *Data* (Dati) creare un file di classe denominato *DbInitializer.cs* e sostituire il codice del modello con il codice seguente. Verrà creato un database se necessario e i dati di test saranno caricati nel nuovo database.</span><span class="sxs-lookup"><span data-stu-id="e37cb-250">In the *Data* folder, create a new class file named *DbInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="e37cb-251">Il codice verifica l'esistenza di studenti nel database. In caso negativo presuppone che il database sia nuovo e che sia necessario eseguire l'inizializzazione con i dati di test.</span><span class="sxs-lookup"><span data-stu-id="e37cb-251">The code checks if there are any students in the database, and if not, it assumes the database is new and needs to be seeded with test data.</span></span> <span data-ttu-id="e37cb-252">I dati di test vengono caricati in matrici anziché in raccolte `List<T>` per ottimizzare le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="e37cb-252">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="e37cb-253">In *Program.cs* modificare il metodo `Main` per eseguire le operazioni seguenti all'avvio dell'applicazione:</span><span class="sxs-lookup"><span data-stu-id="e37cb-253">In *Program.cs*, modify the `Main` method to do the following on application startup:</span></span>

* <span data-ttu-id="e37cb-254">Ottenere un'istanza del contesto di database dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="e37cb-254">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="e37cb-255">Chiamare il metodo di inizializzazione, passandolo al contesto.</span><span class="sxs-lookup"><span data-stu-id="e37cb-255">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="e37cb-256">Eliminare il contesto dopo che il metodo di inizializzazione è stato completato.</span><span class="sxs-lookup"><span data-stu-id="e37cb-256">Dispose the context when the seed method is done.</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

<span data-ttu-id="e37cb-257">Aggiungere le istruzioni `using`:</span><span class="sxs-lookup"><span data-stu-id="e37cb-257">Add `using` statements:</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Usings)]

<span data-ttu-id="e37cb-258">Nelle esercitazioni precedenti il codice nel metodo `Configure` in *Startup.cs* può essere simile.</span><span class="sxs-lookup"><span data-stu-id="e37cb-258">In older tutorials, you may see similar code in the `Configure` method in *Startup.cs*.</span></span> <span data-ttu-id="e37cb-259">È consigliabile usare il metodo `Configure` solo per impostare la pipeline delle richieste.</span><span class="sxs-lookup"><span data-stu-id="e37cb-259">We recommend that you use the `Configure` method only to set up the request pipeline.</span></span> <span data-ttu-id="e37cb-260">Il codice di avvio dell'applicazione fa parte del metodo `Main`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-260">Application startup code belongs in the `Main` method.</span></span>

<span data-ttu-id="e37cb-261">A questo punto l'applicazione viene eseguita per la prima volta. Il database viene creato e inizializzato con i dati di test.</span><span class="sxs-lookup"><span data-stu-id="e37cb-261">Now the first time you run the application, the database will be created and seeded with test data.</span></span> <span data-ttu-id="e37cb-262">Ogni volta che si modifica il modello di dati, è possibile eliminare il database, aggiornare il metodo di inizializzazione e ricominciare con un nuovo database allo stesso modo.</span><span class="sxs-lookup"><span data-stu-id="e37cb-262">Whenever you change your data model, you can delete the database, update your seed method, and start afresh with a new database the same way.</span></span> <span data-ttu-id="e37cb-263">Nelle esercitazioni successive viene illustrato come modificare il database alla modifica del modello di dati, senza eliminare e ricreare il database.</span><span class="sxs-lookup"><span data-stu-id="e37cb-263">In later tutorials, you'll see how to modify the database when the data model changes, without deleting and re-creating it.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="e37cb-264">Creare controller e visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="e37cb-264">Create controller and views</span></span>

<span data-ttu-id="e37cb-265">A questo punto viene usato il motore di scaffolding in Visual Studio per aggiungere un controller MVC e le visualizzazioni che saranno usate da Entity Framework per eseguire le query e salvare i dati.</span><span class="sxs-lookup"><span data-stu-id="e37cb-265">Next, you'll use the scaffolding engine in Visual Studio to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="e37cb-266">La creazione automatica di metodi di azione CRUD e di visualizzazioni è nota come scaffolding.</span><span class="sxs-lookup"><span data-stu-id="e37cb-266">The automatic creation of CRUD action methods and views is known as scaffolding.</span></span> <span data-ttu-id="e37cb-267">Lo scaffolding differisce dalla generazione di codice in quanto il codice di scaffolding è un punto di partenza modificabile per essere adattato ai propri requisiti, mentre generalmente il codice generato non viene modificato.</span><span class="sxs-lookup"><span data-stu-id="e37cb-267">Scaffolding differs from code generation in that the scaffolded code is a starting point that you can modify to suit your own requirements, whereas you typically don't modify generated code.</span></span> <span data-ttu-id="e37cb-268">Quando è necessario personalizzare il codice generato, usare classi parziali oppure rigenerare il codice in caso di modifiche.</span><span class="sxs-lookup"><span data-stu-id="e37cb-268">When you need to customize generated code, you use partial classes or you regenerate the code when things change.</span></span>

* <span data-ttu-id="e37cb-269">Fare clic con il pulsante destro del mouse sulla cartella **Controller** in **Esplora soluzioni** e scegliere **Aggiungi -> Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="e37cb-269">Right-click the **Controllers** folder in **Solution Explorer** and select **Add > New Scaffolded Item**.</span></span>

* <span data-ttu-id="e37cb-270">Nella finestra di dialogo **Aggiungi scaffolding** eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="e37cb-270">In the **Add Scaffold** dialog box:</span></span>

  * <span data-ttu-id="e37cb-271">Selezionare **Controller MVC con visualizzazioni, che usa Entity Framework**.</span><span class="sxs-lookup"><span data-stu-id="e37cb-271">Select **MVC controller with views, using Entity Framework**.</span></span>

  * <span data-ttu-id="e37cb-272">Scegliere **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="e37cb-272">Click **Add**.</span></span> <span data-ttu-id="e37cb-273">Verrà visualizzata la finestra di dialogo **Aggiungi Controller MVC con visualizzazioni, che usa Entity Framework**.</span><span class="sxs-lookup"><span data-stu-id="e37cb-273">The **Add MVC Controller with views, using Entity Framework** dialog box appears.</span></span>

    ![Scaffolding di Student](intro/_static/scaffold-student2.png)

  * <span data-ttu-id="e37cb-275">In **Classe modello** selezionare **Student** (Studente).</span><span class="sxs-lookup"><span data-stu-id="e37cb-275">In **Model class** select **Student**.</span></span>

  * <span data-ttu-id="e37cb-276">In **Classe contesto di dati** selezionare **SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="e37cb-276">In **Data context class** select **SchoolContext**.</span></span>

  * <span data-ttu-id="e37cb-277">Accettare il valore predefinito **StudentsController** come nome.</span><span class="sxs-lookup"><span data-stu-id="e37cb-277">Accept the default **StudentsController** as the name.</span></span>

  * <span data-ttu-id="e37cb-278">Scegliere **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="e37cb-278">Click **Add**.</span></span>

  <span data-ttu-id="e37cb-279">Quando si fa clic su **Aggiungi**, il motore di scaffolding di Visual Studio crea un file *StudentsController.cs* e un set di visualizzazioni (file con estensione *cshtml*) che vengono usate insieme al controller.</span><span class="sxs-lookup"><span data-stu-id="e37cb-279">When you click **Add**, the Visual Studio scaffolding engine creates a *StudentsController.cs* file and a set of views (*.cshtml* files) that work with the controller.</span></span>

<span data-ttu-id="e37cb-280">Il motore di scaffolding può anche creare il contesto del database se non è stato precedentemente creato in modo manuale, come all'inizio di questa esercitazione.</span><span class="sxs-lookup"><span data-stu-id="e37cb-280">(The scaffolding engine can also create the database context for you if you don't create it manually first as you did earlier for this tutorial.</span></span> <span data-ttu-id="e37cb-281">È possibile specificare una nuova classe di contesto nella casella **Aggiungi controller** facendo clic sul segno + a destra della casella di **Classe contesto di dati**.</span><span class="sxs-lookup"><span data-stu-id="e37cb-281">You can specify a new context class in the **Add Controller** box by clicking the plus sign to the right of **Data context class**.</span></span>  <span data-ttu-id="e37cb-282">Visual Studio creerà quindi la classe `DbContext` nonché il controller e le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="e37cb-282">Visual Studio will then create your `DbContext` class as well as the controller and views.)</span></span>

<span data-ttu-id="e37cb-283">Si noti che il controller usa `SchoolContext` come parametro del costruttore.</span><span class="sxs-lookup"><span data-stu-id="e37cb-283">You'll notice that the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="e37cb-284">L'inserimento delle dipendenze di ASP.NET Core si occupa di passare un'istanza di `SchoolContext` al controller.</span><span class="sxs-lookup"><span data-stu-id="e37cb-284">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="e37cb-285">Tale comportamento è configurato nel file *Startup.cs* in precedenza.</span><span class="sxs-lookup"><span data-stu-id="e37cb-285">You configured that in the *Startup.cs* file earlier.</span></span>

<span data-ttu-id="e37cb-286">Il controller contiene un metodo di azione `Index` che consente di visualizzare tutti gli studenti nel database.</span><span class="sxs-lookup"><span data-stu-id="e37cb-286">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="e37cb-287">Il metodo ottiene un elenco degli studenti dal set di entità Students (Studenti) leggendo la proprietà `Students` dell'istanza del contesto di database:</span><span class="sxs-lookup"><span data-stu-id="e37cb-287">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="e37cb-288">Gli elementi di programmazione asincrona in questo codice vengono illustrati più avanti nell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="e37cb-288">You'll learn about the asynchronous programming elements in this code later in the tutorial.</span></span>

<span data-ttu-id="e37cb-289">Nella visualizzazione *Views/Students/Index.cshtml* è contenuto l'elenco sotto forma di tabella:</span><span class="sxs-lookup"><span data-stu-id="e37cb-289">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="e37cb-290">Premere CTRL+F5 per eseguire il progetto oppure selezionare **Debug > Avvia senza eseguire debug** dal menu.</span><span class="sxs-lookup"><span data-stu-id="e37cb-290">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="e37cb-291">Fare clic sulla scheda Students (Studenti) per visualizzare i dati di test inseriti nel metodo `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-291">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="e37cb-292">A seconda delle dimensione della finestra del browser, il collegamento della scheda `Students` viene visualizzato in alto alla pagina oppure è necessario selezionare l'icona di spostamento in alto a destra per visualizzarlo.</span><span class="sxs-lookup"><span data-stu-id="e37cb-292">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Home page di Contoso University ristretta](intro/_static/home-page-narrow.png)

![Pagina Student Index (Indice degli studenti)](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="e37cb-295">Visualizzare il database</span><span class="sxs-lookup"><span data-stu-id="e37cb-295">View the database</span></span>

<span data-ttu-id="e37cb-296">Dopo aver avviato l'applicazione, il metodo `DbInitializer.Initialize` chiama `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-296">When you started the application, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="e37cb-297">Entity Framework ha verificato che non esiste un database e ne ha pertanto creato uno. La parte restante di codice del metodo `Initialize` ha popolato il database con i dati.</span><span class="sxs-lookup"><span data-stu-id="e37cb-297">EF saw that there was no database and so it created one, then the remainder of the `Initialize` method code populated the database with data.</span></span> <span data-ttu-id="e37cb-298">È possibile usare \*\*Esplora oggetti di SQL Server \*\* per visualizzare il database in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e37cb-298">You can use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span>

<span data-ttu-id="e37cb-299">Chiudere il browser.</span><span class="sxs-lookup"><span data-stu-id="e37cb-299">Close the browser.</span></span>

<span data-ttu-id="e37cb-300">Se la finestra di Esplora oggetti di SQL Server non è già aperta, selezionarla dal menu **Visualizza** in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e37cb-300">If the SSOX window isn't already open, select it from the **View** menu in Visual Studio.</span></span>

<span data-ttu-id="e37cb-301">In Esplora oggetti di SQL Server fare clic su **(localdb) \MSSQLLocalDB > Database** e fare clic sulla voce che corrisponde al nome del database contenuto nella stringa di connessione nel file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="e37cb-301">In SSOX, click **(localdb)\MSSQLLocalDB > Databases**, and then click the entry for the database name that's in the connection string in your *appsettings.json* file.</span></span>

<span data-ttu-id="e37cb-302">Espandere il nodo **Tabelle** per visualizzare le tabelle nel database.</span><span class="sxs-lookup"><span data-stu-id="e37cb-302">Expand the **Tables** node to see the tables in your database.</span></span>

![Tabelle in SSOX](intro/_static/ssox-tables.png)

<span data-ttu-id="e37cb-304">Fare clic con il pulsante destro del mouse sulla tabella **Student** (Studente) e fare clic su **Visualizza dati** per visualizzare le colonne create e le righe inserite nella tabella.</span><span class="sxs-lookup"><span data-stu-id="e37cb-304">Right-click the **Student** table and click **View Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

![Tabella Student (Studente) in Esplora oggetti di SQL Server](intro/_static/ssox-student-table.png)

<span data-ttu-id="e37cb-306">I file di database con estensione *mdf* e *ldf* sono contenuti nella cartella *C:\Utenti\\\<yourusername>*.</span><span class="sxs-lookup"><span data-stu-id="e37cb-306">The *.mdf* and *.ldf* database files are in the *C:\Users\\\<yourusername>* folder.</span></span>

<span data-ttu-id="e37cb-307">Poiché si sta chiamando `EnsureCreated` nel metodo di inizializzatore che viene eseguito all'avvio dell'app, è ora possibile modificare la classe `Student`, eliminare il database ed eseguire nuovamente l'applicazione. Il database sarà automaticamente ricreato e rispecchierà la modifica.</span><span class="sxs-lookup"><span data-stu-id="e37cb-307">Because you're calling `EnsureCreated` in the initializer method that runs on app start, you could now make a change to the `Student` class, delete the database, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="e37cb-308">Ad esempio, se si aggiunge una proprietà `EmailAddress` alla classe `Student`, una nuova colonna `EmailAddress` sarà visualizzata nella tabella ricreata.</span><span class="sxs-lookup"><span data-stu-id="e37cb-308">For example, if you add an `EmailAddress` property to the `Student` class, you'll see a new `EmailAddress` column in the re-created table.</span></span>

## <a name="conventions"></a><span data-ttu-id="e37cb-309">Convenzioni</span><span class="sxs-lookup"><span data-stu-id="e37cb-309">Conventions</span></span>

<span data-ttu-id="e37cb-310">Grazie all'uso di convenzioni o di ipotesi di Entity Framework, la quantità di codice scritto che è necessario a Entity Framework per creare un database completo è minino.</span><span class="sxs-lookup"><span data-stu-id="e37cb-310">The amount of code you had to write in order for the Entity Framework to be able to create a complete database for you is minimal because of the use of conventions, or assumptions that the Entity Framework makes.</span></span>

* <span data-ttu-id="e37cb-311">I nomi delle proprietà `DbSet` vengono usate come nomi di tabella.</span><span class="sxs-lookup"><span data-stu-id="e37cb-311">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="e37cb-312">Per le entità a cui non fa riferimento una proprietà `DbSet`, i nomi della classe di entità vengono usati come nome di tabella.</span><span class="sxs-lookup"><span data-stu-id="e37cb-312">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>

* <span data-ttu-id="e37cb-313">I nomi della proprietà di entità vengono usati come nomi di colonna.</span><span class="sxs-lookup"><span data-stu-id="e37cb-313">Entity property names are used for column names.</span></span>

* <span data-ttu-id="e37cb-314">Le proprietà dell'entità vengono denominate ID o classnameID e vengono riconosciute come proprietà della chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="e37cb-314">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>

* <span data-ttu-id="e37cb-315">Una proprietà viene interpretata come una proprietà di chiave esterna se è denominata *\<navigation property name>\<primary key property name>* , ad esempio `StudentID` per la `Student` proprietà di navigazione poiché la `Student` chiave primaria dell'entità è `ID` .</span><span class="sxs-lookup"><span data-stu-id="e37cb-315">A property is interpreted as a foreign key property if it's named *\<navigation property name>\<primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="e37cb-316">Le proprietà di chiave esterna possono anche essere denominate semplicemente *\<primary key property name>* , ad esempio `EnrollmentID` poiché la `Enrollment` chiave primaria dell'entità è `EnrollmentID` .</span><span class="sxs-lookup"><span data-stu-id="e37cb-316">Foreign key properties can also be named simply *\<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="e37cb-317">È possibile eseguire l'override del comportamento convenzionale.</span><span class="sxs-lookup"><span data-stu-id="e37cb-317">Conventional behavior can be overridden.</span></span> <span data-ttu-id="e37cb-318">Ad esempio, è possibile specificare in modo esplicito i nomi di tabella, come illustrato in precedenza in questa esercitazione.</span><span class="sxs-lookup"><span data-stu-id="e37cb-318">For example, you can explicitly specify table names, as you saw earlier in this tutorial.</span></span> <span data-ttu-id="e37cb-319">È anche possibile impostare i nomi delle colonne e impostare qualsiasi proprietà come chiave primaria o chiave esterna, come sarà spiegato in un'[esercitazione successiva](complex-data-model.md) di questa serie.</span><span class="sxs-lookup"><span data-stu-id="e37cb-319">And you can set column names and set any property as primary key or foreign key, as you'll see in a [later tutorial](complex-data-model.md) in this series.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="e37cb-320">Codice asincrono</span><span class="sxs-lookup"><span data-stu-id="e37cb-320">Asynchronous code</span></span>

<span data-ttu-id="e37cb-321">La programmazione asincrona è la modalità predefinita per ASP.NET Core ed Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="e37cb-321">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="e37cb-322">Per un server Web è disponibile un numero limitato di thread e in situazioni di carico elevato tutti i thread disponibili potrebbero essere in uso.</span><span class="sxs-lookup"><span data-stu-id="e37cb-322">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="e37cb-323">In queste circostanze il server non può elaborare nuove richieste finché i thread non saranno liberi.</span><span class="sxs-lookup"><span data-stu-id="e37cb-323">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="e37cb-324">Con il codice sincrono, può succedere che molti thread siano vincolati nonostante in quel momento non stiano eseguendo alcuna operazione. Rimangono tuttavia in attesa che l'operazione I/O sia completata.</span><span class="sxs-lookup"><span data-stu-id="e37cb-324">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="e37cb-325">Con il codice asincrono, se un processo è in attesa del completamento dell'operazione I/O, il thread viene liberato e il server lo può usare per l'elaborazione di altre richieste.</span><span class="sxs-lookup"><span data-stu-id="e37cb-325">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="e37cb-326">Di conseguenza, il codice asincrono consente un uso più efficiente delle risorse e il server è abilitato per gestire una maggiore quantità di traffico senza ritardi.</span><span class="sxs-lookup"><span data-stu-id="e37cb-326">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="e37cb-327">Il codice asincrono comporta un minimo sovraccarico in fase di esecuzione. In caso di traffico ridotto, il calo delle prestazioni è irrilevante, mentre nelle situazioni di traffico elevato, è essenziale ottimizzare le prestazioni potenziali.</span><span class="sxs-lookup"><span data-stu-id="e37cb-327">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="e37cb-328">Nel codice seguente la parola chiave `async`, il valore restituito `Task<T>`, la parola chiave `await` e il metodo `ToListAsync` consentono di eseguire il codice in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="e37cb-328">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="e37cb-329">La parola chiave`async` indica al compilatore di generare callback per parti del corpo del metodo e di creare automaticamente l'oggetto `Task<IActionResult>` restituito.</span><span class="sxs-lookup"><span data-stu-id="e37cb-329">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>

* <span data-ttu-id="e37cb-330">Il tipo restituito `Task<IActionResult>` rappresenta il lavoro in corso con un risultato di tipo `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-330">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>

* <span data-ttu-id="e37cb-331">La parola chiave `await` indica al compilatore di suddividere il metodo in due parti.</span><span class="sxs-lookup"><span data-stu-id="e37cb-331">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="e37cb-332">La prima parte termina con l'operazione avviata in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="e37cb-332">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="e37cb-333">La seconda parte viene inserita in un metodo di callback che viene chiamato al termine dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="e37cb-333">The second part is put into a callback method that's called when the operation completes.</span></span>

* <span data-ttu-id="e37cb-334">`ToListAsync` è la versione asincrona del metodo di estensione `ToList`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-334">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="e37cb-335">Alcuni aspetti da considerare quando si scrive codice asincrono usato da Entity Framework:</span><span class="sxs-lookup"><span data-stu-id="e37cb-335">Some things to be aware of when you are writing asynchronous code that uses the Entity Framework:</span></span>

* <span data-ttu-id="e37cb-336">Solo le istruzioni che generano query o comandi da inviare al database vengono eseguite in modo asincrono,</span><span class="sxs-lookup"><span data-stu-id="e37cb-336">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="e37cb-337">ad esempio `ToListAsync`, `SingleOrDefaultAsync`, e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-337">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="e37cb-338">Non sono incluse le istruzioni che modificano solo un oggetto `IQueryable`, ad esempio `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-338">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>

* <span data-ttu-id="e37cb-339">Un contesto di Entity Framework non è thread-safe. Non tentare di eseguire più operazioni parallelamente.</span><span class="sxs-lookup"><span data-stu-id="e37cb-339">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="e37cb-340">Quando si chiama un metodo asincrono Entity Framework, usare sempre la parola chiave `await`.</span><span class="sxs-lookup"><span data-stu-id="e37cb-340">When you call any async EF method, always use the `await` keyword.</span></span>

* <span data-ttu-id="e37cb-341">Se si vogliono sfruttare i vantaggi del codice asincrono in termini di prestazioni, verificare che i pacchetti della libreria impiegati, ad esempio per il paging, usino la modalità asincrona per chiamare i metodi di Entity Framework che generano query da inviare al database.</span><span class="sxs-lookup"><span data-stu-id="e37cb-341">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="e37cb-342">Per altre informazioni sulla programmazione asincrona in .NET, vedere [Panoramica della programmazione asincrona](/dotnet/articles/standard/async).</span><span class="sxs-lookup"><span data-stu-id="e37cb-342">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="e37cb-343">Ottenere il codice</span><span class="sxs-lookup"><span data-stu-id="e37cb-343">Get the code</span></span>

[<span data-ttu-id="e37cb-344">Scaricare o visualizzare l'applicazione completata.</span><span class="sxs-lookup"><span data-stu-id="e37cb-344">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="e37cb-345">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="e37cb-345">Next steps</span></span>

<span data-ttu-id="e37cb-346">In questa esercitazione:</span><span class="sxs-lookup"><span data-stu-id="e37cb-346">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e37cb-347">Creare un'app Web ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="e37cb-347">Created ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="e37cb-348">Impostare lo stile del sito</span><span class="sxs-lookup"><span data-stu-id="e37cb-348">Set up the site style</span></span>
> * <span data-ttu-id="e37cb-349">Scoprire di più sui pacchetti NuGet di EF Core</span><span class="sxs-lookup"><span data-stu-id="e37cb-349">Learned about EF Core NuGet packages</span></span>
> * <span data-ttu-id="e37cb-350">Creare il modello di dati</span><span class="sxs-lookup"><span data-stu-id="e37cb-350">Created the data model</span></span>
> * <span data-ttu-id="e37cb-351">Creare il contesto del database</span><span class="sxs-lookup"><span data-stu-id="e37cb-351">Created the database context</span></span>
> * <span data-ttu-id="e37cb-352">Registrare SchoolContext</span><span class="sxs-lookup"><span data-stu-id="e37cb-352">Registered the SchoolContext</span></span>
> * <span data-ttu-id="e37cb-353">Inizializzare il database con dati di test</span><span class="sxs-lookup"><span data-stu-id="e37cb-353">Initialized DB with test data</span></span>
> * <span data-ttu-id="e37cb-354">Creare controller e visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="e37cb-354">Created controller and views</span></span>
> * <span data-ttu-id="e37cb-355">Visualizzare il database</span><span class="sxs-lookup"><span data-stu-id="e37cb-355">Viewed the database</span></span>

<span data-ttu-id="e37cb-356">Nella prossima esercitazione vengono esaminate le operazioni CRUD per creare, leggere, aggiornare, eliminare ed elencare.</span><span class="sxs-lookup"><span data-stu-id="e37cb-356">In the following tutorial, you'll learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

<span data-ttu-id="e37cb-357">Passare all'esercitazione successiva per informazioni su come eseguire operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) di base.</span><span class="sxs-lookup"><span data-stu-id="e37cb-357">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="e37cb-358">Implementare funzionalità CRUD di base</span><span class="sxs-lookup"><span data-stu-id="e37cb-358">Implement basic CRUD functionality</span></span>](crud.md)

