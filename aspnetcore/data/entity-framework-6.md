---
title: ASP.NET Core e Entity Framework 6
author: rick-anderson
description: Entity Framework 6,3 e versioni successive funzionano con ASP.NET Core 3,1 e versioni successive.
ms.author: riande
ms.custom: mvc
ms.date: 7/14/2020
no-loc:
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/entity-framework-6
ms.openlocfilehash: c65a1ae7cc67ec7e15a2b732184bc05803eb40ce
ms.sourcegitcommit: ec41ab354952b75557240923756a8c2ac79b49f8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88202767"
---
# <a name="aspnet-core-and-entity-framework-6"></a><span data-ttu-id="7abbe-103">ASP.NET Core e Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="7abbe-103">ASP.NET Core and Entity Framework 6</span></span>
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7abbe-104">[Entity Framework](/ef/ef6/) 6,3 e versioni successive funzionano con ASP.NET Core 3,1 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="7abbe-104">[Entity Framework](/ef/ef6/) 6.3 and later works with ASP.NET Core 3.1 and later.</span></span> <span data-ttu-id="7abbe-105">Per altre informazioni, vedere il post di Blog che [annuncia Entity Framework 6,3 con supporto per .NET Core](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-6-3-preview-with-net-core-support/).</span><span class="sxs-lookup"><span data-stu-id="7abbe-105">For more information, see the blog post [Announcing Entity Framework 6.3 with .NET Core Support](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-6-3-preview-with-net-core-support/).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7abbe-106">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="7abbe-106">Additional resources</span></span>

* [<span data-ttu-id="7abbe-107">Entity Framework - configurazione basata su codice</span><span class="sxs-lookup"><span data-stu-id="7abbe-107">Entity Framework - Code-Based Configuration</span></span>](/ef/ef6/fundamentals/configuring/code-based)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7abbe-108">Di [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="7abbe-108">By [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="7abbe-109">In questo articolo viene illustrato come usare Entity Framework 6 in un'applicazione ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7abbe-109">This article shows how to use Entity Framework 6 in an ASP.NET Core application.</span></span>    

## <a name="overview"></a><span data-ttu-id="7abbe-110">Panoramica</span><span class="sxs-lookup"><span data-stu-id="7abbe-110">Overview</span></span> 

<span data-ttu-id="7abbe-111">Per l'uso di Entity Framework 6, il progetto deve eseguire la compilazione con .NET Framework, poiché Entity Framework 6 non supporta .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7abbe-111">To use Entity Framework 6, your project has to compile against .NET Framework, as Entity Framework 6 doesn't support .NET Core.</span></span> <span data-ttu-id="7abbe-112">Se sono richieste funzionalità multipiattaforma, è necessario eseguire l'aggiornamento a [Entity Framework Core](/ef/).</span><span class="sxs-lookup"><span data-stu-id="7abbe-112">If you need cross-platform features you will need to upgrade to [Entity Framework Core](/ef/).</span></span>  

<span data-ttu-id="7abbe-113">Il modo consigliato per usare Entity Framework 6 in un'applicazione ASP.NET Core consiste nell'inserire il contesto EF6 e le classi del modello in un progetto di libreria di classi destinato .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="7abbe-113">The recommended way to use Entity Framework 6 in an ASP.NET Core application is to put the EF6 context and model classes in a class library project that targets .NET Framework.</span></span> <span data-ttu-id="7abbe-114">Aggiungere un riferimento alla libreria di classi dal progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7abbe-114">Add a reference to the class library from the ASP.NET Core project.</span></span> <span data-ttu-id="7abbe-115">Vedere l'esempio [soluzione di Visual Studio con progetti EF6 e ASP.NET Core](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span><span class="sxs-lookup"><span data-stu-id="7abbe-115">See the sample [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span></span>  

<span data-ttu-id="7abbe-116">Non è possibile inserire un contesto EF6 in un progetto ASP.NET poiché i progetti .NET Core non supportano tutte le funzionalità che i comandi EF6, come *Enable-Migrations*, richiedono.</span><span class="sxs-lookup"><span data-stu-id="7abbe-116">You can't put an EF6 context in an ASP.NET Core project because .NET Core projects don't support all of the functionality that EF6 commands such as *Enable-Migrations* require.</span></span>    

<span data-ttu-id="7abbe-117">Indipendentemente dal tipo di progetto in cui si trova il contesto EF6, solo gli strumenti da riga di comando EF6 funzionano con un contesto EF6.</span><span class="sxs-lookup"><span data-stu-id="7abbe-117">Regardless of project type in which you locate your EF6 context, only EF6 command-line tools work with an EF6 context.</span></span> <span data-ttu-id="7abbe-118">Ad esempio, `Scaffold-DbContext` è disponibile solo in Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="7abbe-118">For example, `Scaffold-DbContext` is only available in Entity Framework Core.</span></span> <span data-ttu-id="7abbe-119">Qualora sia necessario il reverse engineering di un database in un modello EF6, vedere [Code First per un Database esistente](https://msdn.microsoft.com/jj200620).</span><span class="sxs-lookup"><span data-stu-id="7abbe-119">If you need to do reverse engineering of a database into an EF6 model, see [Code First to an Existing Database](https://msdn.microsoft.com/jj200620).</span></span>  

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a><span data-ttu-id="7abbe-120">Framework completo di riferimento e EF6 nel progetto ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7abbe-120">Reference full framework and EF6 in the ASP.NET Core project</span></span> 

<span data-ttu-id="7abbe-121">Il progetto ASP.NET Core deve avere come destinazione .NET Framework e fare riferimento a EF6.</span><span class="sxs-lookup"><span data-stu-id="7abbe-121">Your ASP.NET Core project needs to target .NET Framework and reference EF6.</span></span> <span data-ttu-id="7abbe-122">Ad esempio, il file *csproj* del progetto ASP.NET Core avrà un aspetto simile a quanto segue (sono visualizzate solo le parti pertinenti del file).</span><span class="sxs-lookup"><span data-stu-id="7abbe-122">For example, the *.csproj* file of your ASP.NET Core project will look similar to the following example (only relevant parts of the file are shown).</span></span>    

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]   

<span data-ttu-id="7abbe-123">Quando si crea un nuovo progetto, usare il modello **Applicazione Web di ASP.NET Core (.NET Framework)**.</span><span class="sxs-lookup"><span data-stu-id="7abbe-123">When creating a new project, use the **ASP.NET Core Web Application (.NET Framework)** template.</span></span>    

## <a name="handle-connection-strings"></a><span data-ttu-id="7abbe-124">Gestire le stringhe di connessione</span><span class="sxs-lookup"><span data-stu-id="7abbe-124">Handle connection strings</span></span>    

<span data-ttu-id="7abbe-125">Gli strumenti da riga di comando EF6 che vengono usati nel progetto della libreria di classi EF6 richiedono un costruttore predefinito, così da poter creare un'istanza del contesto.</span><span class="sxs-lookup"><span data-stu-id="7abbe-125">The EF6 command-line tools that you'll use in the EF6 class library project require a default constructor so they can instantiate the context.</span></span> <span data-ttu-id="7abbe-126">Tuttavia è opportuno specificare la stringa di connessione da usare nel progetto ASP.NET Core. In questo caso il costruttore di contesto deve avere un parametro che consente di passare alla stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="7abbe-126">But you'll probably want to specify the connection string to use in the ASP.NET Core project, in which case your context constructor must have a parameter that lets you pass in the connection string.</span></span> <span data-ttu-id="7abbe-127">Ecco un esempio.</span><span class="sxs-lookup"><span data-stu-id="7abbe-127">Here's an example.</span></span>   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]   

<span data-ttu-id="7abbe-128">Poiché per il contesto EF6 non è presente un costruttore senza parametri, il progetto EF6 deve specificare un'implementazione di [IDbContextFactory](https://msdn.microsoft.com/library/hh506876).</span><span class="sxs-lookup"><span data-stu-id="7abbe-128">Since your EF6 context doesn't have a parameterless constructor, your EF6 project has to provide an implementation of [IDbContextFactory](https://msdn.microsoft.com/library/hh506876).</span></span> <span data-ttu-id="7abbe-129">Gli strumenti da riga di comando EF6 trovano e usano detta implementazione, così da poter creare un'istanza del contesto.</span><span class="sxs-lookup"><span data-stu-id="7abbe-129">The EF6 command-line tools will find and use that implementation so they can instantiate the context.</span></span> <span data-ttu-id="7abbe-130">Ecco un esempio.</span><span class="sxs-lookup"><span data-stu-id="7abbe-130">Here's an example.</span></span>    

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]  

<span data-ttu-id="7abbe-131">In questo codice di esempio, l'implementazione `IDbContextFactory` passa in una stringa di connessione hardcoded.</span><span class="sxs-lookup"><span data-stu-id="7abbe-131">In this sample code, the `IDbContextFactory` implementation passes in a hard-coded connection string.</span></span> <span data-ttu-id="7abbe-132">Questa è la stringa di connessione che gli strumenti da riga di comando useranno.</span><span class="sxs-lookup"><span data-stu-id="7abbe-132">This is the connection string that the command-line tools will use.</span></span> <span data-ttu-id="7abbe-133">È opportuno implementare una strategia per garantire che la libreria di classi usi la stessa stringa di connessione usata dall'applicazione chiamante.</span><span class="sxs-lookup"><span data-stu-id="7abbe-133">You'll want to implement a strategy to ensure that the class library uses the same connection string that the calling application uses.</span></span> <span data-ttu-id="7abbe-134">Ad esempio, è possibile ottenere il valore dalla variabile di un ambiente in entrambi i progetti.</span><span class="sxs-lookup"><span data-stu-id="7abbe-134">For example, you could get the value from an environment variable in both projects.</span></span>   

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a><span data-ttu-id="7abbe-135">Impostare l'inserimento di dipendenze nel progetto ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7abbe-135">Set up dependency injection in the ASP.NET Core project</span></span>  

<span data-ttu-id="7abbe-136">Nel file *Startup.cs* del progetto Core, impostare il contesto di EF6 per l'inserimento di dipendenze in `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7abbe-136">In the Core project's *Startup.cs* file, set up the EF6 context for dependency injection (DI) in `ConfigureServices`.</span></span> <span data-ttu-id="7abbe-137">Gli oggetti di contesto di Entity Framework devono essere limitati a una durata per richiesta.</span><span class="sxs-lookup"><span data-stu-id="7abbe-137">EF context objects should be scoped for a per-request lifetime.</span></span>   

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]   

<span data-ttu-id="7abbe-138">È quindi possibile ottenere un'istanza del contesto nei controller usando l'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="7abbe-138">You can then get an instance of the context in your controllers by using DI.</span></span> <span data-ttu-id="7abbe-139">Il codice è simile a quanto si scriverebbe per un contesto EF Core:</span><span class="sxs-lookup"><span data-stu-id="7abbe-139">The code is similar to what you'd write for an EF Core context:</span></span>    

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]  

## <a name="sample-application"></a><span data-ttu-id="7abbe-140">Applicazione di esempio</span><span class="sxs-lookup"><span data-stu-id="7abbe-140">Sample application</span></span>   

<span data-ttu-id="7abbe-141">Per un'applicazione di esempio funzionante, vedere la [soluzione di Visual Studio di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) che accompagna questo articolo.</span><span class="sxs-lookup"><span data-stu-id="7abbe-141">For a working sample application, see the [sample Visual Studio solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) that accompanies this article.</span></span>  

<span data-ttu-id="7abbe-142">Questo esempio può essere creato da zero tramite i passaggi seguenti in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="7abbe-142">This sample can be created from scratch by the following steps in Visual Studio:</span></span>    

* <span data-ttu-id="7abbe-143">Creare una soluzione.</span><span class="sxs-lookup"><span data-stu-id="7abbe-143">Create a solution.</span></span>    

* <span data-ttu-id="7abbe-144">**Aggiungi** > **Nuovo progetto** > **Web** > **Applicazione Web ASP.NET Core**</span><span class="sxs-lookup"><span data-stu-id="7abbe-144">**Add** > **New Project** > **Web** > **ASP.NET Core Web Application**</span></span>    
  * <span data-ttu-id="7abbe-145">Nella finestra di dialogo di selezione del modello di progetto selezionare API e .NET Framework nell'elenco a discesa</span><span class="sxs-lookup"><span data-stu-id="7abbe-145">In project template selection dialog, select API and .NET Framework in dropdown</span></span> 

* <span data-ttu-id="7abbe-146">**Aggiungi** > **Nuovo progetto** > **Windows Desktop** > **Libreria di classi (.NET Framework)**</span><span class="sxs-lookup"><span data-stu-id="7abbe-146">**Add** > **New Project** > **Windows Desktop** > **Class Library (.NET Framework)**</span></span>  

* <span data-ttu-id="7abbe-147">Nella **Console di Gestione pacchetti** eseguire il comando per entrambi i progetti`Install-Package Entityframework`.</span><span class="sxs-lookup"><span data-stu-id="7abbe-147">In **Package Manager Console** (PMC) for both projects, run the command `Install-Package Entityframework`.</span></span>    

* <span data-ttu-id="7abbe-148">Nel progetto di libreria di classi, creare le classi di modelli di dati e una classe di contesto e un'implementazione di `IDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="7abbe-148">In the class library project, create data model classes and a context class, and an implementation of `IDbContextFactory`.</span></span>    

* <span data-ttu-id="7abbe-149">Nella Console di Gestione pacchetti del progetto di libreria di classi, eseguire i comandi `Enable-Migrations` e `Add-Migration Initial`.</span><span class="sxs-lookup"><span data-stu-id="7abbe-149">In PMC for the class library project, run the commands `Enable-Migrations` and `Add-Migration Initial`.</span></span> <span data-ttu-id="7abbe-150">Se il progetto ASP.NET Core è stato impostato come progetto di avvio, aggiungere `-StartupProjectName EF6` a questi comandi.</span><span class="sxs-lookup"><span data-stu-id="7abbe-150">If you have set the ASP.NET Core project as the startup project, add `-StartupProjectName EF6` to these commands.</span></span> 

* <span data-ttu-id="7abbe-151">Nel progetto Core aggiungere un riferimento al progetto di libreria di classi.</span><span class="sxs-lookup"><span data-stu-id="7abbe-151">In the Core project, add a project reference to the class library project.</span></span>    

* <span data-ttu-id="7abbe-152">Nel progetto Core, in *Startup.cs*, registrare il contesto per l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="7abbe-152">In the Core project, in *Startup.cs*, register the context for DI.</span></span>    

* <span data-ttu-id="7abbe-153">Nel progetto Corre, in *appsettings.json*, aggiungere la stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="7abbe-153">In the Core project, in *appsettings.json*, add the connection string.</span></span>    

* <span data-ttu-id="7abbe-154">Nel progetto Core, aggiungere un controller e le visualizzazioni per verificare che è possibile leggere e scrivere i dati.</span><span class="sxs-lookup"><span data-stu-id="7abbe-154">In the Core project, add a controller and view(s) to verify that you can read and write data.</span></span> <span data-ttu-id="7abbe-155">Si noti che lo scaffolding di ASP.NET MVC di base non funzionerà con il contesto EF6 a cui viene fatto riferimento dalla libreria di classi.</span><span class="sxs-lookup"><span data-stu-id="7abbe-155">(Note that ASP.NET Core MVC scaffolding won't work with the EF6 context referenced from the class library.)</span></span>

::: moniker-end
