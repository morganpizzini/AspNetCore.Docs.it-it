---
title: Introduzione alle Razor pagine ASP.NET Core
author: Rick-Anderson
description: Spiega in che modo Razor le pagine in ASP.NET Core rendono più semplici e più produttivi gli scenari incentrati sulla pagina del codice rispetto all'uso di MVC
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
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
uid: razor-pages/index
ms.openlocfilehash: bc5f3c85d7ace120dec8369f4550a097b4f258ce
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417643"
---
# <a name="introduction-to-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="254ec-103">Introduzione alle Razor pagine ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="254ec-103">Introduction to Razor Pages in ASP.NET Core</span></span>


<span data-ttu-id="254ec-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="254ec-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="254ec-105">Razor Le pagine possono rendere più semplici e più produttivi gli scenari incentrati sulla pagina di codifica rispetto all'utilizzo di controller e visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="254ec-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="254ec-106">Se si sta cercando un'esercitazione in cui si usa l'approccio Model-View-Controller, vedere [Introduzione ad ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="254ec-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="254ec-107">Questo documento fornisce un'introduzione alle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="254ec-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="254ec-108">Non è un'esercitazione dettagliata.</span><span class="sxs-lookup"><span data-stu-id="254ec-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="254ec-109">Se alcune delle sezioni sono troppo avanzate, vedere [Introduzione alle Razor pagine](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="254ec-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="254ec-110">Per una panoramica di ASP.NET Core, vedere [Introduzione a ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="254ec-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="254ec-111">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="254ec-111">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="254ec-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="254ec-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="254ec-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="254ec-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="254ec-114">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="254ec-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="254ec-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="254ec-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="254ec-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="254ec-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="254ec-117">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="254ec-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="254ec-118">Creazione di un Razor progetto di pagine</span><span class="sxs-lookup"><span data-stu-id="254ec-118">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="254ec-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="254ec-119">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="254ec-120">Per istruzioni dettagliate su come creare un progetto di pagine, vedere [Introduzione alle Razor pagine](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="254ec-120">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="254ec-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="254ec-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="254ec-122">Eseguire `dotnet new webapp` dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="254ec-122">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="254ec-123">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="254ec-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="254ec-124">Per istruzioni dettagliate su come creare un progetto di pagine, vedere [Introduzione alle Razor pagine](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="254ec-124">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="254ec-125">Razor Pagine</span><span class="sxs-lookup"><span data-stu-id="254ec-125">Razor Pages</span></span>

<span data-ttu-id="254ec-126">Razor Le pagine sono abilitate in *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="254ec-126">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="254ec-127">Si consideri una pagina di base: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="254ec-127">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="254ec-128">Il codice precedente ha un aspetto molto simile a quello di un [ Razor file di visualizzazione](xref:tutorials/first-mvc-app/adding-view) usato in un'app ASP.NET Core con i controller e le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="254ec-128">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="254ec-129">Ciò che lo rende diverso è la [`@page`](xref:mvc/views/razor#page) direttiva.</span><span class="sxs-lookup"><span data-stu-id="254ec-129">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="254ec-130">`@page` trasforma il file in un'azione MVC, ovvero gestisce le richieste direttamente, senza passare attraverso un controller.</span><span class="sxs-lookup"><span data-stu-id="254ec-130">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="254ec-131">`@page` deve essere la prima Razor direttiva in una pagina.</span><span class="sxs-lookup"><span data-stu-id="254ec-131">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="254ec-132">`@page` influiscono sul comportamento di altri [Razor](xref:mvc/views/razor) costrutti.</span><span class="sxs-lookup"><span data-stu-id="254ec-132">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="254ec-133">Razor I nomi file delle pagine hanno un suffisso *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="254ec-133">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="254ec-134">Nei due file seguenti viene visualizzata una pagina simile che usa una classe `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="254ec-134">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="254ec-135">Il file *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="254ec-135">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="254ec-136">Il modello di pagina *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="254ec-136">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="254ec-137">Per convenzione, il `PageModel` file di classe ha lo stesso nome del Razor file di paging con l'aggiunta di *. cs* .</span><span class="sxs-lookup"><span data-stu-id="254ec-137">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="254ec-138">La pagina precedente, ad esempio, Razor è *pages/index2. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="254ec-138">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="254ec-139">Il file che contiene la classe `PageModel` è denominato *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="254ec-139">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="254ec-140">Le associazioni dei percorsi URL alle pagine sono determinate dalla posizione della pagina nel file system.</span><span class="sxs-lookup"><span data-stu-id="254ec-140">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="254ec-141">La tabella seguente mostra un Razor percorso di pagina e l'URL corrispondente:</span><span class="sxs-lookup"><span data-stu-id="254ec-141">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="254ec-142">Percorso e nome file</span><span class="sxs-lookup"><span data-stu-id="254ec-142">File name and path</span></span>               | <span data-ttu-id="254ec-143">URL corrispondente</span><span class="sxs-lookup"><span data-stu-id="254ec-143">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="254ec-144">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="254ec-144">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="254ec-145">`/` o `/Index`</span><span class="sxs-lookup"><span data-stu-id="254ec-145">`/` or `/Index`</span></span> |
| <span data-ttu-id="254ec-146">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="254ec-146">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="254ec-147">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="254ec-147">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="254ec-148">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="254ec-148">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="254ec-149">`/Store` o `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="254ec-149">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="254ec-150">Note:</span><span class="sxs-lookup"><span data-stu-id="254ec-150">Notes:</span></span>

* <span data-ttu-id="254ec-151">Per Razor impostazione predefinita, il runtime cerca i file di pagine nella cartella *pages* .</span><span class="sxs-lookup"><span data-stu-id="254ec-151">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="254ec-152">`Index` è la pagina predefinita quando un URL non include una pagina.</span><span class="sxs-lookup"><span data-stu-id="254ec-152">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="254ec-153">Scrivere un form di base</span><span class="sxs-lookup"><span data-stu-id="254ec-153">Write a basic form</span></span>

<span data-ttu-id="254ec-154">Razor Pagine è progettato per semplificare l'implementazione dei modelli comuni usati con i Web browser quando si compila un'app.</span><span class="sxs-lookup"><span data-stu-id="254ec-154">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="254ec-155">L' [associazione di modelli](xref:mvc/models/model-binding), gli [Helper Tag](xref:mvc/views/tag-helpers/intro)e gli helper HTML *funzionano solo* con le proprietà definite in una Razor classe di pagine.</span><span class="sxs-lookup"><span data-stu-id="254ec-155">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="254ec-156">Si consideri una pagina che implementa un form "contact us" di base per il modello `Contact`:</span><span class="sxs-lookup"><span data-stu-id="254ec-156">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="254ec-157">Per gli esempi riportati in questo documento, `DbContext` viene inizializzato nel file [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24).</span><span class="sxs-lookup"><span data-stu-id="254ec-157">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

<span data-ttu-id="254ec-158">Il database in memoria richiede il `Microsoft.EntityFrameworkCore.InMemory` pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="254ec-158">The in memory database requires the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="254ec-159">Il modello di dati:</span><span class="sxs-lookup"><span data-stu-id="254ec-159">The data model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="254ec-160">Il contesto del database:</span><span class="sxs-lookup"><span data-stu-id="254ec-160">The db context:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="254ec-161">Il file di visualizzazione *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="254ec-161">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="254ec-162">Il modello di pagina *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="254ec-162">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="254ec-163">Per convenzione, la classe `PageModel` è denominata `<PageName>Model` e si trova nello stesso spazio dei nomi della pagina.</span><span class="sxs-lookup"><span data-stu-id="254ec-163">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="254ec-164">La classe `PageModel` consente la separazione della logica di una pagina dalla relativa presentazione.</span><span class="sxs-lookup"><span data-stu-id="254ec-164">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="254ec-165">Definisce i gestori di pagina per le richieste inviate alla pagina e i dati usati per il rendering della pagina.</span><span class="sxs-lookup"><span data-stu-id="254ec-165">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="254ec-166">Questa separazione consente:</span><span class="sxs-lookup"><span data-stu-id="254ec-166">This separation allows:</span></span>

* <span data-ttu-id="254ec-167">Gestione delle dipendenze di pagina tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="254ec-167">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="254ec-168">Testing unità</span><span class="sxs-lookup"><span data-stu-id="254ec-168">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="254ec-169">La pagina contiene un oggetto , ovvero un `OnPostAsync` *metodo gestore* che viene eseguito per le richieste `POST`, quando un utente invia il form.</span><span class="sxs-lookup"><span data-stu-id="254ec-169">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="254ec-170">È possibile aggiungere i metodi del gestore per qualsiasi verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="254ec-170">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="254ec-171">I gestori più comuni sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="254ec-171">The most common handlers are:</span></span>

* <span data-ttu-id="254ec-172">`OnGet` per inizializzare lo stato necessario per la pagina.</span><span class="sxs-lookup"><span data-stu-id="254ec-172">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="254ec-173">Nel codice precedente, il `OnGet` Metodo Visualizza la pagina *CreateModel. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="254ec-173">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="254ec-174">`OnPost` per gestire gli invii di form.</span><span class="sxs-lookup"><span data-stu-id="254ec-174">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="254ec-175">Il suffisso `Async` nel nome è facoltativo, ma viene spesso usato per convenzione per le funzioni asincrone.</span><span class="sxs-lookup"><span data-stu-id="254ec-175">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="254ec-176">Il codice precedente è tipico per le Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="254ec-176">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="254ec-177">Se si ha familiarità con le app ASP.NET che usano i controller e le visualizzazioni:</span><span class="sxs-lookup"><span data-stu-id="254ec-177">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="254ec-178">Il `OnPostAsync` codice nell'esempio precedente ha un aspetto simile al codice del controller tipico.</span><span class="sxs-lookup"><span data-stu-id="254ec-178">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="254ec-179">La maggior parte delle primitive MVC come l' [associazione di modelli](xref:mvc/models/model-binding), la [convalida](xref:mvc/models/validation)e i risultati dell'azione funzionano allo stesso modo con i controller e le Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="254ec-179">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="254ec-180">Il metodo `OnPostAsync` precedente:</span><span class="sxs-lookup"><span data-stu-id="254ec-180">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="254ec-181">Il flusso di base di `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="254ec-181">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="254ec-182">Verificare se sono presenti errori di convalida.</span><span class="sxs-lookup"><span data-stu-id="254ec-182">Check for validation errors.</span></span>

* <span data-ttu-id="254ec-183">Se non sono presenti errori, salvare i dati e reindirizzare.</span><span class="sxs-lookup"><span data-stu-id="254ec-183">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="254ec-184">Se sono presenti errori, visualizzare di nuovo la pagina con i messaggi di convalida.</span><span class="sxs-lookup"><span data-stu-id="254ec-184">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="254ec-185">In molti casi, gli errori di convalida vengono rilevati nel client e non vengono mai inviati al server.</span><span class="sxs-lookup"><span data-stu-id="254ec-185">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="254ec-186">Il file di visualizzazione *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="254ec-186">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="254ec-187">Il codice HTML sottoposto a rendering da *pages/create. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="254ec-187">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="254ec-188">Nel codice precedente, inserendo il modulo:</span><span class="sxs-lookup"><span data-stu-id="254ec-188">In the previous code, posting the form:</span></span>

* <span data-ttu-id="254ec-189">Con dati validi:</span><span class="sxs-lookup"><span data-stu-id="254ec-189">With valid data:</span></span>

  * <span data-ttu-id="254ec-190">Il `OnPostAsync` metodo del gestore chiama il <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> metodo helper.</span><span class="sxs-lookup"><span data-stu-id="254ec-190">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="254ec-191">`RedirectToPage` restituisce un'istanza di <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span><span class="sxs-lookup"><span data-stu-id="254ec-191">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="254ec-192">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="254ec-192">`RedirectToPage`:</span></span>

    * <span data-ttu-id="254ec-193">Risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="254ec-193">Is an action result.</span></span>
    * <span data-ttu-id="254ec-194">È simile a `RedirectToAction` o `RedirectToRoute` (usato nei controller e nelle viste).</span><span class="sxs-lookup"><span data-stu-id="254ec-194">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="254ec-195">È personalizzato per le pagine.</span><span class="sxs-lookup"><span data-stu-id="254ec-195">Is customized for pages.</span></span> <span data-ttu-id="254ec-196">Nell'esempio precedente viene reindirizzato alla pagina di indice radice (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="254ec-196">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="254ec-197">`RedirectToPage` è descritto in dettaglio nella sezione [generazione URL per pagine](#url_gen) .</span><span class="sxs-lookup"><span data-stu-id="254ec-197">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="254ec-198">Con gli errori di convalida passati al server:</span><span class="sxs-lookup"><span data-stu-id="254ec-198">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="254ec-199">Il `OnPostAsync` metodo del gestore chiama il <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> metodo helper.</span><span class="sxs-lookup"><span data-stu-id="254ec-199">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="254ec-200">`Page` restituisce un'istanza di <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span><span class="sxs-lookup"><span data-stu-id="254ec-200">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="254ec-201">La restituzione di `Page` è simile al modo in cui le azioni nel controller restituiscono `View`.</span><span class="sxs-lookup"><span data-stu-id="254ec-201">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="254ec-202">`PageResult` è il tipo restituito predefinito per un metodo del gestore.</span><span class="sxs-lookup"><span data-stu-id="254ec-202">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="254ec-203">Un metodo gestore che restituisce `void` esegue il rendering della pagina.</span><span class="sxs-lookup"><span data-stu-id="254ec-203">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="254ec-204">Nell'esempio precedente, se si pubblica il form senza alcun valore, in [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) viene restituito false.</span><span class="sxs-lookup"><span data-stu-id="254ec-204">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="254ec-205">In questo esempio non vengono visualizzati errori di convalida nel client.</span><span class="sxs-lookup"><span data-stu-id="254ec-205">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="254ec-206">Il controllo degli errori di convalida viene trattato più avanti in questo documento.</span><span class="sxs-lookup"><span data-stu-id="254ec-206">Validation error handing is covered later in this document.</span></span>

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="254ec-207">Con errori di convalida rilevati dalla convalida lato client:</span><span class="sxs-lookup"><span data-stu-id="254ec-207">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="254ec-208">I dati **non** vengono inviati al server.</span><span class="sxs-lookup"><span data-stu-id="254ec-208">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="254ec-209">La convalida lato client è illustrata più avanti in questo documento.</span><span class="sxs-lookup"><span data-stu-id="254ec-209">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="254ec-210">La `Customer` Proprietà usa l' [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attributo per acconsentire esplicitamente all'associazione di modelli:</span><span class="sxs-lookup"><span data-stu-id="254ec-210">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="254ec-211">`[BindProperty]`**non** deve essere utilizzato nei modelli contenenti proprietà che non devono essere modificate dal client.</span><span class="sxs-lookup"><span data-stu-id="254ec-211">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="254ec-212">Per ulteriori informazioni, vedere [overposting](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="254ec-212">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="254ec-213">Razor Le pagine, per impostazione predefinita, associano proprietà solo con non `GET` verbi.</span><span class="sxs-lookup"><span data-stu-id="254ec-213">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="254ec-214">L'associazione alle proprietà Elimina la necessità di scrivere codice per convertire i dati HTTP nel tipo di modello.</span><span class="sxs-lookup"><span data-stu-id="254ec-214">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="254ec-215">Riduce il codice usando la stessa proprietà per il eseguire il rendering dei campi del form (`<input asp-for="Customer.Name">`) e accettare l'input.</span><span class="sxs-lookup"><span data-stu-id="254ec-215">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="254ec-216">Esaminando il file di visualizzazione *pages/create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="254ec-216">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="254ec-217">Nel codice precedente, l' [Helper Tag](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` di input associa l' `<input>` elemento HTML all'espressione del `Customer.Name` modello.</span><span class="sxs-lookup"><span data-stu-id="254ec-217">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="254ec-218">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) rende disponibili gli helper tag.</span><span class="sxs-lookup"><span data-stu-id="254ec-218">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="254ec-219">La home page</span><span class="sxs-lookup"><span data-stu-id="254ec-219">The home page</span></span>

<span data-ttu-id="254ec-220">*Index. cshtml* è il Home page:</span><span class="sxs-lookup"><span data-stu-id="254ec-220">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="254ec-221">La classe `PageModel` associata (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="254ec-221">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="254ec-222">Il file *index. cshtml* contiene il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="254ec-222">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="254ec-223">L' `<a /a>` [Helper tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) ha usato l' `asp-route-{value}` attributo per generare un collegamento alla pagina di modifica.</span><span class="sxs-lookup"><span data-stu-id="254ec-223">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="254ec-224">Il collegamento contiene i dati della route con l'ID contatto.</span><span class="sxs-lookup"><span data-stu-id="254ec-224">The link contains route data with the contact ID.</span></span> <span data-ttu-id="254ec-225">Ad esempio: `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="254ec-225">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="254ec-226">Gli [Helper Tag](xref:mvc/views/tag-helpers/intro) consentono al codice lato server di partecipare alla creazione e al rendering di elementi HTML nei Razor file.</span><span class="sxs-lookup"><span data-stu-id="254ec-226">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="254ec-227">Il file *index. cshtml* contiene il markup per la creazione di un pulsante Delete per ogni contatto del cliente:</span><span class="sxs-lookup"><span data-stu-id="254ec-227">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="254ec-228">HTML sottoposto a rendering:</span><span class="sxs-lookup"><span data-stu-id="254ec-228">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="254ec-229">Quando il pulsante Elimina viene sottoposto a rendering in HTML, il relativo [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) include i parametri per:</span><span class="sxs-lookup"><span data-stu-id="254ec-229">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="254ec-230">ID contatto del cliente, specificato dall' `asp-route-id` attributo.</span><span class="sxs-lookup"><span data-stu-id="254ec-230">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="254ec-231">Oggetto `handler` , specificato dall' `asp-page-handler` attributo.</span><span class="sxs-lookup"><span data-stu-id="254ec-231">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="254ec-232">Quando il pulsante è selezionato, viene inviata una richiesta `POST` di modulo al server.</span><span class="sxs-lookup"><span data-stu-id="254ec-232">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="254ec-233">Per convenzione, il nome del metodo del gestore viene selezionato in base al valore del parametro `handler` in base allo schema `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="254ec-233">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="254ec-234">Poiché in questo esempio l'`handler` è `delete`, il metodo `OnPostDeleteAsync` viene usato per elaborare la richiesta `POST`.</span><span class="sxs-lookup"><span data-stu-id="254ec-234">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="254ec-235">Se `asp-page-handler` viene impostato su un valore diverso, ad esempio `remove`, viene selezionato un metodo gestore con il nome `OnPostRemoveAsync`.</span><span class="sxs-lookup"><span data-stu-id="254ec-235">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="254ec-236">Il metodo `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="254ec-236">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="254ec-237">Ottiene `id` dalla stringa di query.</span><span class="sxs-lookup"><span data-stu-id="254ec-237">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="254ec-238">Interroga il database in merito al contatto del cliente con `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="254ec-238">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="254ec-239">Se il contatto del cliente viene trovato, viene rimosso e il database viene aggiornato.</span><span class="sxs-lookup"><span data-stu-id="254ec-239">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="254ec-240">Chiama <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> per reindirizzare alla pagina di indice radice (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="254ec-240">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="254ec-241">File Edit. cshtml</span><span class="sxs-lookup"><span data-stu-id="254ec-241">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="254ec-242">La prima riga contiene la direttiva `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="254ec-242">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="254ec-243">Il vincolo di routing `"{id:int}"` indica alla pagina di accettare le richieste inviate alla pagina che contengono i dati della route `int`.</span><span class="sxs-lookup"><span data-stu-id="254ec-243">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="254ec-244">Se una richiesta inviata alla pagina non contiene dati della route che possono essere convertiti in un oggetto `int`, il runtime restituisce un errore HTTP 404 (non trovato).</span><span class="sxs-lookup"><span data-stu-id="254ec-244">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="254ec-245">Per rendere l'ID facoltativo, aggiungere `?` al vincolo di route:</span><span class="sxs-lookup"><span data-stu-id="254ec-245">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="254ec-246">Il file *Edit.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="254ec-246">The *Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="254ec-247">Convalida</span><span class="sxs-lookup"><span data-stu-id="254ec-247">Validation</span></span>

<span data-ttu-id="254ec-248">Regole di convalida:</span><span class="sxs-lookup"><span data-stu-id="254ec-248">Validation rules:</span></span>

* <span data-ttu-id="254ec-249">Sono specificati in modo dichiarativo nella classe del modello.</span><span class="sxs-lookup"><span data-stu-id="254ec-249">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="254ec-250">Vengono applicati ovunque nell'app.</span><span class="sxs-lookup"><span data-stu-id="254ec-250">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="254ec-251">Lo <xref:System.ComponentModel.DataAnnotations> spazio dei nomi fornisce un set di attributi di convalida predefiniti che vengono applicati in modo dichiarativo a una classe o a una proprietà.</span><span class="sxs-lookup"><span data-stu-id="254ec-251">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="254ec-252">DataAnnotations contiene anche attributi di formattazione come [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) questa guida alla formattazione e non forniscono alcuna convalida.</span><span class="sxs-lookup"><span data-stu-id="254ec-252">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="254ec-253">Si consideri il `Customer` modello:</span><span class="sxs-lookup"><span data-stu-id="254ec-253">Consider the `Customer` model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="254ec-254">Utilizzando il seguente file di visualizzazione *create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="254ec-254">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="254ec-255">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="254ec-255">The preceding code:</span></span>

* <span data-ttu-id="254ec-256">Include gli script di convalida jQuery e jQuery.</span><span class="sxs-lookup"><span data-stu-id="254ec-256">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="254ec-257">USA gli `<div />` `<span />` [Helper Tag](xref:mvc/views/tag-helpers/intro) e per abilitare:</span><span class="sxs-lookup"><span data-stu-id="254ec-257">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="254ec-258">Convalida lato client.</span><span class="sxs-lookup"><span data-stu-id="254ec-258">Client-side validation.</span></span>
  * <span data-ttu-id="254ec-259">Rendering degli errori di convalida.</span><span class="sxs-lookup"><span data-stu-id="254ec-259">Validation error rendering.</span></span>

* <span data-ttu-id="254ec-260">Viene generato il codice HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="254ec-260">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="254ec-261">Se si pubblica il modulo di creazione senza un valore di nome, viene visualizzato il messaggio di errore "il campo nome è obbligatorio".</span><span class="sxs-lookup"><span data-stu-id="254ec-261">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="254ec-262">nel form.</span><span class="sxs-lookup"><span data-stu-id="254ec-262">on the form.</span></span> <span data-ttu-id="254ec-263">Se JavaScript è abilitato nel client, il browser Visualizza l'errore senza inviare al server.</span><span class="sxs-lookup"><span data-stu-id="254ec-263">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="254ec-264">L' `[StringLength(10)]` attributo genera `data-val-length-max="10"` sull'HTML sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="254ec-264">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="254ec-265">`data-val-length-max` impedisce ai browser di immettere più della lunghezza massima specificata.</span><span class="sxs-lookup"><span data-stu-id="254ec-265">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="254ec-266">Se viene usato uno strumento come [Fiddler](https://www.telerik.com/fiddler) per modificare e riprodurre il post:</span><span class="sxs-lookup"><span data-stu-id="254ec-266">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="254ec-267">Con il nome più lungo di 10.</span><span class="sxs-lookup"><span data-stu-id="254ec-267">With the name longer than 10.</span></span>
* <span data-ttu-id="254ec-268">Il messaggio di errore "il nome del campo deve essere una stringa con una lunghezza massima di 10".</span><span class="sxs-lookup"><span data-stu-id="254ec-268">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="254ec-269">un errore imprevisto".</span><span class="sxs-lookup"><span data-stu-id="254ec-269">is returned.</span></span>

<span data-ttu-id="254ec-270">Si consideri il `Movie` modello seguente:</span><span class="sxs-lookup"><span data-stu-id="254ec-270">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="254ec-271">Gli attributi di convalida specificano il comportamento da applicare alle proprietà del modello a cui sono applicati:</span><span class="sxs-lookup"><span data-stu-id="254ec-271">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="254ec-272">Gli `Required` `MinimumLength` attributi e indicano che una proprietà deve avere un valore, ma nulla impedisce a un utente di immettere spazi vuoti per soddisfare la convalida.</span><span class="sxs-lookup"><span data-stu-id="254ec-272">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="254ec-273">L'attributo `RegularExpression` viene usato per limitare i caratteri che possono essere inseriti.</span><span class="sxs-lookup"><span data-stu-id="254ec-273">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="254ec-274">Nel codice precedente "Genre":</span><span class="sxs-lookup"><span data-stu-id="254ec-274">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="254ec-275">Deve includere solo lettere.</span><span class="sxs-lookup"><span data-stu-id="254ec-275">Must only use letters.</span></span>
  * <span data-ttu-id="254ec-276">La prima lettera deve essere maiuscola.</span><span class="sxs-lookup"><span data-stu-id="254ec-276">The first letter is required to be uppercase.</span></span> <span data-ttu-id="254ec-277">Gli spazi, i numeri e i caratteri speciali non sono consentiti.</span><span class="sxs-lookup"><span data-stu-id="254ec-277">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="254ec-278">`RegularExpression` "Rating":</span><span class="sxs-lookup"><span data-stu-id="254ec-278">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="254ec-279">Richiede che il primo carattere sia una lettera maiuscola.</span><span class="sxs-lookup"><span data-stu-id="254ec-279">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="254ec-280">Consente i caratteri speciali e i numeri negli spazi successivi.</span><span class="sxs-lookup"><span data-stu-id="254ec-280">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="254ec-281">"PG-13" è valido per una classificazione, ma non per "Genre".</span><span class="sxs-lookup"><span data-stu-id="254ec-281">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="254ec-282">L'attributo `Range` vincola un valore all'interno di un intervallo specificato.</span><span class="sxs-lookup"><span data-stu-id="254ec-282">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="254ec-283">L' `StringLength` attributo imposta la lunghezza massima di una proprietà di stringa e, facoltativamente, la lunghezza minima.</span><span class="sxs-lookup"><span data-stu-id="254ec-283">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="254ec-284">I tipi di valore, ad esempio `decimal`, `int`, `float` e `DateTime`, sono intrinsecamente necessari e non richiedono l'attributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="254ec-284">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="254ec-285">Nella pagina Crea per il `Movie` modello vengono visualizzati gli errori con valori non validi:</span><span class="sxs-lookup"><span data-stu-id="254ec-285">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Il modulo di vista del film con più errori di convalida del lato client jQuery](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="254ec-287">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="254ec-287">For more information, see:</span></span>

* [<span data-ttu-id="254ec-288">Aggiungere la convalida all'app Movie</span><span class="sxs-lookup"><span data-stu-id="254ec-288">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="254ec-289">[Convalida del modello in ASP.NET Core](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="254ec-289">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="254ec-290">Gestire le richieste HEAD con un fallback del gestore OnGet</span><span class="sxs-lookup"><span data-stu-id="254ec-290">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="254ec-291">`HEAD` le richieste consentono di recuperare le intestazioni per una risorsa specifica.</span><span class="sxs-lookup"><span data-stu-id="254ec-291">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="254ec-292">A differenza delle richieste `GET`, le richieste `HEAD` non restituiscono un corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="254ec-292">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="254ec-293">In genere, per le richieste `HEAD` viene creato e chiamato un gestore `OnHead`:</span><span class="sxs-lookup"><span data-stu-id="254ec-293">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="254ec-294">Razor Le pagine eseguono il fallback alla chiamata del `OnGet` gestore se non `OnHead` è stato definito alcun gestore.</span><span class="sxs-lookup"><span data-stu-id="254ec-294">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="254ec-295">XSRF/CSRF e Razor pagine</span><span class="sxs-lookup"><span data-stu-id="254ec-295">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="254ec-296">Razor Le pagine sono protette dalla [convalida antifalsificazione](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="254ec-296">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="254ec-297">[FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) inserisce i token antifalsificazione negli elementi del form HTML.</span><span class="sxs-lookup"><span data-stu-id="254ec-297">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="254ec-298">Uso di layout, parti parziali, modelli e helper tag con Razor pagine</span><span class="sxs-lookup"><span data-stu-id="254ec-298">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="254ec-299">Le pagine funzionano con tutte le funzionalità del Razor motore di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="254ec-299">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="254ec-300">Layout, parti parziali, modelli, helper tag, *_ViewStart. cshtml* e *_ViewImports. cshtml* funzionano allo stesso modo per le Razor visualizzazioni convenzionali.</span><span class="sxs-lookup"><span data-stu-id="254ec-300">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="254ec-301">La pagina verrà ora riorganizzata in modo da usufruire di alcune di queste funzionalità.</span><span class="sxs-lookup"><span data-stu-id="254ec-301">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="254ec-302">Aggiungere una [pagina di layout](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="254ec-302">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="254ec-303">Il [layout](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="254ec-303">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="254ec-304">Controlla il layout di ogni pagina, a meno che la pagina non accetti il layout.</span><span class="sxs-lookup"><span data-stu-id="254ec-304">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="254ec-305">Importa le strutture HTML, ad esempio JavaScript e i fogli di stile.</span><span class="sxs-lookup"><span data-stu-id="254ec-305">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="254ec-306">Viene eseguito il rendering del contenuto della Razor pagina dove `@RenderBody()` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="254ec-306">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="254ec-307">Per ulteriori informazioni, vedere [pagina layout](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="254ec-307">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="254ec-308">La proprietà [Layout](xref:mvc/views/layout#specifying-a-layout) proprietà viene impostata in *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="254ec-308">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="254ec-309">Il layout è nella cartella *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="254ec-309">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="254ec-310">Le pagine cercano altre visualizzazioni (layout, modelli, righe parzialmente eseguite) in modo gerarchico, partendo dalla stessa cartella della pagina corrente.</span><span class="sxs-lookup"><span data-stu-id="254ec-310">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="254ec-311">Un layout nella cartella pages */Shared* può essere utilizzato da qualsiasi Razor pagina nella cartella *pages* .</span><span class="sxs-lookup"><span data-stu-id="254ec-311">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="254ec-312">Il file di layout dovrebbe andare nella cartella *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="254ec-312">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="254ec-313">Si consiglia di **non** inserire il file di layout nella cartella *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="254ec-313">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="254ec-314">*Views/Shared* è un modello destinato alle visualizzazioni MVC.</span><span class="sxs-lookup"><span data-stu-id="254ec-314">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="254ec-315">Razor Le pagine hanno lo scopo di basarsi sulla gerarchia di cartelle, non sulle convenzioni di percorso.</span><span class="sxs-lookup"><span data-stu-id="254ec-315">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="254ec-316">Visualizza la ricerca da una Razor pagina include la cartella *pagine* .</span><span class="sxs-lookup"><span data-stu-id="254ec-316">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="254ec-317">I layout, i modelli e i parziali utilizzati con i controller MVC e le Razor visualizzazioni convenzionali *funzionano semplicemente*.</span><span class="sxs-lookup"><span data-stu-id="254ec-317">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="254ec-318">Aggiungere un file *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="254ec-318">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="254ec-319">`@namespace` viene spiegato in seguito nell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="254ec-319">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="254ec-320">La direttiva `@addTagHelper` inserisce gli [helper tag predefiniti](xref:mvc/views/tag-helpers/builtin-th/Index) in tutte le pagine presenti nella cartella *Pages*.</span><span class="sxs-lookup"><span data-stu-id="254ec-320">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="254ec-321">La `@namespace` direttiva impostata in una pagina:</span><span class="sxs-lookup"><span data-stu-id="254ec-321">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="254ec-322">La `@namespace` direttiva imposta lo spazio dei nomi per la pagina.</span><span class="sxs-lookup"><span data-stu-id="254ec-322">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="254ec-323">La direttiva `@model` non deve necessariamente includere lo spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="254ec-323">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="254ec-324">Se la direttiva `@namespace` è contenuta in *_ViewImports.cshtml*, lo spazio dei nomi specificato indica il prefisso per lo spazio dei nomi generato nella pagina che importa la direttiva `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="254ec-324">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="254ec-325">Il resto dello spazio dei nomi generato (la parte del suffisso) è il percorso relativo separato dal punto tra la cartella contenente *_Viewimports.cshtml* e la cartella contenente la pagina.</span><span class="sxs-lookup"><span data-stu-id="254ec-325">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="254ec-326">Ad esempio, la classe `PageModel`*Pages/Customers/Edit.cshtml.cs* imposta in modo esplicito lo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="254ec-326">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="254ec-327">Il file *Pages/_ViewImports.cshtml* file imposta il seguente spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="254ec-327">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="254ec-328">Lo spazio dei nomi generato per la pagina *pages/Customers/Edit. cshtml* Razor è uguale a quello della `PageModel` classe.</span><span class="sxs-lookup"><span data-stu-id="254ec-328">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="254ec-329">`@namespace`*funziona anche con le Razor visualizzazioni convenzionali.*</span><span class="sxs-lookup"><span data-stu-id="254ec-329">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="254ec-330">Si consideri il file di visualizzazione *pages/create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="254ec-330">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="254ec-331">Il file di visualizzazione *pages/create. cshtml* aggiornato con *_ViewImports. cshtml* e il file di layout precedente:</span><span class="sxs-lookup"><span data-stu-id="254ec-331">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="254ec-332">Nel codice precedente, il *_ViewImports. cshtml* ha importato lo spazio dei nomi e gli helper tag.</span><span class="sxs-lookup"><span data-stu-id="254ec-332">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="254ec-333">Il file di layout ha importato i file JavaScript.</span><span class="sxs-lookup"><span data-stu-id="254ec-333">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="254ec-334">Il [ Razor progetto di avvio pagine](#rpvs17) contiene le *pagine/_ValidationScriptsPartial. cshtml*, che consente di associare la convalida lato client.</span><span class="sxs-lookup"><span data-stu-id="254ec-334">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="254ec-335">Per altre informazioni sulle visualizzazioni parziali, vedere <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="254ec-335">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="254ec-336">Generazione di URL per le pagine</span><span class="sxs-lookup"><span data-stu-id="254ec-336">URL generation for Pages</span></span>

<span data-ttu-id="254ec-337">La pagina `Create`, riportata in precedenza, usa `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="254ec-337">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="254ec-338">L'applicazione ha la struttura di file o cartella seguente:</span><span class="sxs-lookup"><span data-stu-id="254ec-338">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="254ec-339">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="254ec-339">*/Pages*</span></span>

  * <span data-ttu-id="254ec-340">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="254ec-340">*Index.cshtml*</span></span>
  * <span data-ttu-id="254ec-341">*Privacy. cshtml*</span><span class="sxs-lookup"><span data-stu-id="254ec-341">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="254ec-342">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="254ec-342">*/Customers*</span></span>

    * <span data-ttu-id="254ec-343">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="254ec-343">*Create.cshtml*</span></span>
    * <span data-ttu-id="254ec-344">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="254ec-344">*Edit.cshtml*</span></span>
    * <span data-ttu-id="254ec-345">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="254ec-345">*Index.cshtml*</span></span>

<span data-ttu-id="254ec-346">Le pagine *pages/Customers/create. cshtml* e *pages/Customers/Edit. cshtml* reindirizza a *pages/Customers/index. cshtml* dopo l'esito positivo.</span><span class="sxs-lookup"><span data-stu-id="254ec-346">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="254ec-347">La stringa `./Index` è un nome di pagina relativo usato per accedere alla pagina precedente.</span><span class="sxs-lookup"><span data-stu-id="254ec-347">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="254ec-348">Viene usato per generare gli URL nella pagina *pages/Customers/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="254ec-348">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="254ec-349">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="254ec-349">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="254ec-350">Il nome della pagina assoluta `/Index` viene usato per generare gli URL nella pagina *pages/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="254ec-350">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="254ec-351">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="254ec-351">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="254ec-352">Il nome della pagina è il percorso alla pagina dalla cartella radice */Pages*, inclusa una barra iniziale `/`, ad esempio `/Index`.</span><span class="sxs-lookup"><span data-stu-id="254ec-352">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="254ec-353">Gli esempi di generazione di URL precedenti offrono opzioni avanzate e funzionalità funzionali rispetto a un URL a livello di codice.</span><span class="sxs-lookup"><span data-stu-id="254ec-353">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="254ec-354">La generazione di URL usa il [routing](xref:mvc/controllers/routing) ed è in grado di generare e codificare i parametri in base al modo in cui la route è definita nel percorso di destinazione.</span><span class="sxs-lookup"><span data-stu-id="254ec-354">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="254ec-355">La generazione di URL per le pagine supporta i nomi relativi.</span><span class="sxs-lookup"><span data-stu-id="254ec-355">URL generation for pages supports relative names.</span></span> <span data-ttu-id="254ec-356">Nella tabella seguente viene illustrata la pagina di indice selezionata utilizzando `RedirectToPage` parametri diversi in *pages/Customers/create. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="254ec-356">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="254ec-357">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="254ec-357">RedirectToPage(x)</span></span>| <span data-ttu-id="254ec-358">Pagina</span><span class="sxs-lookup"><span data-stu-id="254ec-358">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="254ec-359">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="254ec-359">RedirectToPage("/Index")</span></span> | <span data-ttu-id="254ec-360">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="254ec-360">*Pages/Index*</span></span> |
| <span data-ttu-id="254ec-361">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="254ec-361">RedirectToPage("./Index");</span></span> | <span data-ttu-id="254ec-362">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="254ec-362">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="254ec-363">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="254ec-363">RedirectToPage("../Index")</span></span> | <span data-ttu-id="254ec-364">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="254ec-364">*Pages/Index*</span></span> |
| <span data-ttu-id="254ec-365">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="254ec-365">RedirectToPage("Index")</span></span>  | <span data-ttu-id="254ec-366">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="254ec-366">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="254ec-367">`RedirectToPage("Index")`, `RedirectToPage("./Index")` e `RedirectToPage("../Index")` sono *nomi relativi*.</span><span class="sxs-lookup"><span data-stu-id="254ec-367">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="254ec-368">Il parametro `RedirectToPage` è *combinato* con il percorso della pagina corrente per calcolare il nome della pagina di destinazione.</span><span class="sxs-lookup"><span data-stu-id="254ec-368">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="254ec-369">Il collegamento dei nomi relativi è utile quando si compilano siti con una struttura complessa.</span><span class="sxs-lookup"><span data-stu-id="254ec-369">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="254ec-370">Quando vengono usati nomi relativi per il collegamento tra le pagine in una cartella:</span><span class="sxs-lookup"><span data-stu-id="254ec-370">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="254ec-371">La ridenominazione di una cartella non interrompe i collegamenti relativi.</span><span class="sxs-lookup"><span data-stu-id="254ec-371">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="254ec-372">I collegamenti non vengono interrotti perché non includono il nome della cartella.</span><span class="sxs-lookup"><span data-stu-id="254ec-372">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="254ec-373">Per reindirizzare la pagina a un'[Area](xref:mvc/controllers/areas) diversa, specificare l'area:</span><span class="sxs-lookup"><span data-stu-id="254ec-373">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="254ec-374">Per altre informazioni, vedere <xref:mvc/controllers/areas> e <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="254ec-374">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="254ec-375">Attributo viewData</span><span class="sxs-lookup"><span data-stu-id="254ec-375">ViewData attribute</span></span>

<span data-ttu-id="254ec-376">I dati possono essere passati a una pagina con <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> .</span><span class="sxs-lookup"><span data-stu-id="254ec-376">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="254ec-377">Le proprietà con l' `[ViewData]` attributo hanno i valori archiviati e caricati da <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> .</span><span class="sxs-lookup"><span data-stu-id="254ec-377">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="254ec-378">Nell'esempio seguente, `AboutModel` applica l' `[ViewData]` attributo alla `Title` proprietà:</span><span class="sxs-lookup"><span data-stu-id="254ec-378">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="254ec-379">Nella pagina About (Informazioni) accedere alla proprietà `Title` come proprietà del modello:</span><span class="sxs-lookup"><span data-stu-id="254ec-379">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="254ec-380">Nel layout il titolo viene letto dal dizionario ViewData:</span><span class="sxs-lookup"><span data-stu-id="254ec-380">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="254ec-381">TempData</span><span class="sxs-lookup"><span data-stu-id="254ec-381">TempData</span></span>

<span data-ttu-id="254ec-382">ASP.NET Core espone <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> .</span><span class="sxs-lookup"><span data-stu-id="254ec-382">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="254ec-383">Questa proprietà archivia i dati finché non viene letta.</span><span class="sxs-lookup"><span data-stu-id="254ec-383">This property stores data until it's read.</span></span> <span data-ttu-id="254ec-384">I metodi <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> e <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> possono essere usati per esaminare i dati senza eliminazione.</span><span class="sxs-lookup"><span data-stu-id="254ec-384">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="254ec-385">`TempData` è utile per il reindirizzamento, quando i dati sono necessari per più di una singola richiesta.</span><span class="sxs-lookup"><span data-stu-id="254ec-385">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="254ec-386">Il codice seguente imposta il valore di `Message` usando `TempData`:</span><span class="sxs-lookup"><span data-stu-id="254ec-386">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="254ec-387">Il markup seguente nel file *Pages/Customers/Index.cshtml* visualizza il valore di `Message` usando `TempData`.</span><span class="sxs-lookup"><span data-stu-id="254ec-387">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="254ec-388">Il modello di pagina *Pages/Customers/Index.cshtml.cs* applica l'attributo `[TempData]` alla proprietà `Message`.</span><span class="sxs-lookup"><span data-stu-id="254ec-388">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="254ec-389">Per ulteriori informazioni, vedere [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="254ec-389">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="254ec-390">Più gestori per pagina</span><span class="sxs-lookup"><span data-stu-id="254ec-390">Multiple handlers per page</span></span>

<span data-ttu-id="254ec-391">La pagina seguente genera markup per due gestori usando l'helper tag `asp-page-handler`:</span><span class="sxs-lookup"><span data-stu-id="254ec-391">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="254ec-392">Il form nell'esempio precedente contiene due pulsanti di invio, ognuno dei quali usa `FormActionTagHelper` per l'invio a un URL diverso.</span><span class="sxs-lookup"><span data-stu-id="254ec-392">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="254ec-393">L'attributo `asp-page-handler` è correlato a `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="254ec-393">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="254ec-394">`asp-page-handler` genera gli URL che indirizzano a ognuno dei metodi gestore definiti da una pagina.</span><span class="sxs-lookup"><span data-stu-id="254ec-394">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="254ec-395">`asp-page` non è specificato poiché l'esempio è collegato alla pagina corrente.</span><span class="sxs-lookup"><span data-stu-id="254ec-395">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="254ec-396">Il modello di pagina:</span><span class="sxs-lookup"><span data-stu-id="254ec-396">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="254ec-397">Il codice precedente usa *metodi gestore denominati*.</span><span class="sxs-lookup"><span data-stu-id="254ec-397">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="254ec-398">I metodi gestore denominati vengono creati usando il testo che nel nome segue `On<HTTP Verb>` e precede `Async` (se presente).</span><span class="sxs-lookup"><span data-stu-id="254ec-398">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="254ec-399">Nell'esempio precedente i metodi della pagina sono OnPost **JoinList** Async e OnPost **JoinListUC** Async.</span><span class="sxs-lookup"><span data-stu-id="254ec-399">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="254ec-400">Rimuovendo *OnPost* e *Async*, i nomi dei gestori sono `JoinList` e `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="254ec-400">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="254ec-401">Usando il codice precedente, il percorso URL che indirizza a `OnPostJoinListAsync` è `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="254ec-401">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="254ec-402">Il percorso URL che indirizza a `OnPostJoinListUCAsync` è `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="254ec-402">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="254ec-403">Route personalizzate</span><span class="sxs-lookup"><span data-stu-id="254ec-403">Custom routes</span></span>

<span data-ttu-id="254ec-404">Usare la direttiva `@page` per:</span><span class="sxs-lookup"><span data-stu-id="254ec-404">Use the `@page` directive to:</span></span>

* <span data-ttu-id="254ec-405">Specificare una route personalizzata a una pagina.</span><span class="sxs-lookup"><span data-stu-id="254ec-405">Specify a custom route to a page.</span></span> <span data-ttu-id="254ec-406">Ad esempio, è possibile impostare la route alla pagina About (Informazioni) su `/Some/Other/Path` con `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="254ec-406">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="254ec-407">Aggiungere segmenti alla route predefinita di una pagina.</span><span class="sxs-lookup"><span data-stu-id="254ec-407">Append segments to a page's default route.</span></span> <span data-ttu-id="254ec-408">Ad esempio, è possibile aggiungere un segmento "item" alla route predefinita di una pagina con `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="254ec-408">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="254ec-409">Aggiungere parametri alla route predefinita di una pagina.</span><span class="sxs-lookup"><span data-stu-id="254ec-409">Append parameters to a page's default route.</span></span> <span data-ttu-id="254ec-410">Ad esempio, un parametro ID, `id`, può essere necessario per una pagina con `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="254ec-410">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="254ec-411">Un percorso relativo alla directory radice designato da una tilde (`~`) all'inizio del percorso è supportato.</span><span class="sxs-lookup"><span data-stu-id="254ec-411">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="254ec-412">Ad esempio, `@page "~/Some/Other/Path"` è identico a `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="254ec-412">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="254ec-413">Se non si preferisce la stringa di query `?handler=JoinList` nell'URL, modificare la route per inserire il nome del gestore nella parte relativa al percorso dell'URL.</span><span class="sxs-lookup"><span data-stu-id="254ec-413">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="254ec-414">La route può essere personalizzata aggiungendo un modello di route racchiuso tra virgolette doppie dopo la `@page` direttiva.</span><span class="sxs-lookup"><span data-stu-id="254ec-414">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="254ec-415">Usando il codice precedente, il percorso URL che indirizza a `OnPostJoinListAsync` è `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="254ec-415">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="254ec-416">Il percorso URL che indirizza a `OnPostJoinListUCAsync` è `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="254ec-416">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="254ec-417">`?` che segue `handler` indica che il parametro di route è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="254ec-417">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="254ec-418">Impostazioni e configurazione avanzate</span><span class="sxs-lookup"><span data-stu-id="254ec-418">Advanced configuration and settings</span></span>

<span data-ttu-id="254ec-419">La configurazione e le impostazioni nelle sezioni seguenti non sono richieste dalla maggior parte delle app.</span><span class="sxs-lookup"><span data-stu-id="254ec-419">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="254ec-420">Per configurare le opzioni avanzate, usare l' <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> Overload che configura <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> :</span><span class="sxs-lookup"><span data-stu-id="254ec-420">To configure advanced options, use the <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="254ec-421">Utilizzare <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> per impostare la directory radice per le pagine o aggiungere le convenzioni del modello applicativo per le pagine.</span><span class="sxs-lookup"><span data-stu-id="254ec-421">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="254ec-422">Per ulteriori informazioni sulle convenzioni, vedere la pagina relativa alle [ Razor convenzioni di autorizzazione](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="254ec-422">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="254ec-423">Per eseguire la precompilazione delle visualizzazioni, vedere [ Razor visualizzare la compilazione](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="254ec-423">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="254ec-424">Consente di specificare che Razor le pagine si trovano nella radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="254ec-424">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="254ec-425">Per impostazione predefinita, Razor le pagine hanno una radice nella directory */pages*</span><span class="sxs-lookup"><span data-stu-id="254ec-425">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="254ec-426">Aggiungere <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> per specificare che le Razor pagine si trovano nella [radice del contenuto](xref:fundamentals/index#content-root) ( <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> ) dell'app:</span><span class="sxs-lookup"><span data-stu-id="254ec-426">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="254ec-427">Consente di specificare che le Razor pagine si trovano in una directory radice personalizzata</span><span class="sxs-lookup"><span data-stu-id="254ec-427">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="254ec-428">Aggiungere <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> per specificare che Razor le pagine si trovano in una directory radice personalizzata nell'app (fornire un percorso relativo):</span><span class="sxs-lookup"><span data-stu-id="254ec-428">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="254ec-429">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="254ec-429">Additional resources</span></span>

* <span data-ttu-id="254ec-430">Vedere Introduzione [alle Razor pagine](xref:tutorials/razor-pages/razor-pages-start), che si basa su questa introduzione.</span><span class="sxs-lookup"><span data-stu-id="254ec-430">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
* [<span data-ttu-id="254ec-431">Autorizzare gli attributi e le Razor pagine</span><span class="sxs-lookup"><span data-stu-id="254ec-431">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* [<span data-ttu-id="254ec-432">Scaricare o visualizzare il codice di esempio</span><span class="sxs-lookup"><span data-stu-id="254ec-432">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/prerendering-and-integration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

# <a name="visual-studio"></a>[<span data-ttu-id="254ec-433">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="254ec-433">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="254ec-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="254ec-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="254ec-435">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="254ec-435">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="254ec-436">Creazione di un Razor progetto di pagine</span><span class="sxs-lookup"><span data-stu-id="254ec-436">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="254ec-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="254ec-437">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="254ec-438">Per istruzioni dettagliate su come creare un progetto di pagine, vedere [Introduzione alle Razor pagine](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="254ec-438">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="254ec-439">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="254ec-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="254ec-440">Eseguire `dotnet new webapp` dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="254ec-440">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="254ec-441">Aprire il file *CSPROJ* generato da Visual Studio per Mac.</span><span class="sxs-lookup"><span data-stu-id="254ec-441">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="254ec-442">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="254ec-442">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="254ec-443">Eseguire `dotnet new webapp` dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="254ec-443">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="254ec-444">Razor Pagine</span><span class="sxs-lookup"><span data-stu-id="254ec-444">Razor Pages</span></span>

<span data-ttu-id="254ec-445">Razor Le pagine sono abilitate in *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="254ec-445">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="254ec-446">Si consideri una pagina di base: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="254ec-446">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="254ec-447">Il codice precedente ha un aspetto molto simile a quello di un [ Razor file di visualizzazione](xref:tutorials/first-mvc-app/adding-view) usato in un'app ASP.NET Core con i controller e le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="254ec-447">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="254ec-448">Ciò che lo differenzia è la direttiva `@page`.</span><span class="sxs-lookup"><span data-stu-id="254ec-448">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="254ec-449">`@page` trasforma il file in un'azione MVC, ovvero gestisce le richieste direttamente, senza passare attraverso un controller.</span><span class="sxs-lookup"><span data-stu-id="254ec-449">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="254ec-450">`@page` deve essere la prima Razor direttiva in una pagina.</span><span class="sxs-lookup"><span data-stu-id="254ec-450">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="254ec-451">`@page` influiscono sul comportamento di altri Razor costrutti.</span><span class="sxs-lookup"><span data-stu-id="254ec-451">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="254ec-452">Nei due file seguenti viene visualizzata una pagina simile che usa una classe `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="254ec-452">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="254ec-453">Il file *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="254ec-453">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="254ec-454">Il modello di pagina *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="254ec-454">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="254ec-455">Per convenzione, il `PageModel` file di classe ha lo stesso nome del Razor file di paging con l'aggiunta di *. cs* .</span><span class="sxs-lookup"><span data-stu-id="254ec-455">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="254ec-456">La pagina precedente, ad esempio, Razor è *pages/index2. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="254ec-456">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="254ec-457">Il file che contiene la classe `PageModel` è denominato *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="254ec-457">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="254ec-458">Le associazioni dei percorsi URL alle pagine sono determinate dalla posizione della pagina nel file system.</span><span class="sxs-lookup"><span data-stu-id="254ec-458">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="254ec-459">La tabella seguente mostra un Razor percorso di pagina e l'URL corrispondente:</span><span class="sxs-lookup"><span data-stu-id="254ec-459">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="254ec-460">Percorso e nome file</span><span class="sxs-lookup"><span data-stu-id="254ec-460">File name and path</span></span>               | <span data-ttu-id="254ec-461">URL corrispondente</span><span class="sxs-lookup"><span data-stu-id="254ec-461">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="254ec-462">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="254ec-462">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="254ec-463">`/` o `/Index`</span><span class="sxs-lookup"><span data-stu-id="254ec-463">`/` or `/Index`</span></span> |
| <span data-ttu-id="254ec-464">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="254ec-464">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="254ec-465">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="254ec-465">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="254ec-466">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="254ec-466">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="254ec-467">`/Store` o `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="254ec-467">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="254ec-468">Note:</span><span class="sxs-lookup"><span data-stu-id="254ec-468">Notes:</span></span>

* <span data-ttu-id="254ec-469">Per Razor impostazione predefinita, il runtime cerca i file di pagine nella cartella *pages* .</span><span class="sxs-lookup"><span data-stu-id="254ec-469">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="254ec-470">`Index` è la pagina predefinita quando un URL non include una pagina.</span><span class="sxs-lookup"><span data-stu-id="254ec-470">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="254ec-471">Scrivere un form di base</span><span class="sxs-lookup"><span data-stu-id="254ec-471">Write a basic form</span></span>

<span data-ttu-id="254ec-472">Razor Pagine è progettato per semplificare l'implementazione dei modelli comuni usati con i Web browser quando si compila un'app.</span><span class="sxs-lookup"><span data-stu-id="254ec-472">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="254ec-473">L' [associazione di modelli](xref:mvc/models/model-binding), gli [Helper Tag](xref:mvc/views/tag-helpers/intro)e gli helper HTML *funzionano solo* con le proprietà definite in una Razor classe di pagine.</span><span class="sxs-lookup"><span data-stu-id="254ec-473">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="254ec-474">Si consideri una pagina che implementa un form "contact us" di base per il modello `Contact`:</span><span class="sxs-lookup"><span data-stu-id="254ec-474">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="254ec-475">Per gli esempi riportati in questo documento, `DbContext` viene inizializzato nel file [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16).</span><span class="sxs-lookup"><span data-stu-id="254ec-475">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="254ec-476">Il modello di dati:</span><span class="sxs-lookup"><span data-stu-id="254ec-476">The data model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="254ec-477">Il contesto del database:</span><span class="sxs-lookup"><span data-stu-id="254ec-477">The db context:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="254ec-478">Il file di visualizzazione *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="254ec-478">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="254ec-479">Il modello di pagina *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="254ec-479">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="254ec-480">Per convenzione, la classe `PageModel` è denominata `<PageName>Model` e si trova nello stesso spazio dei nomi della pagina.</span><span class="sxs-lookup"><span data-stu-id="254ec-480">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="254ec-481">La classe `PageModel` consente la separazione della logica di una pagina dalla relativa presentazione.</span><span class="sxs-lookup"><span data-stu-id="254ec-481">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="254ec-482">Definisce i gestori di pagina per le richieste inviate alla pagina e i dati usati per il rendering della pagina.</span><span class="sxs-lookup"><span data-stu-id="254ec-482">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="254ec-483">Questa separazione consente:</span><span class="sxs-lookup"><span data-stu-id="254ec-483">This separation allows:</span></span>

* <span data-ttu-id="254ec-484">Gestione delle dipendenze di pagina tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="254ec-484">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="254ec-485">[Testing unità](xref:test/razor-pages-tests) delle pagine.</span><span class="sxs-lookup"><span data-stu-id="254ec-485">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="254ec-486">La pagina contiene un oggetto , ovvero un `OnPostAsync` *metodo gestore* che viene eseguito per le richieste `POST`, quando un utente invia il form.</span><span class="sxs-lookup"><span data-stu-id="254ec-486">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="254ec-487">È possibile aggiungere metodi del gestore per qualsiasi verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="254ec-487">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="254ec-488">I gestori più comuni sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="254ec-488">The most common handlers are:</span></span>

* <span data-ttu-id="254ec-489">`OnGet` per inizializzare lo stato necessario per la pagina.</span><span class="sxs-lookup"><span data-stu-id="254ec-489">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="254ec-490">Esempio di [OnGet](#OnGet).</span><span class="sxs-lookup"><span data-stu-id="254ec-490">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="254ec-491">`OnPost` per gestire gli invii di form.</span><span class="sxs-lookup"><span data-stu-id="254ec-491">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="254ec-492">Il suffisso `Async` nel nome è facoltativo, ma viene spesso usato per convenzione per le funzioni asincrone.</span><span class="sxs-lookup"><span data-stu-id="254ec-492">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="254ec-493">Il codice precedente è tipico per le Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="254ec-493">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="254ec-494">Se si ha familiarità con le app ASP.NET che usano i controller e le visualizzazioni:</span><span class="sxs-lookup"><span data-stu-id="254ec-494">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="254ec-495">Il `OnPostAsync` codice nell'esempio precedente ha un aspetto simile al codice del controller tipico.</span><span class="sxs-lookup"><span data-stu-id="254ec-495">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="254ec-496">La maggior parte delle primitive MVC, ad esempio l' [associazione di modelli](xref:mvc/models/model-binding), la [convalida](xref:mvc/models/validation), la [convalida](xref:mvc/models/validation)e i risultati dell'azione, sono condivisi.</span><span class="sxs-lookup"><span data-stu-id="254ec-496">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="254ec-497">Il metodo `OnPostAsync` precedente:</span><span class="sxs-lookup"><span data-stu-id="254ec-497">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="254ec-498">Il flusso di base di `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="254ec-498">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="254ec-499">Verificare se sono presenti errori di convalida.</span><span class="sxs-lookup"><span data-stu-id="254ec-499">Check for validation errors.</span></span>

* <span data-ttu-id="254ec-500">Se non sono presenti errori, salvare i dati e reindirizzare.</span><span class="sxs-lookup"><span data-stu-id="254ec-500">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="254ec-501">Se sono presenti errori, visualizzare di nuovo la pagina con i messaggi di convalida.</span><span class="sxs-lookup"><span data-stu-id="254ec-501">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="254ec-502">La convalida lato client è identica alle applicazioni ASP.NET Core MVC tradizionali.</span><span class="sxs-lookup"><span data-stu-id="254ec-502">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="254ec-503">In molti casi, gli errori di convalida vengono rilevati nel client e non vengono mai inviati al server.</span><span class="sxs-lookup"><span data-stu-id="254ec-503">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="254ec-504">Quando i dati vengono immessi correttamente, il metodo gestore `OnPostAsync` chiama il metodo helper `RedirectToPage` per restituire un'istanza di `RedirectToPageResult`.</span><span class="sxs-lookup"><span data-stu-id="254ec-504">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="254ec-505">`RedirectToPage` è un nuovo risultato dell'azione, simile a `RedirectToAction` o `RedirectToRoute`, ma personalizzato per le pagine.</span><span class="sxs-lookup"><span data-stu-id="254ec-505">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="254ec-506">Nell'esempio precedente viene reindirizzato alla pagina di indice radice (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="254ec-506">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="254ec-507">`RedirectToPage` è descritto in dettaglio nella sezione [generazione URL per pagine](#url_gen) .</span><span class="sxs-lookup"><span data-stu-id="254ec-507">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="254ec-508">Quando il form inviato contiene errori di convalida (che vengono passati al server), il metodo gestore `OnPostAsync` chiama il metodo helper `Page`.</span><span class="sxs-lookup"><span data-stu-id="254ec-508">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="254ec-509">`Page` restituisce un'istanza di `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="254ec-509">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="254ec-510">La restituzione di `Page` è simile al modo in cui le azioni nel controller restituiscono `View`.</span><span class="sxs-lookup"><span data-stu-id="254ec-510">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="254ec-511">`PageResult` è il tipo restituito predefinito per un metodo del gestore.</span><span class="sxs-lookup"><span data-stu-id="254ec-511">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="254ec-512">Un metodo gestore che restituisce `void` esegue il rendering della pagina.</span><span class="sxs-lookup"><span data-stu-id="254ec-512">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="254ec-513">La proprietà `Customer` usa l'attributo `[BindProperty]` optare per consentire l'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="254ec-513">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="254ec-514">Razor Le pagine, per impostazione predefinita, associano proprietà solo con non `GET` verbi.</span><span class="sxs-lookup"><span data-stu-id="254ec-514">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="254ec-515">L'associazione alle proprietà può ridurre la quantità di codice da scrivere.</span><span class="sxs-lookup"><span data-stu-id="254ec-515">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="254ec-516">Riduce il codice usando la stessa proprietà per il eseguire il rendering dei campi del form (`<input asp-for="Customer.Name">`) e accettare l'input.</span><span class="sxs-lookup"><span data-stu-id="254ec-516">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="254ec-517">La home page (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="254ec-517">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="254ec-518">La classe `PageModel` associata (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="254ec-518">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="254ec-519">Il file *Index.cshtml* contiene il markup seguente per creare un collegamento di modifica per ogni contatto:</span><span class="sxs-lookup"><span data-stu-id="254ec-519">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="254ec-520">L' `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Helper tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) ha usato l' `asp-route-{value}` attributo per generare un collegamento alla pagina di modifica.</span><span class="sxs-lookup"><span data-stu-id="254ec-520">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="254ec-521">Il collegamento contiene i dati della route con l'ID contatto.</span><span class="sxs-lookup"><span data-stu-id="254ec-521">The link contains route data with the contact ID.</span></span> <span data-ttu-id="254ec-522">Ad esempio: `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="254ec-522">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="254ec-523">Gli [Helper Tag](xref:mvc/views/tag-helpers/intro) consentono al codice lato server di partecipare alla creazione e al rendering di elementi HTML nei Razor file.</span><span class="sxs-lookup"><span data-stu-id="254ec-523">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="254ec-524">Gli helper tag sono abilitati da `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="254ec-524">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="254ec-525">Il file *Pages/Edit.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="254ec-525">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="254ec-526">La prima riga contiene la direttiva `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="254ec-526">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="254ec-527">Il vincolo di routing `"{id:int}"` indica alla pagina di accettare le richieste inviate alla pagina che contengono i dati della route `int`.</span><span class="sxs-lookup"><span data-stu-id="254ec-527">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="254ec-528">Se una richiesta inviata alla pagina non contiene dati della route che possono essere convertiti in un oggetto `int`, il runtime restituisce un errore HTTP 404 (non trovato).</span><span class="sxs-lookup"><span data-stu-id="254ec-528">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="254ec-529">Per rendere l'ID facoltativo, aggiungere `?` al vincolo di route:</span><span class="sxs-lookup"><span data-stu-id="254ec-529">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="254ec-530">Il file *Pages/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="254ec-530">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="254ec-531">Il file *Index.cshtml* contiene anche il markup per creare un pulsante di eliminazione per ogni contatto cliente:</span><span class="sxs-lookup"><span data-stu-id="254ec-531">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="254ec-532">Quando viene eseguito il rendering del pulsante di eliminazione in HTML, il relativo `formaction` include i parametri per:</span><span class="sxs-lookup"><span data-stu-id="254ec-532">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="254ec-533">L'ID di contatto cliente dall'attributo `asp-route-id`.</span><span class="sxs-lookup"><span data-stu-id="254ec-533">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="254ec-534">L'`handler` specificato dall'attributo `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="254ec-534">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="254ec-535">Di seguito è riportato un esempio di pulsante di eliminazione di cui è stato eseguito il rendering con un ID di contatto cliente `1`:</span><span class="sxs-lookup"><span data-stu-id="254ec-535">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="254ec-536">Quando il pulsante è selezionato, viene inviata una richiesta `POST` di modulo al server.</span><span class="sxs-lookup"><span data-stu-id="254ec-536">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="254ec-537">Per convenzione, il nome del metodo del gestore viene selezionato in base al valore del parametro `handler` in base allo schema `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="254ec-537">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="254ec-538">Poiché in questo esempio l'`handler` è `delete`, il metodo `OnPostDeleteAsync` viene usato per elaborare la richiesta `POST`.</span><span class="sxs-lookup"><span data-stu-id="254ec-538">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="254ec-539">Se `asp-page-handler` viene impostato su un valore diverso, ad esempio `remove`, viene selezionato un metodo gestore con il nome `OnPostRemoveAsync`.</span><span class="sxs-lookup"><span data-stu-id="254ec-539">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="254ec-540">Il codice seguente illustra il `OnPostDeleteAsync` gestore:</span><span class="sxs-lookup"><span data-stu-id="254ec-540">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="254ec-541">Il metodo `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="254ec-541">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="254ec-542">Accetta l'`id` dalla stringa di query.</span><span class="sxs-lookup"><span data-stu-id="254ec-542">Accepts the `id` from the query string.</span></span> <span data-ttu-id="254ec-543">Se la direttiva della pagina *index. cshtml* conteneva il vincolo `"{id:int?}"` di routing, `id` verrebbe derivare dai dati della route.</span><span class="sxs-lookup"><span data-stu-id="254ec-543">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="254ec-544">I dati della route per `id` sono specificati nell'URI, ad esempio `https://localhost:5001/Customers/2` .</span><span class="sxs-lookup"><span data-stu-id="254ec-544">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="254ec-545">Interroga il database in merito al contatto del cliente con `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="254ec-545">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="254ec-546">Se viene trovato il contatto cliente, viene rimosso dall'elenco dei contatti del cliente.</span><span class="sxs-lookup"><span data-stu-id="254ec-546">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="254ec-547">Il database viene aggiornato.</span><span class="sxs-lookup"><span data-stu-id="254ec-547">The database is updated.</span></span>
* <span data-ttu-id="254ec-548">Chiama `RedirectToPage` per reindirizzare alla pagina di indice radice (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="254ec-548">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="254ec-549">Contrassegnare le proprietà della pagina in base alle esigenze</span><span class="sxs-lookup"><span data-stu-id="254ec-549">Mark page properties as required</span></span>

<span data-ttu-id="254ec-550">Le proprietà di un oggetto `PageModel` possono essere contrassegnate con l'attributo [obbligatorio](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) :</span><span class="sxs-lookup"><span data-stu-id="254ec-550">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="254ec-551">Per altre informazioni, vedere [Convalida del modello](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="254ec-551">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="254ec-552">Gestire le richieste HEAD con un fallback del gestore OnGet</span><span class="sxs-lookup"><span data-stu-id="254ec-552">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="254ec-553">Le richieste `HEAD` consentono di recuperare le intestazioni di una risorsa specifica.</span><span class="sxs-lookup"><span data-stu-id="254ec-553">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="254ec-554">A differenza delle richieste `GET`, le richieste `HEAD` non restituiscono un corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="254ec-554">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="254ec-555">In genere, per le richieste `HEAD` viene creato e chiamato un gestore `OnHead`:</span><span class="sxs-lookup"><span data-stu-id="254ec-555">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="254ec-556">In ASP.NET Core 2,1 o versioni successive, Razor le pagine eseguono il fallback alla chiamata del `OnGet` gestore se non `OnHead` è stato definito alcun gestore.</span><span class="sxs-lookup"><span data-stu-id="254ec-556">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="254ec-557">Questo comportamento è abilitato tramite la chiamata a [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="254ec-557">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="254ec-558">I modelli predefiniti generano la chiamata `SetCompatibilityVersion` in ASP.NET Core 2.1 e 2.2.</span><span class="sxs-lookup"><span data-stu-id="254ec-558">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="254ec-559">`SetCompatibilityVersion` imposta effettivamente l' Razor opzione Pages `AllowMappingHeadRequestsToGetHandler` su `true` .</span><span class="sxs-lookup"><span data-stu-id="254ec-559">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="254ec-560">Anziché acconsentire esplicitamente a tutti i comportamenti con `SetCompatibilityVersion`, è possibile acconsentire a comportamenti *specifici*.</span><span class="sxs-lookup"><span data-stu-id="254ec-560">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="254ec-561">Il codice seguente acconsente esplicitamente al mapping delle richieste `HEAD` al gestore `OnGet`:</span><span class="sxs-lookup"><span data-stu-id="254ec-561">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="254ec-562">XSRF/CSRF e Razor pagine</span><span class="sxs-lookup"><span data-stu-id="254ec-562">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="254ec-563">Non è necessario scrivere codice per la [convalida antifalsificazione](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="254ec-563">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="254ec-564">La generazione e la convalida di token antifalsificazione sono incluse automaticamente nelle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="254ec-564">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="254ec-565">Uso di layout, parti parziali, modelli e helper tag con Razor pagine</span><span class="sxs-lookup"><span data-stu-id="254ec-565">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="254ec-566">Le pagine funzionano con tutte le funzionalità del Razor motore di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="254ec-566">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="254ec-567">Layout, parti parziali, modelli, helper tag, *_ViewStart. cshtml*, *_ViewImports. cshtml* funzionano esattamente come per le Razor visualizzazioni convenzionali.</span><span class="sxs-lookup"><span data-stu-id="254ec-567">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="254ec-568">La pagina verrà ora riorganizzata in modo da usufruire di alcune di queste funzionalità.</span><span class="sxs-lookup"><span data-stu-id="254ec-568">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="254ec-569">Aggiungere una [pagina di layout](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="254ec-569">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="254ec-570">Il [layout](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="254ec-570">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="254ec-571">Controlla il layout di ogni pagina, a meno che la pagina non accetti il layout.</span><span class="sxs-lookup"><span data-stu-id="254ec-571">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="254ec-572">Importa le strutture HTML, ad esempio JavaScript e i fogli di stile.</span><span class="sxs-lookup"><span data-stu-id="254ec-572">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="254ec-573">Vedere l'articolo sulla [pagina Layout](xref:mvc/views/layout) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="254ec-573">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="254ec-574">La proprietà [Layout](xref:mvc/views/layout#specifying-a-layout) proprietà viene impostata in *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="254ec-574">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="254ec-575">Il layout è nella cartella *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="254ec-575">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="254ec-576">Le pagine cercano altre visualizzazioni (layout, modelli, righe parzialmente eseguite) in modo gerarchico, partendo dalla stessa cartella della pagina corrente.</span><span class="sxs-lookup"><span data-stu-id="254ec-576">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="254ec-577">Un layout nella cartella pages */Shared* può essere utilizzato da qualsiasi Razor pagina nella cartella *pages* .</span><span class="sxs-lookup"><span data-stu-id="254ec-577">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="254ec-578">Il file di layout dovrebbe andare nella cartella *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="254ec-578">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="254ec-579">Si consiglia di **non** inserire il file di layout nella cartella *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="254ec-579">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="254ec-580">*Views/Shared* è un modello destinato alle visualizzazioni MVC.</span><span class="sxs-lookup"><span data-stu-id="254ec-580">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="254ec-581">Razor Le pagine hanno lo scopo di basarsi sulla gerarchia di cartelle, non sulle convenzioni di percorso.</span><span class="sxs-lookup"><span data-stu-id="254ec-581">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="254ec-582">Visualizza la ricerca da una Razor pagina include la cartella *pagine* .</span><span class="sxs-lookup"><span data-stu-id="254ec-582">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="254ec-583">I layout, i modelli e i parziali utilizzati con i controller MVC e le Razor visualizzazioni convenzionali *funzionano semplicemente*.</span><span class="sxs-lookup"><span data-stu-id="254ec-583">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="254ec-584">Aggiungere un file *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="254ec-584">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="254ec-585">`@namespace` viene spiegato in seguito nell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="254ec-585">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="254ec-586">La direttiva `@addTagHelper` inserisce gli [helper tag predefiniti](xref:mvc/views/tag-helpers/builtin-th/Index) in tutte le pagine presenti nella cartella *Pages*.</span><span class="sxs-lookup"><span data-stu-id="254ec-586">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="254ec-587">Quando la direttiva `@namespace` viene usata in modo esplicito in una pagina:</span><span class="sxs-lookup"><span data-stu-id="254ec-587">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="254ec-588">La direttiva imposta lo spazio dei nomi per la pagina.</span><span class="sxs-lookup"><span data-stu-id="254ec-588">The directive sets the namespace for the page.</span></span> <span data-ttu-id="254ec-589">La direttiva `@model` non deve necessariamente includere lo spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="254ec-589">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="254ec-590">Se la direttiva `@namespace` è contenuta in *_ViewImports.cshtml*, lo spazio dei nomi specificato indica il prefisso per lo spazio dei nomi generato nella pagina che importa la direttiva `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="254ec-590">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="254ec-591">Il resto dello spazio dei nomi generato (la parte del suffisso) è il percorso relativo separato dal punto tra la cartella contenente *_Viewimports.cshtml* e la cartella contenente la pagina.</span><span class="sxs-lookup"><span data-stu-id="254ec-591">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="254ec-592">Ad esempio, la classe `PageModel`*Pages/Customers/Edit.cshtml.cs* imposta in modo esplicito lo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="254ec-592">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="254ec-593">Il file *Pages/_ViewImports.cshtml* file imposta il seguente spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="254ec-593">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="254ec-594">Lo spazio dei nomi generato per la pagina *pages/Customers/Edit. cshtml* Razor è uguale a quello della `PageModel` classe.</span><span class="sxs-lookup"><span data-stu-id="254ec-594">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="254ec-595">`@namespace`*funziona anche con le Razor visualizzazioni convenzionali.*</span><span class="sxs-lookup"><span data-stu-id="254ec-595">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="254ec-596">Il file di visualizzazione *Pages/Create.cshtml* originale:</span><span class="sxs-lookup"><span data-stu-id="254ec-596">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="254ec-597">Il file di visualizzazione *Pages/Create.cshtml* aggiornato:</span><span class="sxs-lookup"><span data-stu-id="254ec-597">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="254ec-598">Il [ Razor progetto di avvio pagine](#rpvs17) contiene le *pagine/_ValidationScriptsPartial. cshtml*, che consente di associare la convalida lato client.</span><span class="sxs-lookup"><span data-stu-id="254ec-598">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="254ec-599">Per altre informazioni sulle visualizzazioni parziali, vedere <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="254ec-599">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="254ec-600">Generazione di URL per le pagine</span><span class="sxs-lookup"><span data-stu-id="254ec-600">URL generation for Pages</span></span>

<span data-ttu-id="254ec-601">La pagina `Create`, riportata in precedenza, usa `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="254ec-601">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="254ec-602">L'applicazione ha la struttura di file o cartella seguente:</span><span class="sxs-lookup"><span data-stu-id="254ec-602">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="254ec-603">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="254ec-603">*/Pages*</span></span>

  * <span data-ttu-id="254ec-604">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="254ec-604">*Index.cshtml*</span></span>
  * <span data-ttu-id="254ec-605">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="254ec-605">*/Customers*</span></span>

    * <span data-ttu-id="254ec-606">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="254ec-606">*Create.cshtml*</span></span>
    * <span data-ttu-id="254ec-607">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="254ec-607">*Edit.cshtml*</span></span>
    * <span data-ttu-id="254ec-608">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="254ec-608">*Index.cshtml*</span></span>

<span data-ttu-id="254ec-609">Le pagine *Pages/Customers/Create.cshtml* e *Pages/Customers/Edit.cshtml* reindirizzano a *Pages/Index.cshtml* dopo l'esecuzione.</span><span class="sxs-lookup"><span data-stu-id="254ec-609">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="254ec-610">La stringa `/Index` fa parte dell'URI di accesso alla pagina precedente.</span><span class="sxs-lookup"><span data-stu-id="254ec-610">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="254ec-611">La stringa `/Index` può essere usata per generare gli URI alla pagina *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="254ec-611">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="254ec-612">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="254ec-612">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="254ec-613">Il nome della pagina è il percorso alla pagina dalla cartella radice */Pages*, inclusa una barra iniziale `/`, ad esempio `/Index`.</span><span class="sxs-lookup"><span data-stu-id="254ec-613">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="254ec-614">Gli esempi di generazione di URL precedenti offrono opzioni e caratteristiche funzionali avanzate rispetto agli URL hardcoded.</span><span class="sxs-lookup"><span data-stu-id="254ec-614">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="254ec-615">La generazione di URL usa il [routing](xref:mvc/controllers/routing) ed è in grado di generare e codificare i parametri in base al modo in cui la route è definita nel percorso di destinazione.</span><span class="sxs-lookup"><span data-stu-id="254ec-615">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="254ec-616">La generazione di URL per le pagine supporta i nomi relativi.</span><span class="sxs-lookup"><span data-stu-id="254ec-616">URL generation for pages supports relative names.</span></span> <span data-ttu-id="254ec-617">La tabella seguente indica quale pagina di indice viene selezionata con diversi parametri `RedirectToPage` da *Pages/Customers/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="254ec-617">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="254ec-618">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="254ec-618">RedirectToPage(x)</span></span>| <span data-ttu-id="254ec-619">Pagina</span><span class="sxs-lookup"><span data-stu-id="254ec-619">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="254ec-620">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="254ec-620">RedirectToPage("/Index")</span></span> | <span data-ttu-id="254ec-621">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="254ec-621">*Pages/Index*</span></span> |
| <span data-ttu-id="254ec-622">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="254ec-622">RedirectToPage("./Index");</span></span> | <span data-ttu-id="254ec-623">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="254ec-623">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="254ec-624">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="254ec-624">RedirectToPage("../Index")</span></span> | <span data-ttu-id="254ec-625">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="254ec-625">*Pages/Index*</span></span> |
| <span data-ttu-id="254ec-626">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="254ec-626">RedirectToPage("Index")</span></span>  | <span data-ttu-id="254ec-627">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="254ec-627">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="254ec-628">`RedirectToPage("Index")`, `RedirectToPage("./Index")` e `RedirectToPage("../Index")`  sono *nomi relativi*.</span><span class="sxs-lookup"><span data-stu-id="254ec-628">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="254ec-629">Il parametro `RedirectToPage` è *combinato* con il percorso della pagina corrente per calcolare il nome della pagina di destinazione.</span><span class="sxs-lookup"><span data-stu-id="254ec-629">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="254ec-630">Il collegamento dei nomi relativi è utile quando si compilano siti con una struttura complessa.</span><span class="sxs-lookup"><span data-stu-id="254ec-630">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="254ec-631">Se si usano i nomi relativi per il collegamento tra le pagine in una cartella, è possibile rinominare tale cartella.</span><span class="sxs-lookup"><span data-stu-id="254ec-631">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="254ec-632">Tutti i collegamenti continuano a funzionare perché non includono il nome della cartella.</span><span class="sxs-lookup"><span data-stu-id="254ec-632">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="254ec-633">Per reindirizzare la pagina a un'[Area](xref:mvc/controllers/areas) diversa, specificare l'area:</span><span class="sxs-lookup"><span data-stu-id="254ec-633">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="254ec-634">Per altre informazioni, vedere <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="254ec-634">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="254ec-635">Attributo viewData</span><span class="sxs-lookup"><span data-stu-id="254ec-635">ViewData attribute</span></span>

<span data-ttu-id="254ec-636">È possibile passare i dati a una pagina tramite [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="254ec-636">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="254ec-637">I valori delle proprietà nei controller o nei Razor modelli di pagina con l' `[ViewData]` attributo sono archiviati e caricati da [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="254ec-637">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="254ec-638">Nell'esempio seguente, `AboutModel` contiene una `Title` proprietà contrassegnata con `[ViewData]` .</span><span class="sxs-lookup"><span data-stu-id="254ec-638">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="254ec-639">La proprietà `Title` è impostata sul titolo della pagina About (Informazioni):</span><span class="sxs-lookup"><span data-stu-id="254ec-639">The `Title` property is set to the title of the About page:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="254ec-640">Nella pagina About (Informazioni) accedere alla proprietà `Title` come proprietà del modello:</span><span class="sxs-lookup"><span data-stu-id="254ec-640">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="254ec-641">Nel layout il titolo viene letto dal dizionario ViewData:</span><span class="sxs-lookup"><span data-stu-id="254ec-641">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="254ec-642">TempData</span><span class="sxs-lookup"><span data-stu-id="254ec-642">TempData</span></span>

<span data-ttu-id="254ec-643">ASP.NET Core espone la proprietà [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata#Microsoft_AspNetCore_Mvc_Controller_TempData) per un [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="254ec-643">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="254ec-644">Questa proprietà archivia i dati finché non viene letta.</span><span class="sxs-lookup"><span data-stu-id="254ec-644">This property stores data until it's read.</span></span> <span data-ttu-id="254ec-645">I metodi `Keep` e `Peek` possono essere usati per esaminare i dati senza eliminazione.</span><span class="sxs-lookup"><span data-stu-id="254ec-645">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="254ec-646">`TempData` è utile per il reindirizzamento quando i dati sono necessari per più di una singola richiesta.</span><span class="sxs-lookup"><span data-stu-id="254ec-646">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="254ec-647">Il codice seguente imposta il valore di `Message` usando `TempData`:</span><span class="sxs-lookup"><span data-stu-id="254ec-647">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="254ec-648">Il markup seguente nel file *Pages/Customers/Index.cshtml* visualizza il valore di `Message` usando `TempData`.</span><span class="sxs-lookup"><span data-stu-id="254ec-648">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="254ec-649">Il modello di pagina *Pages/Customers/Index.cshtml.cs* applica l'attributo `[TempData]` alla proprietà `Message`.</span><span class="sxs-lookup"><span data-stu-id="254ec-649">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="254ec-650">Per altre informazioni, vedere [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="254ec-650">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="254ec-651">Più gestori per pagina</span><span class="sxs-lookup"><span data-stu-id="254ec-651">Multiple handlers per page</span></span>

<span data-ttu-id="254ec-652">La pagina seguente genera markup per due gestori usando l'helper tag `asp-page-handler`:</span><span class="sxs-lookup"><span data-stu-id="254ec-652">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="254ec-653">Il form nell'esempio precedente contiene due pulsanti di invio, ognuno dei quali usa `FormActionTagHelper` per l'invio a un URL diverso.</span><span class="sxs-lookup"><span data-stu-id="254ec-653">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="254ec-654">L'attributo `asp-page-handler` è correlato a `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="254ec-654">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="254ec-655">`asp-page-handler` genera gli URL che indirizzano a ognuno dei metodi gestore definiti da una pagina.</span><span class="sxs-lookup"><span data-stu-id="254ec-655">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="254ec-656">`asp-page` non è specificato poiché l'esempio è collegato alla pagina corrente.</span><span class="sxs-lookup"><span data-stu-id="254ec-656">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="254ec-657">Il modello di pagina:</span><span class="sxs-lookup"><span data-stu-id="254ec-657">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="254ec-658">Il codice precedente usa *metodi gestore denominati*.</span><span class="sxs-lookup"><span data-stu-id="254ec-658">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="254ec-659">I metodi gestore denominati vengono creati usando il testo che nel nome segue `On<HTTP Verb>` e precede `Async` (se presente).</span><span class="sxs-lookup"><span data-stu-id="254ec-659">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="254ec-660">Nell'esempio precedente i metodi della pagina sono OnPost **JoinList** Async e OnPost **JoinListUC** Async.</span><span class="sxs-lookup"><span data-stu-id="254ec-660">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="254ec-661">Rimuovendo *OnPost* e *Async*, i nomi dei gestori sono `JoinList` e `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="254ec-661">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="254ec-662">Usando il codice precedente, il percorso URL che indirizza a `OnPostJoinListAsync` è `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="254ec-662">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="254ec-663">Il percorso URL che indirizza a `OnPostJoinListUCAsync` è `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="254ec-663">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="254ec-664">Route personalizzate</span><span class="sxs-lookup"><span data-stu-id="254ec-664">Custom routes</span></span>

<span data-ttu-id="254ec-665">Usare la direttiva `@page` per:</span><span class="sxs-lookup"><span data-stu-id="254ec-665">Use the `@page` directive to:</span></span>

* <span data-ttu-id="254ec-666">Specificare una route personalizzata a una pagina.</span><span class="sxs-lookup"><span data-stu-id="254ec-666">Specify a custom route to a page.</span></span> <span data-ttu-id="254ec-667">Ad esempio, è possibile impostare la route alla pagina About (Informazioni) su `/Some/Other/Path` con `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="254ec-667">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="254ec-668">Aggiungere segmenti alla route predefinita di una pagina.</span><span class="sxs-lookup"><span data-stu-id="254ec-668">Append segments to a page's default route.</span></span> <span data-ttu-id="254ec-669">Ad esempio, è possibile aggiungere un segmento "item" alla route predefinita di una pagina con `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="254ec-669">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="254ec-670">Aggiungere parametri alla route predefinita di una pagina.</span><span class="sxs-lookup"><span data-stu-id="254ec-670">Append parameters to a page's default route.</span></span> <span data-ttu-id="254ec-671">Ad esempio, un parametro ID, `id`, può essere necessario per una pagina con `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="254ec-671">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="254ec-672">Un percorso relativo alla directory radice designato da una tilde (`~`) all'inizio del percorso è supportato.</span><span class="sxs-lookup"><span data-stu-id="254ec-672">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="254ec-673">Ad esempio, `@page "~/Some/Other/Path"` è identico a `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="254ec-673">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="254ec-674">Se non si preferisce la stringa di query `?handler=JoinList` nell'URL, modificare la route per inserire il nome del gestore nella parte relativa al percorso dell'URL.</span><span class="sxs-lookup"><span data-stu-id="254ec-674">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="254ec-675">La route può essere personalizzata aggiungendo un modello di route racchiuso tra virgolette doppie dopo la `@page` direttiva.</span><span class="sxs-lookup"><span data-stu-id="254ec-675">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="254ec-676">Usando il codice precedente, il percorso URL che indirizza a `OnPostJoinListAsync` è `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="254ec-676">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="254ec-677">Il percorso URL che indirizza a `OnPostJoinListUCAsync` è `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="254ec-677">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="254ec-678">`?` che segue `handler` indica che il parametro di route è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="254ec-678">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="254ec-679">Configurazione e impostazioni</span><span class="sxs-lookup"><span data-stu-id="254ec-679">Configuration and settings</span></span>

<span data-ttu-id="254ec-680">Per configurare le opzioni avanzate, usare il metodo di estensione `AddRazorPagesOptions` nel generatore MVC:</span><span class="sxs-lookup"><span data-stu-id="254ec-680">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="254ec-681">Attualmente è possibile usare `RazorPagesOptions` per impostare la directory radice per le pagine o aggiungere convenzioni di modello applicativo per le pagine.</span><span class="sxs-lookup"><span data-stu-id="254ec-681">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="254ec-682">In questo modo si potrà garantire una maggiore estendibilità in futuro.</span><span class="sxs-lookup"><span data-stu-id="254ec-682">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="254ec-683">Per eseguire la precompilazione delle visualizzazioni, vedere [ Razor visualizzare la compilazione](xref:mvc/views/view-compilation) .</span><span class="sxs-lookup"><span data-stu-id="254ec-683">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="254ec-684">[Scaricare o visualizzare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="254ec-684">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="254ec-685">Vedere Introduzione [alle Razor pagine](xref:tutorials/razor-pages/razor-pages-start), che si basa su questa introduzione.</span><span class="sxs-lookup"><span data-stu-id="254ec-685">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="254ec-686">Consente di specificare che Razor le pagine si trovano nella radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="254ec-686">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="254ec-687">Per impostazione predefinita, Razor le pagine hanno una radice nella directory */pages*</span><span class="sxs-lookup"><span data-stu-id="254ec-687">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="254ec-688">Aggiungere [con Razor PagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) a [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) per specificare che le Razor pagine si trovano nella [radice del contenuto](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) dell'app:</span><span class="sxs-lookup"><span data-stu-id="254ec-688">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="254ec-689">Consente di specificare che le Razor pagine si trovano in una directory radice personalizzata</span><span class="sxs-lookup"><span data-stu-id="254ec-689">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="254ec-690">Aggiungere [con Razor PagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) a [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) per specificare che le Razor pagine si trovano in una directory radice personalizzata nell'app (fornire un percorso relativo):</span><span class="sxs-lookup"><span data-stu-id="254ec-690">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="254ec-691">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="254ec-691">Additional resources</span></span>

* [<span data-ttu-id="254ec-692">Autorizzare gli attributi e le Razor pagine</span><span class="sxs-lookup"><span data-stu-id="254ec-692">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
