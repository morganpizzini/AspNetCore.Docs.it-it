---
title: Introduzione alle Razor pagine ASP.NET Core
author: Rick-Anderson
description: Informazioni Razor sul modo in cui le pagine ASP.NET Core rendono più semplici e più produttivi gli scenari incentrati sulla pagina della codifica rispetto all'utilizzo di MVC
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/index
ms.openlocfilehash: e7e9bc76a2bba1c15b32c97f0f3629285f256499
ms.sourcegitcommit: d9ae1f352d372a20534b57e23646c1a1d9171af1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/21/2020
ms.locfileid: "86568705"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="40206-103">Introduzione alle Razor pagine ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="40206-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="40206-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="40206-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

Razor<span data-ttu-id="40206-105">Le pagine possono rendere più semplici e più produttivi gli scenari incentrati sulla pagina di codifica rispetto all'utilizzo di controller e visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="40206-105"> Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="40206-106">Se si sta cercando un'esercitazione in cui si usa l'approccio Model-View-Controller, vedere [Introduzione ad ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="40206-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="40206-107">Questo documento fornisce un'introduzione alle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="40206-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="40206-108">Non è un'esercitazione dettagliata.</span><span class="sxs-lookup"><span data-stu-id="40206-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="40206-109">Se alcune delle sezioni sono troppo avanzate, vedere [Introduzione alle Razor pagine](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="40206-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="40206-110">Per una panoramica di ASP.NET Core, vedere [Introduzione a ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="40206-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="40206-111">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="40206-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="40206-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40206-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="40206-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="40206-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="40206-114">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="40206-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="40206-115">Creazione di un Razor progetto di pagine</span><span class="sxs-lookup"><span data-stu-id="40206-115">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="40206-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40206-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="40206-117">Per istruzioni dettagliate su come creare un progetto di pagine, vedere [Introduzione alle Razor pagine](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="40206-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="40206-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="40206-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="40206-119">Eseguire `dotnet new webapp` dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="40206-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="40206-120">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="40206-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="40206-121">Per istruzioni dettagliate su come creare un progetto di pagine, vedere [Introduzione alle Razor pagine](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="40206-121">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="razor-pages"></a>Razor<span data-ttu-id="40206-122">Pagine</span><span class="sxs-lookup"><span data-stu-id="40206-122"> Pages</span></span>

Razor<span data-ttu-id="40206-123">Le pagine sono abilitate in *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="40206-123"> Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="40206-124">Si consideri una pagina di base: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="40206-124">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="40206-125">Il codice precedente ha un aspetto molto simile a quello di un [ Razor file di visualizzazione](xref:tutorials/first-mvc-app/adding-view) usato in un'app ASP.NET Core con i controller e le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="40206-125">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="40206-126">Ciò che lo rende diverso è la [`@page`](xref:mvc/views/razor#page) direttiva.</span><span class="sxs-lookup"><span data-stu-id="40206-126">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="40206-127">`@page` trasforma il file in un'azione MVC, ovvero gestisce le richieste direttamente, senza passare attraverso un controller.</span><span class="sxs-lookup"><span data-stu-id="40206-127">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="40206-128">`@page`deve essere la prima Razor direttiva in una pagina.</span><span class="sxs-lookup"><span data-stu-id="40206-128">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="40206-129">`@page`influiscono sul comportamento di altri [Razor](xref:mvc/views/razor) costrutti.</span><span class="sxs-lookup"><span data-stu-id="40206-129">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> Razor<span data-ttu-id="40206-130">I nomi file delle pagine hanno un suffisso *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="40206-130"> Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="40206-131">Nei due file seguenti viene visualizzata una pagina simile che usa una classe `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="40206-131">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="40206-132">Il file *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="40206-132">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="40206-133">Il modello di pagina *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="40206-133">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="40206-134">Per convenzione, il `PageModel` file di classe ha lo stesso nome del Razor file di paging con l'aggiunta di *. cs* .</span><span class="sxs-lookup"><span data-stu-id="40206-134">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="40206-135">La pagina precedente, ad esempio, Razor è *pages/index2. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="40206-135">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="40206-136">Il file che contiene la classe `PageModel` è denominato *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="40206-136">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="40206-137">Le associazioni dei percorsi URL alle pagine sono determinate dalla posizione della pagina nel file system.</span><span class="sxs-lookup"><span data-stu-id="40206-137">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="40206-138">La tabella seguente mostra un Razor percorso di pagina e l'URL corrispondente:</span><span class="sxs-lookup"><span data-stu-id="40206-138">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="40206-139">Percorso e nome file</span><span class="sxs-lookup"><span data-stu-id="40206-139">File name and path</span></span>               | <span data-ttu-id="40206-140">URL corrispondente</span><span class="sxs-lookup"><span data-stu-id="40206-140">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="40206-141">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="40206-141">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="40206-142">`/` o `/Index`</span><span class="sxs-lookup"><span data-stu-id="40206-142">`/` or `/Index`</span></span> |
| <span data-ttu-id="40206-143">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="40206-143">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="40206-144">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="40206-144">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="40206-145">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="40206-145">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="40206-146">`/Store` o `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="40206-146">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="40206-147">Note:</span><span class="sxs-lookup"><span data-stu-id="40206-147">Notes:</span></span>

* <span data-ttu-id="40206-148">Per Razor impostazione predefinita, il runtime cerca i file di pagine nella cartella *pages* .</span><span class="sxs-lookup"><span data-stu-id="40206-148">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="40206-149">`Index` è la pagina predefinita quando un URL non include una pagina.</span><span class="sxs-lookup"><span data-stu-id="40206-149">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="40206-150">Scrivere un form di base</span><span class="sxs-lookup"><span data-stu-id="40206-150">Write a basic form</span></span>

Razor<span data-ttu-id="40206-151">Pagine è progettato per semplificare l'implementazione dei modelli comuni usati con i Web browser quando si compila un'app.</span><span class="sxs-lookup"><span data-stu-id="40206-151"> Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="40206-152">L' [associazione di modelli](xref:mvc/models/model-binding), gli [Helper Tag](xref:mvc/views/tag-helpers/intro)e gli helper HTML *funzionano solo* con le proprietà definite in una Razor classe di pagine.</span><span class="sxs-lookup"><span data-stu-id="40206-152">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="40206-153">Si consideri una pagina che implementa un form "contact us" di base per il modello `Contact`:</span><span class="sxs-lookup"><span data-stu-id="40206-153">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="40206-154">Per gli esempi riportati in questo documento, `DbContext` viene inizializzato nel file [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24).</span><span class="sxs-lookup"><span data-stu-id="40206-154">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="40206-155">Il modello di dati:</span><span class="sxs-lookup"><span data-stu-id="40206-155">The data model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="40206-156">Il contesto del database:</span><span class="sxs-lookup"><span data-stu-id="40206-156">The db context:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="40206-157">Il file di visualizzazione *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="40206-157">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="40206-158">Il modello di pagina *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="40206-158">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="40206-159">Per convenzione, la classe `PageModel` è denominata `<PageName>Model` e si trova nello stesso spazio dei nomi della pagina.</span><span class="sxs-lookup"><span data-stu-id="40206-159">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="40206-160">La classe `PageModel` consente la separazione della logica di una pagina dalla relativa presentazione.</span><span class="sxs-lookup"><span data-stu-id="40206-160">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="40206-161">Definisce i gestori di pagina per le richieste inviate alla pagina e i dati usati per il rendering della pagina.</span><span class="sxs-lookup"><span data-stu-id="40206-161">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="40206-162">Questa separazione consente:</span><span class="sxs-lookup"><span data-stu-id="40206-162">This separation allows:</span></span>

* <span data-ttu-id="40206-163">Gestione delle dipendenze di pagina tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="40206-163">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="40206-164">Testing unità</span><span class="sxs-lookup"><span data-stu-id="40206-164">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="40206-165">La pagina contiene un oggetto , ovvero un `OnPostAsync` *metodo gestore* che viene eseguito per le richieste `POST`, quando un utente invia il form.</span><span class="sxs-lookup"><span data-stu-id="40206-165">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="40206-166">È possibile aggiungere i metodi del gestore per qualsiasi verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="40206-166">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="40206-167">I gestori più comuni sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="40206-167">The most common handlers are:</span></span>

* <span data-ttu-id="40206-168">`OnGet` per inizializzare lo stato necessario per la pagina.</span><span class="sxs-lookup"><span data-stu-id="40206-168">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="40206-169">Nel codice precedente, il `OnGet` Metodo Visualizza la pagina *CreateModel. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="40206-169">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="40206-170">`OnPost` per gestire gli invii di form.</span><span class="sxs-lookup"><span data-stu-id="40206-170">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="40206-171">Il suffisso `Async` nel nome è facoltativo, ma viene spesso usato per convenzione per le funzioni asincrone.</span><span class="sxs-lookup"><span data-stu-id="40206-171">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="40206-172">Il codice precedente è tipico per le Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="40206-172">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="40206-173">Se si ha familiarità con le app ASP.NET che usano i controller e le visualizzazioni:</span><span class="sxs-lookup"><span data-stu-id="40206-173">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="40206-174">Il `OnPostAsync` codice nell'esempio precedente ha un aspetto simile al codice del controller tipico.</span><span class="sxs-lookup"><span data-stu-id="40206-174">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="40206-175">La maggior parte delle primitive MVC come l' [associazione di modelli](xref:mvc/models/model-binding), la [convalida](xref:mvc/models/validation)e i risultati dell'azione funzionano allo stesso modo con i controller e le Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="40206-175">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="40206-176">Il metodo `OnPostAsync` precedente:</span><span class="sxs-lookup"><span data-stu-id="40206-176">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="40206-177">Il flusso di base di `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="40206-177">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="40206-178">Verificare se sono presenti errori di convalida.</span><span class="sxs-lookup"><span data-stu-id="40206-178">Check for validation errors.</span></span>

* <span data-ttu-id="40206-179">Se non sono presenti errori, salvare i dati e reindirizzare.</span><span class="sxs-lookup"><span data-stu-id="40206-179">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="40206-180">Se sono presenti errori, visualizzare di nuovo la pagina con i messaggi di convalida.</span><span class="sxs-lookup"><span data-stu-id="40206-180">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="40206-181">In molti casi, gli errori di convalida vengono rilevati nel client e non vengono mai inviati al server.</span><span class="sxs-lookup"><span data-stu-id="40206-181">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="40206-182">Il file di visualizzazione *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="40206-182">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="40206-183">Il codice HTML sottoposto a rendering da *pages/create. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="40206-183">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="40206-184">Nel codice precedente, inserendo il modulo:</span><span class="sxs-lookup"><span data-stu-id="40206-184">In the previous code, posting the form:</span></span>

* <span data-ttu-id="40206-185">Con dati validi:</span><span class="sxs-lookup"><span data-stu-id="40206-185">With valid data:</span></span>

  * <span data-ttu-id="40206-186">Il `OnPostAsync` metodo del gestore chiama il <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> metodo helper.</span><span class="sxs-lookup"><span data-stu-id="40206-186">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="40206-187">`RedirectToPage` restituisce un'istanza di <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span><span class="sxs-lookup"><span data-stu-id="40206-187">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="40206-188">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="40206-188">`RedirectToPage`:</span></span>

    * <span data-ttu-id="40206-189">Risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="40206-189">Is an action result.</span></span>
    * <span data-ttu-id="40206-190">È simile a `RedirectToAction` o `RedirectToRoute` (usato nei controller e nelle viste).</span><span class="sxs-lookup"><span data-stu-id="40206-190">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="40206-191">È personalizzato per le pagine.</span><span class="sxs-lookup"><span data-stu-id="40206-191">Is customized for pages.</span></span> <span data-ttu-id="40206-192">Nell'esempio precedente viene reindirizzato alla pagina di indice radice (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="40206-192">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="40206-193">`RedirectToPage`è descritto in dettaglio nella sezione [generazione URL per pagine](#url_gen) .</span><span class="sxs-lookup"><span data-stu-id="40206-193">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="40206-194">Con gli errori di convalida passati al server:</span><span class="sxs-lookup"><span data-stu-id="40206-194">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="40206-195">Il `OnPostAsync` metodo del gestore chiama il <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> metodo helper.</span><span class="sxs-lookup"><span data-stu-id="40206-195">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="40206-196">`Page` restituisce un'istanza di <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span><span class="sxs-lookup"><span data-stu-id="40206-196">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="40206-197">La restituzione di `Page` è simile al modo in cui le azioni nel controller restituiscono `View`.</span><span class="sxs-lookup"><span data-stu-id="40206-197">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="40206-198">`PageResult`è il tipo restituito predefinito per un metodo del gestore.</span><span class="sxs-lookup"><span data-stu-id="40206-198">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="40206-199">Un metodo gestore che restituisce `void` esegue il rendering della pagina.</span><span class="sxs-lookup"><span data-stu-id="40206-199">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="40206-200">Nell'esempio precedente, se si pubblica il form senza alcun valore, in [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) viene restituito false.</span><span class="sxs-lookup"><span data-stu-id="40206-200">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="40206-201">In questo esempio non vengono visualizzati errori di convalida nel client.</span><span class="sxs-lookup"><span data-stu-id="40206-201">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="40206-202">Il controllo degli errori di convalida viene trattato più avanti in questo documento.</span><span class="sxs-lookup"><span data-stu-id="40206-202">Validation error handing is covered later in this document.</span></span>

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="40206-203">Con errori di convalida rilevati dalla convalida lato client:</span><span class="sxs-lookup"><span data-stu-id="40206-203">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="40206-204">I dati **non** vengono inviati al server.</span><span class="sxs-lookup"><span data-stu-id="40206-204">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="40206-205">La convalida lato client è illustrata più avanti in questo documento.</span><span class="sxs-lookup"><span data-stu-id="40206-205">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="40206-206">La `Customer` Proprietà usa l' [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attributo per acconsentire esplicitamente all'associazione di modelli:</span><span class="sxs-lookup"><span data-stu-id="40206-206">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="40206-207">`[BindProperty]`**non** deve essere utilizzato nei modelli contenenti proprietà che non devono essere modificate dal client.</span><span class="sxs-lookup"><span data-stu-id="40206-207">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="40206-208">Per ulteriori informazioni, vedere [overposting](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="40206-208">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

Razor<span data-ttu-id="40206-209">Le pagine, per impostazione predefinita, associano proprietà solo con non `GET` verbi.</span><span class="sxs-lookup"><span data-stu-id="40206-209"> Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="40206-210">L'associazione alle proprietà Elimina la necessità di scrivere codice per convertire i dati HTTP nel tipo di modello.</span><span class="sxs-lookup"><span data-stu-id="40206-210">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="40206-211">Riduce il codice usando la stessa proprietà per il eseguire il rendering dei campi del form (`<input asp-for="Customer.Name">`) e accettare l'input.</span><span class="sxs-lookup"><span data-stu-id="40206-211">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="40206-212">Esaminando il file di visualizzazione *pages/create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="40206-212">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="40206-213">Nel codice precedente, l' [Helper Tag](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` di input associa l' `<input>` elemento HTML all'espressione del `Customer.Name` modello.</span><span class="sxs-lookup"><span data-stu-id="40206-213">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="40206-214">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available)rende disponibili gli helper tag.</span><span class="sxs-lookup"><span data-stu-id="40206-214">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="40206-215">La home page</span><span class="sxs-lookup"><span data-stu-id="40206-215">The home page</span></span>

<span data-ttu-id="40206-216">*Index. cshtml* è il Home page:</span><span class="sxs-lookup"><span data-stu-id="40206-216">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="40206-217">La classe `PageModel` associata (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="40206-217">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="40206-218">Il file *index. cshtml* contiene il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="40206-218">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="40206-219">L' `<a /a>` [Helper tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) ha usato l' `asp-route-{value}` attributo per generare un collegamento alla pagina di modifica.</span><span class="sxs-lookup"><span data-stu-id="40206-219">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="40206-220">Il collegamento contiene i dati della route con l'ID contatto.</span><span class="sxs-lookup"><span data-stu-id="40206-220">The link contains route data with the contact ID.</span></span> <span data-ttu-id="40206-221">Ad esempio: `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="40206-221">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="40206-222">Gli [Helper Tag](xref:mvc/views/tag-helpers/intro) consentono al codice lato server di partecipare alla creazione e al rendering di elementi HTML nei Razor file.</span><span class="sxs-lookup"><span data-stu-id="40206-222">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="40206-223">Il file *index. cshtml* contiene il markup per la creazione di un pulsante Delete per ogni contatto del cliente:</span><span class="sxs-lookup"><span data-stu-id="40206-223">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="40206-224">HTML sottoposto a rendering:</span><span class="sxs-lookup"><span data-stu-id="40206-224">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="40206-225">Quando il pulsante Elimina viene sottoposto a rendering in HTML, il relativo [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) include i parametri per:</span><span class="sxs-lookup"><span data-stu-id="40206-225">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="40206-226">ID contatto del cliente, specificato dall' `asp-route-id` attributo.</span><span class="sxs-lookup"><span data-stu-id="40206-226">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="40206-227">Oggetto `handler` , specificato dall' `asp-page-handler` attributo.</span><span class="sxs-lookup"><span data-stu-id="40206-227">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="40206-228">Quando il pulsante è selezionato, viene inviata una richiesta `POST` di modulo al server.</span><span class="sxs-lookup"><span data-stu-id="40206-228">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="40206-229">Per convenzione, il nome del metodo del gestore viene selezionato in base al valore del parametro `handler` in base allo schema `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="40206-229">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="40206-230">Poiché in questo esempio l'`handler` è `delete`, il metodo `OnPostDeleteAsync` viene usato per elaborare la richiesta `POST`.</span><span class="sxs-lookup"><span data-stu-id="40206-230">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="40206-231">Se `asp-page-handler` viene impostato su un valore diverso, ad esempio `remove`, viene selezionato un metodo gestore con il nome `OnPostRemoveAsync`.</span><span class="sxs-lookup"><span data-stu-id="40206-231">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="40206-232">Il metodo `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="40206-232">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="40206-233">Ottiene `id` dalla stringa di query.</span><span class="sxs-lookup"><span data-stu-id="40206-233">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="40206-234">Interroga il database in merito al contatto del cliente con `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="40206-234">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="40206-235">Se il contatto del cliente viene trovato, viene rimosso e il database viene aggiornato.</span><span class="sxs-lookup"><span data-stu-id="40206-235">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="40206-236">Chiama <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> per reindirizzare alla pagina di indice radice (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="40206-236">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="40206-237">File Edit. cshtml</span><span class="sxs-lookup"><span data-stu-id="40206-237">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="40206-238">La prima riga contiene la direttiva `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="40206-238">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="40206-239">Il vincolo di routing `"{id:int}"` indica alla pagina di accettare le richieste inviate alla pagina che contengono i dati della route `int`.</span><span class="sxs-lookup"><span data-stu-id="40206-239">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="40206-240">Se una richiesta inviata alla pagina non contiene dati della route che possono essere convertiti in un oggetto `int`, il runtime restituisce un errore HTTP 404 (non trovato).</span><span class="sxs-lookup"><span data-stu-id="40206-240">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="40206-241">Per rendere l'ID facoltativo, aggiungere `?` al vincolo di route:</span><span class="sxs-lookup"><span data-stu-id="40206-241">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="40206-242">Il file *Edit.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="40206-242">The *Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="40206-243">Convalida</span><span class="sxs-lookup"><span data-stu-id="40206-243">Validation</span></span>

<span data-ttu-id="40206-244">Regole di convalida:</span><span class="sxs-lookup"><span data-stu-id="40206-244">Validation rules:</span></span>

* <span data-ttu-id="40206-245">Sono specificati in modo dichiarativo nella classe del modello.</span><span class="sxs-lookup"><span data-stu-id="40206-245">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="40206-246">Vengono applicati ovunque nell'app.</span><span class="sxs-lookup"><span data-stu-id="40206-246">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="40206-247">Lo <xref:System.ComponentModel.DataAnnotations> spazio dei nomi fornisce un set di attributi di convalida predefiniti che vengono applicati in modo dichiarativo a una classe o a una proprietà.</span><span class="sxs-lookup"><span data-stu-id="40206-247">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="40206-248">DataAnnotations contiene anche attributi di formattazione come [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) questa guida alla formattazione e non forniscono alcuna convalida.</span><span class="sxs-lookup"><span data-stu-id="40206-248">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="40206-249">Si consideri il `Customer` modello:</span><span class="sxs-lookup"><span data-stu-id="40206-249">Consider the `Customer` model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="40206-250">Utilizzando il seguente file di visualizzazione *create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="40206-250">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="40206-251">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="40206-251">The preceding code:</span></span>

* <span data-ttu-id="40206-252">Include gli script di convalida jQuery e jQuery.</span><span class="sxs-lookup"><span data-stu-id="40206-252">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="40206-253">USA gli `<div />` `<span />` [Helper Tag](xref:mvc/views/tag-helpers/intro) e per abilitare:</span><span class="sxs-lookup"><span data-stu-id="40206-253">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="40206-254">Convalida lato client.</span><span class="sxs-lookup"><span data-stu-id="40206-254">Client-side validation.</span></span>
  * <span data-ttu-id="40206-255">Rendering degli errori di convalida.</span><span class="sxs-lookup"><span data-stu-id="40206-255">Validation error rendering.</span></span>

* <span data-ttu-id="40206-256">Viene generato il codice HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="40206-256">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="40206-257">Se si pubblica il modulo di creazione senza un valore di nome, viene visualizzato il messaggio di errore "il campo nome è obbligatorio".</span><span class="sxs-lookup"><span data-stu-id="40206-257">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="40206-258">nel form.</span><span class="sxs-lookup"><span data-stu-id="40206-258">on the form.</span></span> <span data-ttu-id="40206-259">Se JavaScript è abilitato nel client, il browser Visualizza l'errore senza inviare al server.</span><span class="sxs-lookup"><span data-stu-id="40206-259">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="40206-260">L' `[StringLength(10)]` attributo genera `data-val-length-max="10"` sull'HTML sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="40206-260">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="40206-261">`data-val-length-max`impedisce ai browser di immettere più della lunghezza massima specificata.</span><span class="sxs-lookup"><span data-stu-id="40206-261">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="40206-262">Se viene usato uno strumento come [Fiddler](https://www.telerik.com/fiddler) per modificare e riprodurre il post:</span><span class="sxs-lookup"><span data-stu-id="40206-262">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="40206-263">Con il nome più lungo di 10.</span><span class="sxs-lookup"><span data-stu-id="40206-263">With the name longer than 10.</span></span>
* <span data-ttu-id="40206-264">Il messaggio di errore "il nome del campo deve essere una stringa con una lunghezza massima di 10".</span><span class="sxs-lookup"><span data-stu-id="40206-264">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="40206-265">un errore imprevisto".</span><span class="sxs-lookup"><span data-stu-id="40206-265">is returned.</span></span>

<span data-ttu-id="40206-266">Si consideri il `Movie` modello seguente:</span><span class="sxs-lookup"><span data-stu-id="40206-266">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="40206-267">Gli attributi di convalida specificano il comportamento da applicare alle proprietà del modello a cui sono applicati:</span><span class="sxs-lookup"><span data-stu-id="40206-267">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="40206-268">Gli `Required` `MinimumLength` attributi e indicano che una proprietà deve avere un valore, ma nulla impedisce a un utente di immettere spazi vuoti per soddisfare la convalida.</span><span class="sxs-lookup"><span data-stu-id="40206-268">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="40206-269">L'attributo `RegularExpression` viene usato per limitare i caratteri che possono essere inseriti.</span><span class="sxs-lookup"><span data-stu-id="40206-269">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="40206-270">Nel codice precedente "Genre":</span><span class="sxs-lookup"><span data-stu-id="40206-270">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="40206-271">Deve includere solo lettere.</span><span class="sxs-lookup"><span data-stu-id="40206-271">Must only use letters.</span></span>
  * <span data-ttu-id="40206-272">La prima lettera deve essere maiuscola.</span><span class="sxs-lookup"><span data-stu-id="40206-272">The first letter is required to be uppercase.</span></span> <span data-ttu-id="40206-273">Gli spazi, i numeri e i caratteri speciali non sono consentiti.</span><span class="sxs-lookup"><span data-stu-id="40206-273">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="40206-274">`RegularExpression` "Rating":</span><span class="sxs-lookup"><span data-stu-id="40206-274">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="40206-275">Richiede che il primo carattere sia una lettera maiuscola.</span><span class="sxs-lookup"><span data-stu-id="40206-275">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="40206-276">Consente i caratteri speciali e i numeri negli spazi successivi.</span><span class="sxs-lookup"><span data-stu-id="40206-276">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="40206-277">"PG-13" è valido per una classificazione, ma non per "Genre".</span><span class="sxs-lookup"><span data-stu-id="40206-277">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="40206-278">L'attributo `Range` vincola un valore all'interno di un intervallo specificato.</span><span class="sxs-lookup"><span data-stu-id="40206-278">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="40206-279">L' `StringLength` attributo imposta la lunghezza massima di una proprietà di stringa e, facoltativamente, la lunghezza minima.</span><span class="sxs-lookup"><span data-stu-id="40206-279">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="40206-280">I tipi di valore, ad esempio `decimal`, `int`, `float` e `DateTime`, sono intrinsecamente necessari e non richiedono l'attributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="40206-280">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="40206-281">Nella pagina Crea per il `Movie` modello vengono visualizzati gli errori con valori non validi:</span><span class="sxs-lookup"><span data-stu-id="40206-281">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Il modulo di vista del film con più errori di convalida del lato client jQuery](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="40206-283">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="40206-283">For more information, see:</span></span>

* [<span data-ttu-id="40206-284">Aggiungere la convalida all'app Movie</span><span class="sxs-lookup"><span data-stu-id="40206-284">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="40206-285">[Convalida del modello in ASP.NET Core](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="40206-285">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="40206-286">Gestire le richieste HEAD con un fallback del gestore OnGet</span><span class="sxs-lookup"><span data-stu-id="40206-286">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="40206-287">`HEAD`le richieste consentono di recuperare le intestazioni per una risorsa specifica.</span><span class="sxs-lookup"><span data-stu-id="40206-287">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="40206-288">A differenza delle richieste `GET`, le richieste `HEAD` non restituiscono un corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="40206-288">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="40206-289">In genere, per le richieste `HEAD` viene creato e chiamato un gestore `OnHead`:</span><span class="sxs-lookup"><span data-stu-id="40206-289">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

Razor<span data-ttu-id="40206-290">Le pagine eseguono il fallback alla chiamata del `OnGet` gestore se non `OnHead` è stato definito alcun gestore.</span><span class="sxs-lookup"><span data-stu-id="40206-290"> Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="40206-291">XSRF/CSRF e Razor pagine</span><span class="sxs-lookup"><span data-stu-id="40206-291">XSRF/CSRF and Razor Pages</span></span>

Razor<span data-ttu-id="40206-292">Le pagine sono protette dalla [convalida antifalsificazione](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="40206-292"> Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="40206-293">[FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) inserisce i token antifalsificazione negli elementi del form HTML.</span><span class="sxs-lookup"><span data-stu-id="40206-293">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="40206-294">Uso di layout, parti parziali, modelli e helper tag con Razor pagine</span><span class="sxs-lookup"><span data-stu-id="40206-294">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="40206-295">Le pagine funzionano con tutte le funzionalità del Razor motore di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="40206-295">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="40206-296">Layout, parti parziali, modelli, helper tag, *_ViewStart. cshtml*e *_ViewImports. cshtml* funzionano allo stesso modo per le Razor visualizzazioni convenzionali.</span><span class="sxs-lookup"><span data-stu-id="40206-296">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="40206-297">La pagina verrà ora riorganizzata in modo da usufruire di alcune di queste funzionalità.</span><span class="sxs-lookup"><span data-stu-id="40206-297">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="40206-298">Aggiungere una [pagina di layout](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="40206-298">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="40206-299">Il [layout](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="40206-299">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="40206-300">Controlla il layout di ogni pagina, a meno che la pagina non accetti il layout.</span><span class="sxs-lookup"><span data-stu-id="40206-300">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="40206-301">Importa le strutture HTML, ad esempio JavaScript e i fogli di stile.</span><span class="sxs-lookup"><span data-stu-id="40206-301">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="40206-302">Viene eseguito il rendering del contenuto della Razor pagina dove `@RenderBody()` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="40206-302">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="40206-303">Per ulteriori informazioni, vedere [pagina layout](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="40206-303">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="40206-304">La proprietà [Layout](xref:mvc/views/layout#specifying-a-layout) proprietà viene impostata in *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="40206-304">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="40206-305">Il layout è nella cartella *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="40206-305">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="40206-306">Le pagine cercano altre visualizzazioni (layout, modelli, righe parzialmente eseguite) in modo gerarchico, partendo dalla stessa cartella della pagina corrente.</span><span class="sxs-lookup"><span data-stu-id="40206-306">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="40206-307">Un layout nella cartella pages */Shared* può essere utilizzato da qualsiasi Razor pagina nella cartella *pages* .</span><span class="sxs-lookup"><span data-stu-id="40206-307">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="40206-308">Il file di layout dovrebbe andare nella cartella *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="40206-308">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="40206-309">Si consiglia di **non** inserire il file di layout nella cartella *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="40206-309">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="40206-310">*Views/Shared* è un modello destinato alle visualizzazioni MVC.</span><span class="sxs-lookup"><span data-stu-id="40206-310">*Views/Shared* is an MVC views pattern.</span></span> Razor<span data-ttu-id="40206-311">Le pagine hanno lo scopo di basarsi sulla gerarchia di cartelle, non sulle convenzioni di percorso.</span><span class="sxs-lookup"><span data-stu-id="40206-311"> Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="40206-312">Visualizza la ricerca da una Razor pagina include la cartella *pagine* .</span><span class="sxs-lookup"><span data-stu-id="40206-312">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="40206-313">I layout, i modelli e i parziali utilizzati con i controller MVC e le Razor visualizzazioni convenzionali *funzionano semplicemente*.</span><span class="sxs-lookup"><span data-stu-id="40206-313">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="40206-314">Aggiungere un file *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="40206-314">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="40206-315">`@namespace` viene spiegato in seguito nell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="40206-315">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="40206-316">La direttiva `@addTagHelper` inserisce gli [helper tag predefiniti](xref:mvc/views/tag-helpers/builtin-th/Index) in tutte le pagine presenti nella cartella *Pages*.</span><span class="sxs-lookup"><span data-stu-id="40206-316">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="40206-317">La `@namespace` direttiva impostata in una pagina:</span><span class="sxs-lookup"><span data-stu-id="40206-317">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="40206-318">La `@namespace` direttiva imposta lo spazio dei nomi per la pagina.</span><span class="sxs-lookup"><span data-stu-id="40206-318">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="40206-319">La direttiva `@model` non deve necessariamente includere lo spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="40206-319">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="40206-320">Se la direttiva `@namespace` è contenuta in *_ViewImports.cshtml*, lo spazio dei nomi specificato indica il prefisso per lo spazio dei nomi generato nella pagina che importa la direttiva `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="40206-320">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="40206-321">Il resto dello spazio dei nomi generato (la parte del suffisso) è il percorso relativo separato dal punto tra la cartella contenente *_Viewimports.cshtml* e la cartella contenente la pagina.</span><span class="sxs-lookup"><span data-stu-id="40206-321">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="40206-322">Ad esempio, la classe `PageModel`*Pages/Customers/Edit.cshtml.cs* imposta in modo esplicito lo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="40206-322">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="40206-323">Il file *Pages/_ViewImports.cshtml* file imposta il seguente spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="40206-323">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="40206-324">Lo spazio dei nomi generato per la pagina *pages/Customers/Edit. cshtml* Razor è uguale a quello della `PageModel` classe.</span><span class="sxs-lookup"><span data-stu-id="40206-324">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="40206-325">`@namespace`*funziona anche con le Razor visualizzazioni convenzionali.*</span><span class="sxs-lookup"><span data-stu-id="40206-325">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="40206-326">Si consideri il file di visualizzazione *pages/create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="40206-326">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="40206-327">Il file di visualizzazione *pages/create. cshtml* aggiornato con *_ViewImports. cshtml* e il file di layout precedente:</span><span class="sxs-lookup"><span data-stu-id="40206-327">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="40206-328">Nel codice precedente, il *_ViewImports. cshtml* ha importato lo spazio dei nomi e gli helper tag.</span><span class="sxs-lookup"><span data-stu-id="40206-328">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="40206-329">Il file di layout ha importato i file JavaScript.</span><span class="sxs-lookup"><span data-stu-id="40206-329">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="40206-330">Il [ Razor progetto di avvio pagine](#rpvs17) contiene le *pagine/_ValidationScriptsPartial. cshtml*, che consente di associare la convalida lato client.</span><span class="sxs-lookup"><span data-stu-id="40206-330">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="40206-331">Per altre informazioni sulle visualizzazioni parziali, vedere <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="40206-331">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="40206-332">Generazione di URL per le pagine</span><span class="sxs-lookup"><span data-stu-id="40206-332">URL generation for Pages</span></span>

<span data-ttu-id="40206-333">La pagina `Create`, riportata in precedenza, usa `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="40206-333">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="40206-334">L'applicazione ha la struttura di file o cartella seguente:</span><span class="sxs-lookup"><span data-stu-id="40206-334">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="40206-335">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="40206-335">*/Pages*</span></span>

  * <span data-ttu-id="40206-336">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="40206-336">*Index.cshtml*</span></span>
  * <span data-ttu-id="40206-337">*Privacy. cshtml*</span><span class="sxs-lookup"><span data-stu-id="40206-337">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="40206-338">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="40206-338">*/Customers*</span></span>

    * <span data-ttu-id="40206-339">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="40206-339">*Create.cshtml*</span></span>
    * <span data-ttu-id="40206-340">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="40206-340">*Edit.cshtml*</span></span>
    * <span data-ttu-id="40206-341">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="40206-341">*Index.cshtml*</span></span>

<span data-ttu-id="40206-342">Le pagine *pages/Customers/create. cshtml* e *pages/Customers/Edit. cshtml* reindirizza a *pages/Customers/index. cshtml* dopo l'esito positivo.</span><span class="sxs-lookup"><span data-stu-id="40206-342">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="40206-343">La stringa `./Index` è un nome di pagina relativo usato per accedere alla pagina precedente.</span><span class="sxs-lookup"><span data-stu-id="40206-343">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="40206-344">Viene usato per generare gli URL nella pagina *pages/Customers/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="40206-344">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="40206-345">ad esempio:</span><span class="sxs-lookup"><span data-stu-id="40206-345">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="40206-346">Il nome della pagina assoluta `/Index` viene usato per generare gli URL nella pagina *pages/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="40206-346">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="40206-347">ad esempio:</span><span class="sxs-lookup"><span data-stu-id="40206-347">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="40206-348">Il nome della pagina è il percorso alla pagina dalla cartella radice */Pages*, inclusa una barra iniziale `/`, ad esempio `/Index`.</span><span class="sxs-lookup"><span data-stu-id="40206-348">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="40206-349">Gli esempi di generazione di URL precedenti offrono opzioni avanzate e funzionalità funzionali rispetto a un URL a livello di codice.</span><span class="sxs-lookup"><span data-stu-id="40206-349">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="40206-350">La generazione di URL usa il [routing](xref:mvc/controllers/routing) ed è in grado di generare e codificare i parametri in base al modo in cui la route è definita nel percorso di destinazione.</span><span class="sxs-lookup"><span data-stu-id="40206-350">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="40206-351">La generazione di URL per le pagine supporta i nomi relativi.</span><span class="sxs-lookup"><span data-stu-id="40206-351">URL generation for pages supports relative names.</span></span> <span data-ttu-id="40206-352">Nella tabella seguente viene illustrata la pagina di indice selezionata utilizzando `RedirectToPage` parametri diversi in *pages/Customers/create. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="40206-352">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="40206-353">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="40206-353">RedirectToPage(x)</span></span>| <span data-ttu-id="40206-354">Page</span><span class="sxs-lookup"><span data-stu-id="40206-354">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="40206-355">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="40206-355">RedirectToPage("/Index")</span></span> | <span data-ttu-id="40206-356">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="40206-356">*Pages/Index*</span></span> |
| <span data-ttu-id="40206-357">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="40206-357">RedirectToPage("./Index");</span></span> | <span data-ttu-id="40206-358">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="40206-358">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="40206-359">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="40206-359">RedirectToPage("../Index")</span></span> | <span data-ttu-id="40206-360">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="40206-360">*Pages/Index*</span></span> |
| <span data-ttu-id="40206-361">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="40206-361">RedirectToPage("Index")</span></span>  | <span data-ttu-id="40206-362">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="40206-362">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="40206-363">`RedirectToPage("Index")`, `RedirectToPage("./Index")` e `RedirectToPage("../Index")` sono *nomi relativi*.</span><span class="sxs-lookup"><span data-stu-id="40206-363">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="40206-364">Il parametro `RedirectToPage` è *combinato* con il percorso della pagina corrente per calcolare il nome della pagina di destinazione.</span><span class="sxs-lookup"><span data-stu-id="40206-364">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="40206-365">Il collegamento dei nomi relativi è utile quando si compilano siti con una struttura complessa.</span><span class="sxs-lookup"><span data-stu-id="40206-365">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="40206-366">Quando vengono usati nomi relativi per il collegamento tra le pagine in una cartella:</span><span class="sxs-lookup"><span data-stu-id="40206-366">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="40206-367">La ridenominazione di una cartella non interrompe i collegamenti relativi.</span><span class="sxs-lookup"><span data-stu-id="40206-367">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="40206-368">I collegamenti non vengono interrotti perché non includono il nome della cartella.</span><span class="sxs-lookup"><span data-stu-id="40206-368">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="40206-369">Per reindirizzare la pagina a un'[Area](xref:mvc/controllers/areas) diversa, specificare l'area:</span><span class="sxs-lookup"><span data-stu-id="40206-369">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="40206-370">Per altre informazioni, vedere <xref:mvc/controllers/areas> e <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="40206-370">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="40206-371">Attributo viewData</span><span class="sxs-lookup"><span data-stu-id="40206-371">ViewData attribute</span></span>

<span data-ttu-id="40206-372">I dati possono essere passati a una pagina con <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> .</span><span class="sxs-lookup"><span data-stu-id="40206-372">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="40206-373">Le proprietà con l' `[ViewData]` attributo hanno i valori archiviati e caricati da <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> .</span><span class="sxs-lookup"><span data-stu-id="40206-373">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="40206-374">Nell'esempio seguente, `AboutModel` applica l' `[ViewData]` attributo alla `Title` proprietà:</span><span class="sxs-lookup"><span data-stu-id="40206-374">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

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

<span data-ttu-id="40206-375">Nella pagina About (Informazioni) accedere alla proprietà `Title` come proprietà del modello:</span><span class="sxs-lookup"><span data-stu-id="40206-375">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="40206-376">Nel layout il titolo viene letto dal dizionario ViewData:</span><span class="sxs-lookup"><span data-stu-id="40206-376">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="40206-377">TempData</span><span class="sxs-lookup"><span data-stu-id="40206-377">TempData</span></span>

<span data-ttu-id="40206-378">ASP.NET Core espone <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> .</span><span class="sxs-lookup"><span data-stu-id="40206-378">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="40206-379">Questa proprietà archivia i dati finché non viene letta.</span><span class="sxs-lookup"><span data-stu-id="40206-379">This property stores data until it's read.</span></span> <span data-ttu-id="40206-380">I metodi <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> e <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> possono essere usati per esaminare i dati senza eliminazione.</span><span class="sxs-lookup"><span data-stu-id="40206-380">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="40206-381">`TempData`è utile per il reindirizzamento, quando i dati sono necessari per più di una singola richiesta.</span><span class="sxs-lookup"><span data-stu-id="40206-381">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="40206-382">Il codice seguente imposta il valore di `Message` usando `TempData`:</span><span class="sxs-lookup"><span data-stu-id="40206-382">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="40206-383">Il markup seguente nel file *Pages/Customers/Index.cshtml* visualizza il valore di `Message` usando `TempData`.</span><span class="sxs-lookup"><span data-stu-id="40206-383">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="40206-384">Il modello di pagina *Pages/Customers/Index.cshtml.cs* applica l'attributo `[TempData]` alla proprietà `Message`.</span><span class="sxs-lookup"><span data-stu-id="40206-384">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="40206-385">Per ulteriori informazioni, vedere [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="40206-385">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="40206-386">Più gestori per pagina</span><span class="sxs-lookup"><span data-stu-id="40206-386">Multiple handlers per page</span></span>

<span data-ttu-id="40206-387">La pagina seguente genera markup per due gestori usando l'helper tag `asp-page-handler`:</span><span class="sxs-lookup"><span data-stu-id="40206-387">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="40206-388">Il form nell'esempio precedente contiene due pulsanti di invio, ognuno dei quali usa `FormActionTagHelper` per l'invio a un URL diverso.</span><span class="sxs-lookup"><span data-stu-id="40206-388">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="40206-389">L'attributo `asp-page-handler` è correlato a `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="40206-389">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="40206-390">`asp-page-handler` genera gli URL che indirizzano a ognuno dei metodi gestore definiti da una pagina.</span><span class="sxs-lookup"><span data-stu-id="40206-390">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="40206-391">`asp-page` non è specificato poiché l'esempio è collegato alla pagina corrente.</span><span class="sxs-lookup"><span data-stu-id="40206-391">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="40206-392">Il modello di pagina:</span><span class="sxs-lookup"><span data-stu-id="40206-392">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="40206-393">Il codice precedente usa *metodi gestore denominati*.</span><span class="sxs-lookup"><span data-stu-id="40206-393">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="40206-394">I metodi gestore denominati vengono creati usando il testo che nel nome segue `On<HTTP Verb>` e precede `Async` (se presente).</span><span class="sxs-lookup"><span data-stu-id="40206-394">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="40206-395">Nell'esempio precedente i metodi della pagina sono OnPost**JoinList**Async e OnPost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="40206-395">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="40206-396">Rimuovendo *OnPost* e *Async*, i nomi dei gestori sono `JoinList` e `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="40206-396">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="40206-397">Usando il codice precedente, il percorso URL che indirizza a `OnPostJoinListAsync` è `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="40206-397">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="40206-398">Il percorso URL che indirizza a `OnPostJoinListUCAsync` è `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="40206-398">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="40206-399">Route personalizzate</span><span class="sxs-lookup"><span data-stu-id="40206-399">Custom routes</span></span>

<span data-ttu-id="40206-400">Usare la direttiva `@page` per:</span><span class="sxs-lookup"><span data-stu-id="40206-400">Use the `@page` directive to:</span></span>

* <span data-ttu-id="40206-401">Specificare una route personalizzata a una pagina.</span><span class="sxs-lookup"><span data-stu-id="40206-401">Specify a custom route to a page.</span></span> <span data-ttu-id="40206-402">Ad esempio, è possibile impostare la route alla pagina About (Informazioni) su `/Some/Other/Path` con `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="40206-402">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="40206-403">Aggiungere segmenti alla route predefinita di una pagina.</span><span class="sxs-lookup"><span data-stu-id="40206-403">Append segments to a page's default route.</span></span> <span data-ttu-id="40206-404">Ad esempio, è possibile aggiungere un segmento "item" alla route predefinita di una pagina con `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="40206-404">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="40206-405">Aggiungere parametri alla route predefinita di una pagina.</span><span class="sxs-lookup"><span data-stu-id="40206-405">Append parameters to a page's default route.</span></span> <span data-ttu-id="40206-406">Ad esempio, un parametro ID, `id`, può essere necessario per una pagina con `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="40206-406">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="40206-407">Un percorso relativo alla directory radice designato da una tilde (`~`) all'inizio del percorso è supportato.</span><span class="sxs-lookup"><span data-stu-id="40206-407">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="40206-408">Ad esempio, `@page "~/Some/Other/Path"` è identico a `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="40206-408">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="40206-409">Se non si preferisce la stringa di query `?handler=JoinList` nell'URL, modificare la route per inserire il nome del gestore nella parte relativa al percorso dell'URL.</span><span class="sxs-lookup"><span data-stu-id="40206-409">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="40206-410">La route può essere personalizzata aggiungendo un modello di route racchiuso tra virgolette doppie dopo la `@page` direttiva.</span><span class="sxs-lookup"><span data-stu-id="40206-410">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="40206-411">Usando il codice precedente, il percorso URL che indirizza a `OnPostJoinListAsync` è `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="40206-411">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="40206-412">Il percorso URL che indirizza a `OnPostJoinListUCAsync` è `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="40206-412">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="40206-413">`?` che segue `handler` indica che il parametro di route è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="40206-413">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="40206-414">Impostazioni e configurazione avanzate</span><span class="sxs-lookup"><span data-stu-id="40206-414">Advanced configuration and settings</span></span>

<span data-ttu-id="40206-415">La configurazione e le impostazioni nelle sezioni seguenti non sono richieste dalla maggior parte delle app.</span><span class="sxs-lookup"><span data-stu-id="40206-415">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="40206-416">Per configurare le opzioni avanzate, usare l' <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> Overload che configura <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> :</span><span class="sxs-lookup"><span data-stu-id="40206-416">To configure advanced options, use the <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="40206-417">Utilizzare <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> per impostare la directory radice per le pagine o aggiungere le convenzioni del modello applicativo per le pagine.</span><span class="sxs-lookup"><span data-stu-id="40206-417">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="40206-418">Per ulteriori informazioni sulle convenzioni, vedere la pagina relativa alle [ Razor convenzioni di autorizzazione](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="40206-418">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="40206-419">Per eseguire la precompilazione delle visualizzazioni, vedere [ Razor visualizzare la compilazione](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="40206-419">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="40206-420">Consente di specificare che Razor le pagine si trovano nella radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="40206-420">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="40206-421">Per impostazione predefinita, Razor le pagine hanno una radice nella directory */pages*</span><span class="sxs-lookup"><span data-stu-id="40206-421">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="40206-422">Aggiungere <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> per specificare che le Razor pagine si trovano nella [radice del contenuto](xref:fundamentals/index#content-root) ( <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> ) dell'app:</span><span class="sxs-lookup"><span data-stu-id="40206-422">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="40206-423">Consente di specificare che le Razor pagine si trovano in una directory radice personalizzata</span><span class="sxs-lookup"><span data-stu-id="40206-423">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="40206-424">Aggiungere <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> per specificare che Razor le pagine si trovano in una directory radice personalizzata nell'app (fornire un percorso relativo):</span><span class="sxs-lookup"><span data-stu-id="40206-424">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="40206-425">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="40206-425">Additional resources</span></span>

* <span data-ttu-id="40206-426">Vedere Introduzione [alle Razor pagine](xref:tutorials/razor-pages/razor-pages-start), che si basa su questa introduzione.</span><span class="sxs-lookup"><span data-stu-id="40206-426">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
* <span data-ttu-id="40206-427">[Autorizzare gli attributi e le Razor pagine](xref:security/authorization/simple#aarp)</span><span class="sxs-lookup"><span data-stu-id="40206-427">[Authorize attribute and Razor Pages](xref:security/authorization/simple#aarp)</span></span>
* [<span data-ttu-id="40206-428">Scaricare o visualizzare il codice di esempio</span><span class="sxs-lookup"><span data-stu-id="40206-428">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="40206-429">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="40206-429">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

Razor<span data-ttu-id="40206-430">Pages è un nuovo aspetto di ASP.NET Core MVC che rende più semplici e più produttivi gli scenari di codifica della pagina.</span><span class="sxs-lookup"><span data-stu-id="40206-430"> Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="40206-431">Se si sta cercando un'esercitazione in cui si usa l'approccio Model-View-Controller, vedere [Introduzione ad ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="40206-431">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="40206-432">Questo documento fornisce un'introduzione alle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="40206-432">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="40206-433">Non è un'esercitazione dettagliata.</span><span class="sxs-lookup"><span data-stu-id="40206-433">It's not a step by step tutorial.</span></span> <span data-ttu-id="40206-434">Se alcune delle sezioni sono troppo avanzate, vedere [Introduzione alle Razor pagine](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="40206-434">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="40206-435">Per una panoramica di ASP.NET Core, vedere [Introduzione a ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="40206-435">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="40206-436">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="40206-436">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="40206-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40206-437">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="40206-438">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="40206-438">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="40206-439">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="40206-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="40206-440">Creazione di un Razor progetto di pagine</span><span class="sxs-lookup"><span data-stu-id="40206-440">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="40206-441">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40206-441">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="40206-442">Per istruzioni dettagliate su come creare un progetto di pagine, vedere [Introduzione alle Razor pagine](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="40206-442">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="40206-443">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="40206-443">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="40206-444">Eseguire `dotnet new webapp` dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="40206-444">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="40206-445">Aprire il file *CSPROJ* generato da Visual Studio per Mac.</span><span class="sxs-lookup"><span data-stu-id="40206-445">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="40206-446">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="40206-446">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="40206-447">Eseguire `dotnet new webapp` dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="40206-447">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="razor-pages"></a>Razor<span data-ttu-id="40206-448">Pagine</span><span class="sxs-lookup"><span data-stu-id="40206-448"> Pages</span></span>

Razor<span data-ttu-id="40206-449">Le pagine sono abilitate in *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="40206-449"> Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="40206-450">Si consideri una pagina di base: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="40206-450">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="40206-451">Il codice precedente ha un aspetto molto simile a quello di un [ Razor file di visualizzazione](xref:tutorials/first-mvc-app/adding-view) usato in un'app ASP.NET Core con i controller e le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="40206-451">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="40206-452">Ciò che lo differenzia è la direttiva `@page`.</span><span class="sxs-lookup"><span data-stu-id="40206-452">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="40206-453">`@page` trasforma il file in un'azione MVC, ovvero gestisce le richieste direttamente, senza passare attraverso un controller.</span><span class="sxs-lookup"><span data-stu-id="40206-453">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="40206-454">`@page`deve essere la prima Razor direttiva in una pagina.</span><span class="sxs-lookup"><span data-stu-id="40206-454">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="40206-455">`@page`influiscono sul comportamento di altri Razor costrutti.</span><span class="sxs-lookup"><span data-stu-id="40206-455">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="40206-456">Nei due file seguenti viene visualizzata una pagina simile che usa una classe `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="40206-456">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="40206-457">Il file *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="40206-457">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="40206-458">Il modello di pagina *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="40206-458">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="40206-459">Per convenzione, il `PageModel` file di classe ha lo stesso nome del Razor file di paging con l'aggiunta di *. cs* .</span><span class="sxs-lookup"><span data-stu-id="40206-459">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="40206-460">La pagina precedente, ad esempio, Razor è *pages/index2. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="40206-460">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="40206-461">Il file che contiene la classe `PageModel` è denominato *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="40206-461">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="40206-462">Le associazioni dei percorsi URL alle pagine sono determinate dalla posizione della pagina nel file system.</span><span class="sxs-lookup"><span data-stu-id="40206-462">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="40206-463">La tabella seguente mostra un Razor percorso di pagina e l'URL corrispondente:</span><span class="sxs-lookup"><span data-stu-id="40206-463">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="40206-464">Percorso e nome file</span><span class="sxs-lookup"><span data-stu-id="40206-464">File name and path</span></span>               | <span data-ttu-id="40206-465">URL corrispondente</span><span class="sxs-lookup"><span data-stu-id="40206-465">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="40206-466">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="40206-466">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="40206-467">`/` o `/Index`</span><span class="sxs-lookup"><span data-stu-id="40206-467">`/` or `/Index`</span></span> |
| <span data-ttu-id="40206-468">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="40206-468">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="40206-469">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="40206-469">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="40206-470">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="40206-470">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="40206-471">`/Store` o `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="40206-471">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="40206-472">Note:</span><span class="sxs-lookup"><span data-stu-id="40206-472">Notes:</span></span>

* <span data-ttu-id="40206-473">Per Razor impostazione predefinita, il runtime cerca i file di pagine nella cartella *pages* .</span><span class="sxs-lookup"><span data-stu-id="40206-473">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="40206-474">`Index` è la pagina predefinita quando un URL non include una pagina.</span><span class="sxs-lookup"><span data-stu-id="40206-474">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="40206-475">Scrivere un form di base</span><span class="sxs-lookup"><span data-stu-id="40206-475">Write a basic form</span></span>

Razor<span data-ttu-id="40206-476">Pagine è progettato per semplificare l'implementazione dei modelli comuni usati con i Web browser quando si compila un'app.</span><span class="sxs-lookup"><span data-stu-id="40206-476"> Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="40206-477">L' [associazione di modelli](xref:mvc/models/model-binding), gli [Helper Tag](xref:mvc/views/tag-helpers/intro)e gli helper HTML *funzionano solo* con le proprietà definite in una Razor classe di pagine.</span><span class="sxs-lookup"><span data-stu-id="40206-477">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="40206-478">Si consideri una pagina che implementa un form "contact us" di base per il modello `Contact`:</span><span class="sxs-lookup"><span data-stu-id="40206-478">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="40206-479">Per gli esempi riportati in questo documento, `DbContext` viene inizializzato nel file [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16).</span><span class="sxs-lookup"><span data-stu-id="40206-479">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="40206-480">Il modello di dati:</span><span class="sxs-lookup"><span data-stu-id="40206-480">The data model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="40206-481">Il contesto del database:</span><span class="sxs-lookup"><span data-stu-id="40206-481">The db context:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="40206-482">Il file di visualizzazione *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="40206-482">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="40206-483">Il modello di pagina *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="40206-483">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="40206-484">Per convenzione, la classe `PageModel` è denominata `<PageName>Model` e si trova nello stesso spazio dei nomi della pagina.</span><span class="sxs-lookup"><span data-stu-id="40206-484">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="40206-485">La classe `PageModel` consente la separazione della logica di una pagina dalla relativa presentazione.</span><span class="sxs-lookup"><span data-stu-id="40206-485">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="40206-486">Definisce i gestori di pagina per le richieste inviate alla pagina e i dati usati per il rendering della pagina.</span><span class="sxs-lookup"><span data-stu-id="40206-486">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="40206-487">Questa separazione consente:</span><span class="sxs-lookup"><span data-stu-id="40206-487">This separation allows:</span></span>

* <span data-ttu-id="40206-488">Gestione delle dipendenze di pagina tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="40206-488">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="40206-489">[Testing unità](xref:test/razor-pages-tests) delle pagine.</span><span class="sxs-lookup"><span data-stu-id="40206-489">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="40206-490">La pagina contiene un oggetto , ovvero un `OnPostAsync` *metodo gestore* che viene eseguito per le richieste `POST`, quando un utente invia il form.</span><span class="sxs-lookup"><span data-stu-id="40206-490">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="40206-491">È possibile aggiungere metodi del gestore per qualsiasi verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="40206-491">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="40206-492">I gestori più comuni sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="40206-492">The most common handlers are:</span></span>

* <span data-ttu-id="40206-493">`OnGet` per inizializzare lo stato necessario per la pagina.</span><span class="sxs-lookup"><span data-stu-id="40206-493">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="40206-494">Esempio di [OnGet](#OnGet).</span><span class="sxs-lookup"><span data-stu-id="40206-494">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="40206-495">`OnPost` per gestire gli invii di form.</span><span class="sxs-lookup"><span data-stu-id="40206-495">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="40206-496">Il suffisso `Async` nel nome è facoltativo, ma viene spesso usato per convenzione per le funzioni asincrone.</span><span class="sxs-lookup"><span data-stu-id="40206-496">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="40206-497">Il codice precedente è tipico per le Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="40206-497">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="40206-498">Se si ha familiarità con le app ASP.NET che usano i controller e le visualizzazioni:</span><span class="sxs-lookup"><span data-stu-id="40206-498">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="40206-499">Il `OnPostAsync` codice nell'esempio precedente ha un aspetto simile al codice del controller tipico.</span><span class="sxs-lookup"><span data-stu-id="40206-499">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="40206-500">La maggior parte delle primitive MVC, ad esempio l' [associazione di modelli](xref:mvc/models/model-binding), la [convalida](xref:mvc/models/validation), la [convalida](xref:mvc/models/validation)e i risultati dell'azione, sono condivisi.</span><span class="sxs-lookup"><span data-stu-id="40206-500">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="40206-501">Il metodo `OnPostAsync` precedente:</span><span class="sxs-lookup"><span data-stu-id="40206-501">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="40206-502">Il flusso di base di `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="40206-502">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="40206-503">Verificare se sono presenti errori di convalida.</span><span class="sxs-lookup"><span data-stu-id="40206-503">Check for validation errors.</span></span>

* <span data-ttu-id="40206-504">Se non sono presenti errori, salvare i dati e reindirizzare.</span><span class="sxs-lookup"><span data-stu-id="40206-504">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="40206-505">Se sono presenti errori, visualizzare di nuovo la pagina con i messaggi di convalida.</span><span class="sxs-lookup"><span data-stu-id="40206-505">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="40206-506">La convalida lato client è identica alle applicazioni ASP.NET Core MVC tradizionali.</span><span class="sxs-lookup"><span data-stu-id="40206-506">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="40206-507">In molti casi, gli errori di convalida vengono rilevati nel client e non vengono mai inviati al server.</span><span class="sxs-lookup"><span data-stu-id="40206-507">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="40206-508">Quando i dati vengono immessi correttamente, il metodo gestore `OnPostAsync` chiama il metodo helper `RedirectToPage` per restituire un'istanza di `RedirectToPageResult`.</span><span class="sxs-lookup"><span data-stu-id="40206-508">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="40206-509">`RedirectToPage` è un nuovo risultato dell'azione, simile a `RedirectToAction` o `RedirectToRoute`, ma personalizzato per le pagine.</span><span class="sxs-lookup"><span data-stu-id="40206-509">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="40206-510">Nell'esempio precedente viene reindirizzato alla pagina di indice radice (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="40206-510">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="40206-511">`RedirectToPage`è descritto in dettaglio nella sezione [generazione URL per pagine](#url_gen) .</span><span class="sxs-lookup"><span data-stu-id="40206-511">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="40206-512">Quando il form inviato contiene errori di convalida (che vengono passati al server), il metodo gestore `OnPostAsync` chiama il metodo helper `Page`.</span><span class="sxs-lookup"><span data-stu-id="40206-512">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="40206-513">`Page` restituisce un'istanza di `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="40206-513">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="40206-514">La restituzione di `Page` è simile al modo in cui le azioni nel controller restituiscono `View`.</span><span class="sxs-lookup"><span data-stu-id="40206-514">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="40206-515">`PageResult`è il tipo restituito predefinito per un metodo del gestore.</span><span class="sxs-lookup"><span data-stu-id="40206-515">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="40206-516">Un metodo gestore che restituisce `void` esegue il rendering della pagina.</span><span class="sxs-lookup"><span data-stu-id="40206-516">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="40206-517">La proprietà `Customer` usa l'attributo `[BindProperty]` optare per consentire l'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="40206-517">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

Razor<span data-ttu-id="40206-518">Le pagine, per impostazione predefinita, associano proprietà solo con non `GET` verbi.</span><span class="sxs-lookup"><span data-stu-id="40206-518"> Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="40206-519">L'associazione alle proprietà può ridurre la quantità di codice da scrivere.</span><span class="sxs-lookup"><span data-stu-id="40206-519">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="40206-520">Riduce il codice usando la stessa proprietà per il eseguire il rendering dei campi del form (`<input asp-for="Customer.Name">`) e accettare l'input.</span><span class="sxs-lookup"><span data-stu-id="40206-520">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="40206-521">La home page (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="40206-521">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="40206-522">La classe `PageModel` associata (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="40206-522">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="40206-523">Il file *Index.cshtml* contiene il markup seguente per creare un collegamento di modifica per ogni contatto:</span><span class="sxs-lookup"><span data-stu-id="40206-523">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="40206-524">L' `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Helper tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) ha usato l' `asp-route-{value}` attributo per generare un collegamento alla pagina di modifica.</span><span class="sxs-lookup"><span data-stu-id="40206-524">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="40206-525">Il collegamento contiene i dati della route con l'ID contatto.</span><span class="sxs-lookup"><span data-stu-id="40206-525">The link contains route data with the contact ID.</span></span> <span data-ttu-id="40206-526">Ad esempio: `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="40206-526">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="40206-527">Gli [Helper Tag](xref:mvc/views/tag-helpers/intro) consentono al codice lato server di partecipare alla creazione e al rendering di elementi HTML nei Razor file.</span><span class="sxs-lookup"><span data-stu-id="40206-527">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="40206-528">Gli helper tag sono abilitati da `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="40206-528">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="40206-529">Il file *Pages/Edit.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="40206-529">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="40206-530">La prima riga contiene la direttiva `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="40206-530">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="40206-531">Il vincolo di routing `"{id:int}"` indica alla pagina di accettare le richieste inviate alla pagina che contengono i dati della route `int`.</span><span class="sxs-lookup"><span data-stu-id="40206-531">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="40206-532">Se una richiesta inviata alla pagina non contiene dati della route che possono essere convertiti in un oggetto `int`, il runtime restituisce un errore HTTP 404 (non trovato).</span><span class="sxs-lookup"><span data-stu-id="40206-532">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="40206-533">Per rendere l'ID facoltativo, aggiungere `?` al vincolo di route:</span><span class="sxs-lookup"><span data-stu-id="40206-533">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="40206-534">Il file *Pages/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="40206-534">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="40206-535">Il file *Index.cshtml* contiene anche il markup per creare un pulsante di eliminazione per ogni contatto cliente:</span><span class="sxs-lookup"><span data-stu-id="40206-535">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="40206-536">Quando viene eseguito il rendering del pulsante di eliminazione in HTML, il relativo `formaction` include i parametri per:</span><span class="sxs-lookup"><span data-stu-id="40206-536">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="40206-537">L'ID di contatto cliente dall'attributo `asp-route-id`.</span><span class="sxs-lookup"><span data-stu-id="40206-537">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="40206-538">L'`handler` specificato dall'attributo `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="40206-538">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="40206-539">Di seguito è riportato un esempio di pulsante di eliminazione di cui è stato eseguito il rendering con un ID di contatto cliente `1`:</span><span class="sxs-lookup"><span data-stu-id="40206-539">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="40206-540">Quando il pulsante è selezionato, viene inviata una richiesta `POST` di modulo al server.</span><span class="sxs-lookup"><span data-stu-id="40206-540">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="40206-541">Per convenzione, il nome del metodo del gestore viene selezionato in base al valore del parametro `handler` in base allo schema `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="40206-541">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="40206-542">Poiché in questo esempio l'`handler` è `delete`, il metodo `OnPostDeleteAsync` viene usato per elaborare la richiesta `POST`.</span><span class="sxs-lookup"><span data-stu-id="40206-542">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="40206-543">Se `asp-page-handler` viene impostato su un valore diverso, ad esempio `remove`, viene selezionato un metodo gestore con il nome `OnPostRemoveAsync`.</span><span class="sxs-lookup"><span data-stu-id="40206-543">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="40206-544">Il codice seguente illustra il `OnPostDeleteAsync` gestore:</span><span class="sxs-lookup"><span data-stu-id="40206-544">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="40206-545">Il metodo `OnPostDeleteAsync`:</span><span class="sxs-lookup"><span data-stu-id="40206-545">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="40206-546">Accetta l'`id` dalla stringa di query.</span><span class="sxs-lookup"><span data-stu-id="40206-546">Accepts the `id` from the query string.</span></span> <span data-ttu-id="40206-547">Se la direttiva della pagina *index. cshtml* conteneva il vincolo `"{id:int?}"` di routing, `id` verrebbe derivare dai dati della route.</span><span class="sxs-lookup"><span data-stu-id="40206-547">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="40206-548">I dati della route per `id` sono specificati nell'URI, ad esempio `https://localhost:5001/Customers/2` .</span><span class="sxs-lookup"><span data-stu-id="40206-548">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="40206-549">Interroga il database in merito al contatto del cliente con `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="40206-549">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="40206-550">Se viene trovato il contatto cliente, viene rimosso dall'elenco dei contatti del cliente.</span><span class="sxs-lookup"><span data-stu-id="40206-550">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="40206-551">Il database viene aggiornato.</span><span class="sxs-lookup"><span data-stu-id="40206-551">The database is updated.</span></span>
* <span data-ttu-id="40206-552">Chiama `RedirectToPage` per reindirizzare alla pagina di indice radice (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="40206-552">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="40206-553">Contrassegnare le proprietà della pagina in base alle esigenze</span><span class="sxs-lookup"><span data-stu-id="40206-553">Mark page properties as required</span></span>

<span data-ttu-id="40206-554">Le proprietà di un oggetto `PageModel` possono essere contrassegnate con l'attributo [obbligatorio](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) :</span><span class="sxs-lookup"><span data-stu-id="40206-554">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="40206-555">Per altre informazioni, vedere [Convalida del modello](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="40206-555">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="40206-556">Gestire le richieste HEAD con un fallback del gestore OnGet</span><span class="sxs-lookup"><span data-stu-id="40206-556">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="40206-557">Le richieste `HEAD` consentono di recuperare le intestazioni di una risorsa specifica.</span><span class="sxs-lookup"><span data-stu-id="40206-557">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="40206-558">A differenza delle richieste `GET`, le richieste `HEAD` non restituiscono un corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="40206-558">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="40206-559">In genere, per le richieste `HEAD` viene creato e chiamato un gestore `OnHead`:</span><span class="sxs-lookup"><span data-stu-id="40206-559">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="40206-560">In ASP.NET Core 2,1 o versioni successive, Razor le pagine eseguono il fallback alla chiamata del `OnGet` gestore se non `OnHead` è stato definito alcun gestore.</span><span class="sxs-lookup"><span data-stu-id="40206-560">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="40206-561">Questo comportamento è abilitato tramite la chiamata a [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="40206-561">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="40206-562">I modelli predefiniti generano la chiamata `SetCompatibilityVersion` in ASP.NET Core 2.1 e 2.2.</span><span class="sxs-lookup"><span data-stu-id="40206-562">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="40206-563">`SetCompatibilityVersion`imposta effettivamente l' Razor opzione Pages `AllowMappingHeadRequestsToGetHandler` su `true` .</span><span class="sxs-lookup"><span data-stu-id="40206-563">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="40206-564">Anziché acconsentire esplicitamente a tutti i comportamenti con `SetCompatibilityVersion`, è possibile acconsentire a comportamenti *specifici*.</span><span class="sxs-lookup"><span data-stu-id="40206-564">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="40206-565">Il codice seguente acconsente esplicitamente al mapping delle richieste `HEAD` al gestore `OnGet`:</span><span class="sxs-lookup"><span data-stu-id="40206-565">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="40206-566">XSRF/CSRF e Razor pagine</span><span class="sxs-lookup"><span data-stu-id="40206-566">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="40206-567">Non è necessario scrivere codice per la [convalida antifalsificazione](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="40206-567">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="40206-568">La generazione e la convalida di token antifalsificazione sono incluse automaticamente nelle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="40206-568">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="40206-569">Uso di layout, parti parziali, modelli e helper tag con Razor pagine</span><span class="sxs-lookup"><span data-stu-id="40206-569">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="40206-570">Le pagine funzionano con tutte le funzionalità del Razor motore di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="40206-570">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="40206-571">Layout, parti parziali, modelli, helper tag, *_ViewStart. cshtml*, *_ViewImports. cshtml* funzionano esattamente come per le Razor visualizzazioni convenzionali.</span><span class="sxs-lookup"><span data-stu-id="40206-571">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="40206-572">La pagina verrà ora riorganizzata in modo da usufruire di alcune di queste funzionalità.</span><span class="sxs-lookup"><span data-stu-id="40206-572">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="40206-573">Aggiungere una [pagina di layout](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="40206-573">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="40206-574">Il [layout](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="40206-574">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="40206-575">Controlla il layout di ogni pagina, a meno che la pagina non accetti il layout.</span><span class="sxs-lookup"><span data-stu-id="40206-575">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="40206-576">Importa le strutture HTML, ad esempio JavaScript e i fogli di stile.</span><span class="sxs-lookup"><span data-stu-id="40206-576">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="40206-577">Vedere l'articolo sulla [pagina Layout](xref:mvc/views/layout) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="40206-577">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="40206-578">La proprietà [Layout](xref:mvc/views/layout#specifying-a-layout) proprietà viene impostata in *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="40206-578">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="40206-579">Il layout è nella cartella *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="40206-579">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="40206-580">Le pagine cercano altre visualizzazioni (layout, modelli, righe parzialmente eseguite) in modo gerarchico, partendo dalla stessa cartella della pagina corrente.</span><span class="sxs-lookup"><span data-stu-id="40206-580">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="40206-581">Un layout nella cartella pages */Shared* può essere utilizzato da qualsiasi Razor pagina nella cartella *pages* .</span><span class="sxs-lookup"><span data-stu-id="40206-581">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="40206-582">Il file di layout dovrebbe andare nella cartella *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="40206-582">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="40206-583">Si consiglia di **non** inserire il file di layout nella cartella *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="40206-583">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="40206-584">*Views/Shared* è un modello destinato alle visualizzazioni MVC.</span><span class="sxs-lookup"><span data-stu-id="40206-584">*Views/Shared* is an MVC views pattern.</span></span> Razor<span data-ttu-id="40206-585">Le pagine hanno lo scopo di basarsi sulla gerarchia di cartelle, non sulle convenzioni di percorso.</span><span class="sxs-lookup"><span data-stu-id="40206-585"> Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="40206-586">Visualizza la ricerca da una Razor pagina include la cartella *pagine* .</span><span class="sxs-lookup"><span data-stu-id="40206-586">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="40206-587">I layout, i modelli e i parziali utilizzati con i controller MVC e le Razor visualizzazioni convenzionali *funzionano semplicemente*.</span><span class="sxs-lookup"><span data-stu-id="40206-587">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="40206-588">Aggiungere un file *Pages/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="40206-588">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="40206-589">`@namespace` viene spiegato in seguito nell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="40206-589">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="40206-590">La direttiva `@addTagHelper` inserisce gli [helper tag predefiniti](xref:mvc/views/tag-helpers/builtin-th/Index) in tutte le pagine presenti nella cartella *Pages*.</span><span class="sxs-lookup"><span data-stu-id="40206-590">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="40206-591">Quando la direttiva `@namespace` viene usata in modo esplicito in una pagina:</span><span class="sxs-lookup"><span data-stu-id="40206-591">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="40206-592">La direttiva imposta lo spazio dei nomi per la pagina.</span><span class="sxs-lookup"><span data-stu-id="40206-592">The directive sets the namespace for the page.</span></span> <span data-ttu-id="40206-593">La direttiva `@model` non deve necessariamente includere lo spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="40206-593">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="40206-594">Se la direttiva `@namespace` è contenuta in *_ViewImports.cshtml*, lo spazio dei nomi specificato indica il prefisso per lo spazio dei nomi generato nella pagina che importa la direttiva `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="40206-594">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="40206-595">Il resto dello spazio dei nomi generato (la parte del suffisso) è il percorso relativo separato dal punto tra la cartella contenente *_Viewimports.cshtml* e la cartella contenente la pagina.</span><span class="sxs-lookup"><span data-stu-id="40206-595">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="40206-596">Ad esempio, la classe `PageModel`*Pages/Customers/Edit.cshtml.cs* imposta in modo esplicito lo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="40206-596">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="40206-597">Il file *Pages/_ViewImports.cshtml* file imposta il seguente spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="40206-597">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="40206-598">Lo spazio dei nomi generato per la pagina *pages/Customers/Edit. cshtml* Razor è uguale a quello della `PageModel` classe.</span><span class="sxs-lookup"><span data-stu-id="40206-598">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="40206-599">`@namespace`*funziona anche con le Razor visualizzazioni convenzionali.*</span><span class="sxs-lookup"><span data-stu-id="40206-599">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="40206-600">Il file di visualizzazione *Pages/Create.cshtml* originale:</span><span class="sxs-lookup"><span data-stu-id="40206-600">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="40206-601">Il file di visualizzazione *Pages/Create.cshtml* aggiornato:</span><span class="sxs-lookup"><span data-stu-id="40206-601">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="40206-602">Il [ Razor progetto di avvio pagine](#rpvs17) contiene le *pagine/_ValidationScriptsPartial. cshtml*, che consente di associare la convalida lato client.</span><span class="sxs-lookup"><span data-stu-id="40206-602">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="40206-603">Per altre informazioni sulle visualizzazioni parziali, vedere <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="40206-603">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="40206-604">Generazione di URL per le pagine</span><span class="sxs-lookup"><span data-stu-id="40206-604">URL generation for Pages</span></span>

<span data-ttu-id="40206-605">La pagina `Create`, riportata in precedenza, usa `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="40206-605">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="40206-606">L'applicazione ha la struttura di file o cartella seguente:</span><span class="sxs-lookup"><span data-stu-id="40206-606">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="40206-607">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="40206-607">*/Pages*</span></span>

  * <span data-ttu-id="40206-608">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="40206-608">*Index.cshtml*</span></span>
  * <span data-ttu-id="40206-609">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="40206-609">*/Customers*</span></span>

    * <span data-ttu-id="40206-610">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="40206-610">*Create.cshtml*</span></span>
    * <span data-ttu-id="40206-611">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="40206-611">*Edit.cshtml*</span></span>
    * <span data-ttu-id="40206-612">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="40206-612">*Index.cshtml*</span></span>

<span data-ttu-id="40206-613">Le pagine *Pages/Customers/Create.cshtml* e *Pages/Customers/Edit.cshtml* reindirizzano a *Pages/Index.cshtml* dopo l'esecuzione.</span><span class="sxs-lookup"><span data-stu-id="40206-613">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="40206-614">La stringa `/Index` fa parte dell'URI di accesso alla pagina precedente.</span><span class="sxs-lookup"><span data-stu-id="40206-614">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="40206-615">La stringa `/Index` può essere usata per generare gli URI alla pagina *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="40206-615">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="40206-616">ad esempio:</span><span class="sxs-lookup"><span data-stu-id="40206-616">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="40206-617">Il nome della pagina è il percorso alla pagina dalla cartella radice */Pages*, inclusa una barra iniziale `/`, ad esempio `/Index`.</span><span class="sxs-lookup"><span data-stu-id="40206-617">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="40206-618">Gli esempi di generazione di URL precedenti offrono opzioni e caratteristiche funzionali avanzate rispetto agli URL hardcoded.</span><span class="sxs-lookup"><span data-stu-id="40206-618">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="40206-619">La generazione di URL usa il [routing](xref:mvc/controllers/routing) ed è in grado di generare e codificare i parametri in base al modo in cui la route è definita nel percorso di destinazione.</span><span class="sxs-lookup"><span data-stu-id="40206-619">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="40206-620">La generazione di URL per le pagine supporta i nomi relativi.</span><span class="sxs-lookup"><span data-stu-id="40206-620">URL generation for pages supports relative names.</span></span> <span data-ttu-id="40206-621">La tabella seguente indica quale pagina di indice viene selezionata con diversi parametri `RedirectToPage` da *Pages/Customers/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="40206-621">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="40206-622">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="40206-622">RedirectToPage(x)</span></span>| <span data-ttu-id="40206-623">Page</span><span class="sxs-lookup"><span data-stu-id="40206-623">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="40206-624">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="40206-624">RedirectToPage("/Index")</span></span> | <span data-ttu-id="40206-625">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="40206-625">*Pages/Index*</span></span> |
| <span data-ttu-id="40206-626">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="40206-626">RedirectToPage("./Index");</span></span> | <span data-ttu-id="40206-627">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="40206-627">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="40206-628">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="40206-628">RedirectToPage("../Index")</span></span> | <span data-ttu-id="40206-629">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="40206-629">*Pages/Index*</span></span> |
| <span data-ttu-id="40206-630">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="40206-630">RedirectToPage("Index")</span></span>  | <span data-ttu-id="40206-631">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="40206-631">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="40206-632">`RedirectToPage("Index")`, `RedirectToPage("./Index")` e `RedirectToPage("../Index")` sono *nomi relativi*.</span><span class="sxs-lookup"><span data-stu-id="40206-632">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="40206-633">Il parametro `RedirectToPage` è *combinato* con il percorso della pagina corrente per calcolare il nome della pagina di destinazione.</span><span class="sxs-lookup"><span data-stu-id="40206-633">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="40206-634">Il collegamento dei nomi relativi è utile quando si compilano siti con una struttura complessa.</span><span class="sxs-lookup"><span data-stu-id="40206-634">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="40206-635">Se si usano i nomi relativi per il collegamento tra le pagine in una cartella, è possibile rinominare tale cartella.</span><span class="sxs-lookup"><span data-stu-id="40206-635">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="40206-636">Tutti i collegamenti continuano a funzionare perché non includono il nome della cartella.</span><span class="sxs-lookup"><span data-stu-id="40206-636">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="40206-637">Per reindirizzare la pagina a un'[Area](xref:mvc/controllers/areas) diversa, specificare l'area:</span><span class="sxs-lookup"><span data-stu-id="40206-637">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="40206-638">Per altre informazioni, vedere <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="40206-638">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="40206-639">Attributo viewData</span><span class="sxs-lookup"><span data-stu-id="40206-639">ViewData attribute</span></span>

<span data-ttu-id="40206-640">È possibile passare i dati a una pagina tramite [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="40206-640">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="40206-641">I valori delle proprietà nei controller o nei Razor modelli di pagina con l' `[ViewData]` attributo sono archiviati e caricati da [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="40206-641">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="40206-642">Nell'esempio seguente, `AboutModel` contiene una `Title` proprietà contrassegnata con `[ViewData]` .</span><span class="sxs-lookup"><span data-stu-id="40206-642">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="40206-643">La proprietà `Title` è impostata sul titolo della pagina About (Informazioni):</span><span class="sxs-lookup"><span data-stu-id="40206-643">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="40206-644">Nella pagina About (Informazioni) accedere alla proprietà `Title` come proprietà del modello:</span><span class="sxs-lookup"><span data-stu-id="40206-644">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="40206-645">Nel layout il titolo viene letto dal dizionario ViewData:</span><span class="sxs-lookup"><span data-stu-id="40206-645">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="40206-646">TempData</span><span class="sxs-lookup"><span data-stu-id="40206-646">TempData</span></span>

<span data-ttu-id="40206-647">ASP.NET Core espone la proprietà [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) per un [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="40206-647">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="40206-648">Questa proprietà archivia i dati finché non viene letta.</span><span class="sxs-lookup"><span data-stu-id="40206-648">This property stores data until it's read.</span></span> <span data-ttu-id="40206-649">I metodi `Keep` e `Peek` possono essere usati per esaminare i dati senza eliminazione.</span><span class="sxs-lookup"><span data-stu-id="40206-649">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="40206-650">`TempData` è utile per il reindirizzamento quando i dati sono necessari per più di una singola richiesta.</span><span class="sxs-lookup"><span data-stu-id="40206-650">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="40206-651">Il codice seguente imposta il valore di `Message` usando `TempData`:</span><span class="sxs-lookup"><span data-stu-id="40206-651">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="40206-652">Il markup seguente nel file *Pages/Customers/Index.cshtml* visualizza il valore di `Message` usando `TempData`.</span><span class="sxs-lookup"><span data-stu-id="40206-652">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="40206-653">Il modello di pagina *Pages/Customers/Index.cshtml.cs* applica l'attributo `[TempData]` alla proprietà `Message`.</span><span class="sxs-lookup"><span data-stu-id="40206-653">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="40206-654">Per altre informazioni, vedere [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="40206-654">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="40206-655">Più gestori per pagina</span><span class="sxs-lookup"><span data-stu-id="40206-655">Multiple handlers per page</span></span>

<span data-ttu-id="40206-656">La pagina seguente genera markup per due gestori usando l'helper tag `asp-page-handler`:</span><span class="sxs-lookup"><span data-stu-id="40206-656">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="40206-657">Il form nell'esempio precedente contiene due pulsanti di invio, ognuno dei quali usa `FormActionTagHelper` per l'invio a un URL diverso.</span><span class="sxs-lookup"><span data-stu-id="40206-657">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="40206-658">L'attributo `asp-page-handler` è correlato a `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="40206-658">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="40206-659">`asp-page-handler` genera gli URL che indirizzano a ognuno dei metodi gestore definiti da una pagina.</span><span class="sxs-lookup"><span data-stu-id="40206-659">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="40206-660">`asp-page` non è specificato poiché l'esempio è collegato alla pagina corrente.</span><span class="sxs-lookup"><span data-stu-id="40206-660">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="40206-661">Il modello di pagina:</span><span class="sxs-lookup"><span data-stu-id="40206-661">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="40206-662">Il codice precedente usa *metodi gestore denominati*.</span><span class="sxs-lookup"><span data-stu-id="40206-662">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="40206-663">I metodi gestore denominati vengono creati usando il testo che nel nome segue `On<HTTP Verb>` e precede `Async` (se presente).</span><span class="sxs-lookup"><span data-stu-id="40206-663">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="40206-664">Nell'esempio precedente i metodi della pagina sono OnPost**JoinList**Async e OnPost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="40206-664">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="40206-665">Rimuovendo *OnPost* e *Async*, i nomi dei gestori sono `JoinList` e `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="40206-665">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="40206-666">Usando il codice precedente, il percorso URL che indirizza a `OnPostJoinListAsync` è `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="40206-666">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="40206-667">Il percorso URL che indirizza a `OnPostJoinListUCAsync` è `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="40206-667">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="40206-668">Route personalizzate</span><span class="sxs-lookup"><span data-stu-id="40206-668">Custom routes</span></span>

<span data-ttu-id="40206-669">Usare la direttiva `@page` per:</span><span class="sxs-lookup"><span data-stu-id="40206-669">Use the `@page` directive to:</span></span>

* <span data-ttu-id="40206-670">Specificare una route personalizzata a una pagina.</span><span class="sxs-lookup"><span data-stu-id="40206-670">Specify a custom route to a page.</span></span> <span data-ttu-id="40206-671">Ad esempio, è possibile impostare la route alla pagina About (Informazioni) su `/Some/Other/Path` con `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="40206-671">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="40206-672">Aggiungere segmenti alla route predefinita di una pagina.</span><span class="sxs-lookup"><span data-stu-id="40206-672">Append segments to a page's default route.</span></span> <span data-ttu-id="40206-673">Ad esempio, è possibile aggiungere un segmento "item" alla route predefinita di una pagina con `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="40206-673">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="40206-674">Aggiungere parametri alla route predefinita di una pagina.</span><span class="sxs-lookup"><span data-stu-id="40206-674">Append parameters to a page's default route.</span></span> <span data-ttu-id="40206-675">Ad esempio, un parametro ID, `id`, può essere necessario per una pagina con `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="40206-675">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="40206-676">Un percorso relativo alla directory radice designato da una tilde (`~`) all'inizio del percorso è supportato.</span><span class="sxs-lookup"><span data-stu-id="40206-676">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="40206-677">Ad esempio, `@page "~/Some/Other/Path"` è identico a `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="40206-677">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="40206-678">Se non si preferisce la stringa di query `?handler=JoinList` nell'URL, modificare la route per inserire il nome del gestore nella parte relativa al percorso dell'URL.</span><span class="sxs-lookup"><span data-stu-id="40206-678">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="40206-679">La route può essere personalizzata aggiungendo un modello di route racchiuso tra virgolette doppie dopo la `@page` direttiva.</span><span class="sxs-lookup"><span data-stu-id="40206-679">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="40206-680">Usando il codice precedente, il percorso URL che indirizza a `OnPostJoinListAsync` è `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="40206-680">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="40206-681">Il percorso URL che indirizza a `OnPostJoinListUCAsync` è `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="40206-681">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="40206-682">`?` che segue `handler` indica che il parametro di route è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="40206-682">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="40206-683">Configurazione e impostazioni</span><span class="sxs-lookup"><span data-stu-id="40206-683">Configuration and settings</span></span>

<span data-ttu-id="40206-684">Per configurare le opzioni avanzate, usare il metodo di estensione `AddRazorPagesOptions` nel generatore MVC:</span><span class="sxs-lookup"><span data-stu-id="40206-684">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="40206-685">Attualmente è possibile usare `RazorPagesOptions` per impostare la directory radice per le pagine o aggiungere convenzioni di modello applicativo per le pagine.</span><span class="sxs-lookup"><span data-stu-id="40206-685">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="40206-686">In questo modo si potrà garantire una maggiore estendibilità in futuro.</span><span class="sxs-lookup"><span data-stu-id="40206-686">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="40206-687">Per eseguire la precompilazione delle visualizzazioni, vedere [ Razor visualizzare la compilazione](xref:mvc/views/view-compilation) .</span><span class="sxs-lookup"><span data-stu-id="40206-687">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="40206-688">[Scaricare o visualizzare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="40206-688">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="40206-689">Vedere Introduzione [alle Razor pagine](xref:tutorials/razor-pages/razor-pages-start), che si basa su questa introduzione.</span><span class="sxs-lookup"><span data-stu-id="40206-689">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="40206-690">Consente di specificare che Razor le pagine si trovano nella radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="40206-690">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="40206-691">Per impostazione predefinita, Razor le pagine hanno una radice nella directory */pages*</span><span class="sxs-lookup"><span data-stu-id="40206-691">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="40206-692">Aggiungere [con Razor PagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) a [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) per specificare che le Razor pagine si trovano nella [radice del contenuto](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) dell'app:</span><span class="sxs-lookup"><span data-stu-id="40206-692">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="40206-693">Consente di specificare che le Razor pagine si trovano in una directory radice personalizzata</span><span class="sxs-lookup"><span data-stu-id="40206-693">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="40206-694">Aggiungere [con Razor PagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) a [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) per specificare che le Razor pagine si trovano in una directory radice personalizzata nell'app (fornire un percorso relativo):</span><span class="sxs-lookup"><span data-stu-id="40206-694">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="40206-695">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="40206-695">Additional resources</span></span>

* <span data-ttu-id="40206-696">[Autorizzare gli attributi e le Razor pagine](xref:security/authorization/simple#aarp)</span><span class="sxs-lookup"><span data-stu-id="40206-696">[Authorize attribute and Razor Pages](xref:security/authorization/simple#aarp)</span></span>
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
