---
title: 'Esercitazione: uso della funzionalità Migrations-ASP.NET MVC con EF Core'
description: In questa esercitazione si inizia a usare la funzionalità delle migrazioni EF Core per la gestione delle modifiche al modello di dati in un'applicazione ASP.NET Core MVC.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/migrations
ms.openlocfilehash: b8701687d97f5fe940e2f39fca9c3f98052660be
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773523"
---
# <a name="tutorial-using-the-migrations-feature---aspnet-mvc-with-ef-core"></a><span data-ttu-id="50498-103">Esercitazione: uso della funzionalità Migrations-ASP.NET MVC con EF Core</span><span class="sxs-lookup"><span data-stu-id="50498-103">Tutorial: Using the migrations feature - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="50498-104">In questa esercitazione si inizia a usare la funzionalità delle migrazioni EF Core per la gestione delle modifiche al modello di dati.</span><span class="sxs-lookup"><span data-stu-id="50498-104">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="50498-105">Nelle esercitazioni successive si aggiungeranno altre migrazioni quando si modifica il modello di dati.</span><span class="sxs-lookup"><span data-stu-id="50498-105">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="50498-106">In questa esercitazione:</span><span class="sxs-lookup"><span data-stu-id="50498-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="50498-107">Ottenere informazioni sulle migrazioni</span><span class="sxs-lookup"><span data-stu-id="50498-107">Learn about migrations</span></span>
> * <span data-ttu-id="50498-108">Modificare la stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="50498-108">Change the connection string</span></span>
> * <span data-ttu-id="50498-109">Creare una migrazione iniziale</span><span class="sxs-lookup"><span data-stu-id="50498-109">Create an initial migration</span></span>
> * <span data-ttu-id="50498-110">Esaminare i metodi Up e Down</span><span class="sxs-lookup"><span data-stu-id="50498-110">Examine Up and Down methods</span></span>
> * <span data-ttu-id="50498-111">Esaminare lo snapshot del modello di dati</span><span class="sxs-lookup"><span data-stu-id="50498-111">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="50498-112">Applicare la migrazione</span><span class="sxs-lookup"><span data-stu-id="50498-112">Apply the migration</span></span>

## <a name="prerequisites"></a><span data-ttu-id="50498-113">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="50498-113">Prerequisites</span></span>

* [<span data-ttu-id="50498-114">Ordinamento, filtro e suddivisione in pagine</span><span class="sxs-lookup"><span data-stu-id="50498-114">Sorting, filtering, and paging</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="50498-115">Informazioni sulle migrazioni</span><span class="sxs-lookup"><span data-stu-id="50498-115">About migrations</span></span>

<span data-ttu-id="50498-116">Quando si sviluppa una nuova applicazione, il modello di dati cambia di frequente e, a ogni cambiamento, non è più sincronizzato con il database.</span><span class="sxs-lookup"><span data-stu-id="50498-116">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="50498-117">La serie di esercitazioni è iniziata con la configurazione di Entity Framework per creare il database se non esiste già.</span><span class="sxs-lookup"><span data-stu-id="50498-117">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="50498-118">Quindi, ogni volta che si modifica il modello di dati, ovvero si aggiungono, rimuovono, o modificano le classi di entità o si modifica la classe DbContext, è possibile eliminare il database. In seguito EF ne crea uno nuovo corrispondente al modello ed esegue il seeding con dati di test.</span><span class="sxs-lookup"><span data-stu-id="50498-118">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="50498-119">Questo metodo che consiste nel mantenere il database sincronizzato con il modello di dati funziona bene fino a quando non si distribuisce l'applicazione nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="50498-119">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="50498-120">Quando l'applicazione è in esecuzione nell'ambiente di produzione in genere archivia dati che devono essere mantenuti ed è necessario evitare di perdere tutto ad ogni modifica, come ad esempio all'aggiunta di una colonna.</span><span class="sxs-lookup"><span data-stu-id="50498-120">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="50498-121">La funzionalità delle migrazioni di EF Core risolve questo problema consentendo a EF Core di aggiornare lo schema del database anziché crearne uno nuovo.</span><span class="sxs-lookup"><span data-stu-id="50498-121">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

<span data-ttu-id="50498-122">Per lavorare con le migrazioni, è possibile usare la **console di gestione pacchetti** (PMC) o l'interfaccia della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="50498-122">To work with migrations, you can use the **Package Manager Console** (PMC) or the CLI.</span></span>  <span data-ttu-id="50498-123">Queste esercitazioni illustrano come usare i comandi dell'interfaccia della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="50498-123">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="50498-124">Per informazioni sulla Console di Gestione pacchetti, vedere [la fine di questa esercitazione](#pmc).</span><span class="sxs-lookup"><span data-stu-id="50498-124">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="50498-125">Modificare la stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="50498-125">Change the connection string</span></span>

<span data-ttu-id="50498-126">Nel file *appsettings.json* cambiare il nome del database nella stringa di connessione digitando ContosoUniversity2 o un altro nome non ancora adottato nel computer in uso.</span><span class="sxs-lookup"><span data-stu-id="50498-126">In the *appsettings.json* file, change the name of the database in the connection string to ContosoUniversity2 or some other name that you haven't used on the computer you're using.</span></span>

[!code-json[](intro/samples/cu/appsettings2.json?range=1-4)]

<span data-ttu-id="50498-127">Questa modifica configura il progetto in modo che la prima migrazione crei un nuovo database.</span><span class="sxs-lookup"><span data-stu-id="50498-127">This change sets up the project so that the first migration will create a new database.</span></span> <span data-ttu-id="50498-128">Questa operazione non è necessaria per iniziare a usare le migrazioni, ma si capirà più avanti perché è utile eseguirla.</span><span class="sxs-lookup"><span data-stu-id="50498-128">This isn't required to get started with migrations, but you'll see later why it's a good idea.</span></span>

> [!NOTE]
> <span data-ttu-id="50498-129">In alternativa alla modifica del nome del database, è possibile eliminare il database.</span><span class="sxs-lookup"><span data-stu-id="50498-129">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="50498-130">Usare **Esplora oggetti di SQL Server** (SSOX) o il comando CLI `database drop`:</span><span class="sxs-lookup"><span data-stu-id="50498-130">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```dotnetcli
> dotnet ef database drop
> ```
>
> <span data-ttu-id="50498-131">Nella sezione seguente viene illustrato come eseguire i comandi della CLI.</span><span class="sxs-lookup"><span data-stu-id="50498-131">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="50498-132">Creare una migrazione iniziale</span><span class="sxs-lookup"><span data-stu-id="50498-132">Create an initial migration</span></span>

<span data-ttu-id="50498-133">Salvare le modifiche e compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="50498-133">Save your changes and build the project.</span></span> <span data-ttu-id="50498-134">Aprire una finestra di comando e passare alla cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="50498-134">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="50498-135">Ecco un modo rapido per eseguire questa operazione:</span><span class="sxs-lookup"><span data-stu-id="50498-135">Here's a quick way to do that:</span></span>

* <span data-ttu-id="50498-136">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Apri cartella in Esplora File** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="50498-136">In **Solution Explorer**, right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![Voce di menu Apri in Esplora file](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="50498-138">Immettere "cmd" nella barra degli indirizzi e premere INVIO.</span><span class="sxs-lookup"><span data-stu-id="50498-138">Enter "cmd" in the address bar and press Enter.</span></span>

  ![Finestra di comando](migrations/_static/open-command-window.png)

<span data-ttu-id="50498-140">Immettere il comando seguente nella finestra Comando:</span><span class="sxs-lookup"><span data-stu-id="50498-140">Enter the following command in the command window:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="50498-141">Nella finestra di comando viene visualizzato un output come il seguente:</span><span class="sxs-lookup"><span data-stu-id="50498-141">You see output like the following in the command window:</span></span>

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> <span data-ttu-id="50498-142">Se viene visualizzato un messaggio di errore che indica che *non sono stati trovati eseguibili corrispondenti al comando "dotnet-ef"*, vedere [il post di questo blog](https://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) per risolvere il problema.</span><span class="sxs-lookup"><span data-stu-id="50498-142">If you see an error message *No executable found matching command "dotnet-ef"*, see [this blog post](https://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) for help troubleshooting.</span></span>

<span data-ttu-id="50498-143">Se viene visualizzato un messaggio di errore "*Impossibile accedere al file... ContosoUniversity. dll perché è in uso da un altro processo.*", trovare l'icona IIS Express nella barra delle applicazioni di Windows e fare clic con il pulsante destro del mouse su di essa, quindi fare clic su **ContosoUniversity > arresta sito**.</span><span class="sxs-lookup"><span data-stu-id="50498-143">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="50498-144">Esaminare i metodi Up e Down</span><span class="sxs-lookup"><span data-stu-id="50498-144">Examine Up and Down methods</span></span>

<span data-ttu-id="50498-145">Quando è stato eseguito il comando `migrations add`, EF ha generato il codice che crea il database da zero.</span><span class="sxs-lookup"><span data-stu-id="50498-145">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="50498-146">Questo codice si trova nella cartella *Migrations*, nel file denominato *\<timestamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="50498-146">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="50498-147">Il metodo `Up` della classe `InitialCreate` crea le tabelle di database che corrispondono ai set di entità del modello di dati, e il metodo `Down` le elimina, come illustrato nell'esempio seguente.</span><span class="sxs-lookup"><span data-stu-id="50498-147">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="50498-148">Le migrazioni chiamano il metodo `Up` per implementare le modifiche al modello di dati per una migrazione.</span><span class="sxs-lookup"><span data-stu-id="50498-148">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="50498-149">Quando si immette un comando per annullare l'aggiornamento, le migrazioni chiamano il metodo `Down`.</span><span class="sxs-lookup"><span data-stu-id="50498-149">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="50498-150">Questo codice è per la migrazione iniziale creata al momento dell'immissione del comando `migrations add InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="50498-150">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="50498-151">Il parametro del nome della migrazione, nell'esempio "InitialCreate", viene usato per il nome del file e può essere ciò che si vuole.</span><span class="sxs-lookup"><span data-stu-id="50498-151">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="50498-152">È consigliabile scegliere una parola o una frase che riepiloghi cosa viene fatto nella migrazione.</span><span class="sxs-lookup"><span data-stu-id="50498-152">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="50498-153">Ad esempio, è possibile denominare una migrazione successiva "AddDepartmentTable".</span><span class="sxs-lookup"><span data-stu-id="50498-153">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="50498-154">Se la migrazione iniziale è stata creata quando il database esisteva già, il codice di creazione del database viene generato ma non è necessario eseguirlo perché il database corrisponde già al modello di dati.</span><span class="sxs-lookup"><span data-stu-id="50498-154">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="50498-155">Quando si distribuisce l'app in un altro ambiente in cui il database non esiste ancora, questo codice verrà eseguito per creare il database, è consigliabile quindi testarlo prima.</span><span class="sxs-lookup"><span data-stu-id="50498-155">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="50498-156">Ecco perché in precedenza è stato modificato il nome del database nella stringa di connessione: per far sì che le migrazioni possano crearne uno nuovo da zero.</span><span class="sxs-lookup"><span data-stu-id="50498-156">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="50498-157">Snapshot del modello di dati</span><span class="sxs-lookup"><span data-stu-id="50498-157">The data model snapshot</span></span>

<span data-ttu-id="50498-158">Le migrazioni creano uno *snapshot* dello schema del database corrente in *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="50498-158">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="50498-159">Quando si aggiunge una migrazione, EF determina le modifiche apportate confrontando il modello di dati con il file dello snapshot.</span><span class="sxs-lookup"><span data-stu-id="50498-159">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="50498-160">Usare il comando [DotNet EF Migrations Remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) per rimuovere una migrazione.</span><span class="sxs-lookup"><span data-stu-id="50498-160">Use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command to remove a migration.</span></span> <span data-ttu-id="50498-161">`dotnet ef migrations remove` elimina la migrazione e garantisce che lo snapshot venga reimpostato correttamente.</span><span class="sxs-lookup"><span data-stu-id="50498-161">`dotnet ef migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="50498-162">Se `dotnet ef migrations remove` ha esito `dotnet ef migrations remove -v` negativo, usare per ottenere altre informazioni sull'errore.</span><span class="sxs-lookup"><span data-stu-id="50498-162">If `dotnet ef migrations remove` fails, use `dotnet ef migrations remove -v` to get more information on the failure.</span></span>

<span data-ttu-id="50498-163">Per altre informazioni sull'uso del file di snapshot, vedere [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) (Migrazioni EF Core in ambienti team).</span><span class="sxs-lookup"><span data-stu-id="50498-163">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="50498-164">Applicare la migrazione</span><span class="sxs-lookup"><span data-stu-id="50498-164">Apply the migration</span></span>

<span data-ttu-id="50498-165">Nella finestra di comando immettere il comando seguente per creare il database e le tabelle al suo interno.</span><span class="sxs-lookup"><span data-stu-id="50498-165">In the command window, enter the following command to create the database and tables in it.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="50498-166">L'output del comando è simile al comando `migrations add`, a eccezione del fatto che vengono visualizzati i log per i comandi SQL che configurano il database.</span><span class="sxs-lookup"><span data-stu-id="50498-166">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="50498-167">La maggior parte dei log viene omessa nell'output di esempio seguente.</span><span class="sxs-lookup"><span data-stu-id="50498-167">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="50498-168">Se si vuole ridurre il livello di dettaglio nei messaggi di log, è possibile modificare i livelli di log nel file *appsettings.Development.json*.</span><span class="sxs-lookup"><span data-stu-id="50498-168">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="50498-169">Per altre informazioni, vedere <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="50498-169">For more information, see <xref:fundamentals/logging/index>.</span></span>

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (274ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (60ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      IF SERVERPROPERTY('EngineEdition') <> 5
      BEGIN
          ALTER DATABASE [ContosoUniversity2] SET READ_COMMITTED_SNAPSHOT ON;
      END;
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (15ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20190327172701_InitialCreate', N'2.2.0-rtm-35687');
Done.
```

<span data-ttu-id="50498-170">Per controllare il database come è stato fatto nella prima esercitazione, usare **Esplora oggetti di SQL Server**.</span><span class="sxs-lookup"><span data-stu-id="50498-170">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="50498-171">Si noterà l'aggiunta di una tabella \_\_EFMigrationsHistory che tiene traccia di quali migrazioni sono state applicate al database.</span><span class="sxs-lookup"><span data-stu-id="50498-171">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="50498-172">Visualizzare i dati nella tabella: si noterà la presenza di una riga per la prima migrazione.</span><span class="sxs-lookup"><span data-stu-id="50498-172">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="50498-173">Nell'ultimo log nell'esempio di output della CLI precedente viene visualizzata l'istruzione INSERT che crea tale riga.</span><span class="sxs-lookup"><span data-stu-id="50498-173">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="50498-174">Eseguire l'applicazione per verificare che tutto funzioni come prima.</span><span class="sxs-lookup"><span data-stu-id="50498-174">Run the application to verify that everything still works the same as before.</span></span>

![Pagina Student Index (Indice degli studenti)](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="50498-176">Interfaccia della riga di comando e console di gestione pacchetto a confronto</span><span class="sxs-lookup"><span data-stu-id="50498-176">Compare CLI and PMC</span></span>

<span data-ttu-id="50498-177">Gli strumenti di EF per la gestione delle migrazioni sono disponibili dai comandi della CLI di .NET Core o dai cmdlet di PowerShell in Visual Studio nella finestra **Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="50498-177">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="50498-178">In questa esercitazione viene illustrato come usare la CLI, ma se si preferisce è possibile usare la console di Gestione pacchetti.</span><span class="sxs-lookup"><span data-stu-id="50498-178">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="50498-179">I comandi di EF per la console di Gestione pacchetti sono inclusi nel pacchetto [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools).</span><span class="sxs-lookup"><span data-stu-id="50498-179">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="50498-180">Questo pacchetto è incluso nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), quindi non è necessario aggiungere un riferimento al pacchetto se l'app dispone di un riferimento al pacchetto per `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="50498-180">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="50498-181">**Importante:** non si tratta dello stesso pacchetto che si installa per l'interfaccia della riga di comando modificando il file con estensione *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="50498-181">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="50498-182">Il nome di questo pacchetto termina con `Tools`, a differenza del nome del pacchetto della CLI che termina con `Tools.DotNet`.</span><span class="sxs-lookup"><span data-stu-id="50498-182">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="50498-183">Per altre informazioni sui comandi della CLI, vedere [Strumenti da riga di comando di EF Core .NET](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="50498-183">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="50498-184">Per altre informazioni sui comandi della console di Gestione pacchetti, vedere [Console di Gestione pacchetti (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="50498-184">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="50498-185">Ottenere il codice</span><span class="sxs-lookup"><span data-stu-id="50498-185">Get the code</span></span>

[<span data-ttu-id="50498-186">Scaricare o visualizzare l'applicazione completata.</span><span class="sxs-lookup"><span data-stu-id="50498-186">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-step"></a><span data-ttu-id="50498-187">Passaggio successivo</span><span class="sxs-lookup"><span data-stu-id="50498-187">Next step</span></span>

<span data-ttu-id="50498-188">In questa esercitazione:</span><span class="sxs-lookup"><span data-stu-id="50498-188">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="50498-189">Sono state descritte le migrazioni</span><span class="sxs-lookup"><span data-stu-id="50498-189">Learned about migrations</span></span>
> * <span data-ttu-id="50498-190">Sono stati presentati i pacchetti di migrazione NuGet</span><span class="sxs-lookup"><span data-stu-id="50498-190">Learned about NuGet migration packages</span></span>
> * <span data-ttu-id="50498-191">Modifica della stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="50498-191">Changed the connection string</span></span>
> * <span data-ttu-id="50498-192">È stata creata una migrazione iniziale</span><span class="sxs-lookup"><span data-stu-id="50498-192">Created an initial migration</span></span>
> * <span data-ttu-id="50498-193">Sono stati esaminati i metodi Up e Down</span><span class="sxs-lookup"><span data-stu-id="50498-193">Examined Up and Down methods</span></span>
> * <span data-ttu-id="50498-194">È stato esaminato lo snapshot del modello di dati</span><span class="sxs-lookup"><span data-stu-id="50498-194">Learned about the data model snapshot</span></span>
> * <span data-ttu-id="50498-195">È stata applicata la migrazione</span><span class="sxs-lookup"><span data-stu-id="50498-195">Applied the migration</span></span>

<span data-ttu-id="50498-196">Passare all'esercitazione successiva per iniziare a esaminare argomenti più avanzati sull'espansione del modello di dati.</span><span class="sxs-lookup"><span data-stu-id="50498-196">Advance to the next tutorial to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="50498-197">Lungo il percorso verranno create e applicate altre migrazioni.</span><span class="sxs-lookup"><span data-stu-id="50498-197">Along the way you'll create and apply additional migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="50498-198">Creare e applicare altre migrazioni</span><span class="sxs-lookup"><span data-stu-id="50498-198">Create and apply additional migrations</span></span>](complex-data-model.md)
