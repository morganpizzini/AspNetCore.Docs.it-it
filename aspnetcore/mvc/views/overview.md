---
title: Visualizzazioni in ASP.NET Core MVC
author: ardalis
description: Informazioni su come le visualizzazioni gestiscono la presentazione dei dati dell'app e l'interazione dell'utente in ASP.NET Core MVC.
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
uid: mvc/views/overview
ms.openlocfilehash: 8630df0ad8ea556c6edf0ab251b3c86493f751e2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020873"
---
# <a name="views-in-aspnet-core-mvc"></a><span data-ttu-id="667a3-103">Visualizzazioni in ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="667a3-103">Views in ASP.NET Core MVC</span></span>

<span data-ttu-id="667a3-104">Di [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="667a3-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="667a3-105">Questo documento illustra le visualizzazioni usate nelle applicazioni ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="667a3-105">This document explains views used in ASP.NET Core MVC applications.</span></span> <span data-ttu-id="667a3-106">Per informazioni sulle Razor pagine, vedere [Introduzione alle Razor pagine](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="667a3-106">For information on Razor Pages, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="667a3-107">Nello schema Model-View-Controller (MVC) la *visualizzazione* gestisce la presentazione dei dati dell'app e l'interazione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="667a3-107">In the Model-View-Controller (MVC) pattern, the *view* handles the app's data presentation and user interaction.</span></span> <span data-ttu-id="667a3-108">Una vista è un modello HTML con [ Razor markup](xref:mvc/views/razor)incorporato.</span><span class="sxs-lookup"><span data-stu-id="667a3-108">A view is an HTML template with embedded [Razor markup](xref:mvc/views/razor).</span></span> <span data-ttu-id="667a3-109">Razoril markup è codice che interagisce con il markup HTML per produrre una pagina Web che viene inviata al client.</span><span class="sxs-lookup"><span data-stu-id="667a3-109">Razor markup is code that interacts with HTML markup to produce a webpage that's sent to the client.</span></span>

<span data-ttu-id="667a3-110">In ASP.NET Core MVC le visualizzazioni sono file con *estensione cshtml* che usano il [linguaggio di programmazione C#](/dotnet/csharp/) nel Razor markup.</span><span class="sxs-lookup"><span data-stu-id="667a3-110">In ASP.NET Core MVC, views are *.cshtml* files that use the [C# programming language](/dotnet/csharp/) in Razor markup.</span></span> <span data-ttu-id="667a3-111">I file di visualizzazione sono in genere raggruppati in cartelle denominate per ognuno dei [controller](xref:mvc/controllers/actions) dell'app.</span><span class="sxs-lookup"><span data-stu-id="667a3-111">Usually, view files are grouped into folders named for each of the app's [controllers](xref:mvc/controllers/actions).</span></span> <span data-ttu-id="667a3-112">Le cartelle vengono archiviate in una cartella *Views* alla radice dell'app:</span><span class="sxs-lookup"><span data-stu-id="667a3-112">The folders are stored in a *Views* folder at the root of the app:</span></span>

![Cartella delle visualizzazioni aperta in Esplora soluzioni di Visual Studio con la cartella Home aperta per mostrare i file About.cshtml, Contact.cshtml e Index.cshtml](overview/_static/views_solution_explorer.png)

<span data-ttu-id="667a3-114">Il controller *Home* è rappresentato da una cartella *Home* all'interno della cartella *Views*.</span><span class="sxs-lookup"><span data-stu-id="667a3-114">The *Home* controller is represented by a *Home* folder inside the *Views* folder.</span></span> <span data-ttu-id="667a3-115">La cartella *Home* contiene le visualizzazioni per le pagine Web *About*, *Contact* e *Index* (home page).</span><span class="sxs-lookup"><span data-stu-id="667a3-115">The *Home* folder contains the views for the *About*, *Contact*, and *Index* (homepage) webpages.</span></span> <span data-ttu-id="667a3-116">Quando un utente richiede una di queste tre pagine Web, le azioni del controller nel controller *Home* determinano quale delle tre visualizzazioni verrà usata per compilare e restituire una pagina Web all'utente.</span><span class="sxs-lookup"><span data-stu-id="667a3-116">When a user requests one of these three webpages, controller actions in the *Home* controller determine which of the three views is used to build and return a webpage to the user.</span></span>

<span data-ttu-id="667a3-117">Usare i [layout](xref:mvc/views/layout) per offrire sezioni di pagine Web coerenti e ridurre la ripetizione del codice.</span><span class="sxs-lookup"><span data-stu-id="667a3-117">Use [layouts](xref:mvc/views/layout) to provide consistent webpage sections and reduce code repetition.</span></span> <span data-ttu-id="667a3-118">I layout spesso includono l'intestazione, elementi di navigazione e menu, nonché il piè di pagina.</span><span class="sxs-lookup"><span data-stu-id="667a3-118">Layouts often contain the header, navigation and menu elements, and the footer.</span></span> <span data-ttu-id="667a3-119">L'intestazione e il piè di pagina in genere contengono il markup boilerplate per molti elementi di metadati e i collegamenti alle risorse di script e stile.</span><span class="sxs-lookup"><span data-stu-id="667a3-119">The header and footer usually contain boilerplate markup for many metadata elements and links to script and style assets.</span></span> <span data-ttu-id="667a3-120">I layout consentono di evitare questo markup boilerplate nelle visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="667a3-120">Layouts help you avoid this boilerplate markup in your views.</span></span>

<span data-ttu-id="667a3-121">Le [visualizzazioni parziali](xref:mvc/views/partial) riducono la duplicazione del codice grazie alla gestione delle parti riutilizzabili delle visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="667a3-121">[Partial views](xref:mvc/views/partial) reduce code duplication by managing reusable parts of views.</span></span> <span data-ttu-id="667a3-122">Una visualizzazione parziale è ad esempio utile per la biografia di un autore nel sito Web di un blog che viene mostrata in diverse visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="667a3-122">For example, a partial view is useful for an author biography on a blog website that appears in several views.</span></span> <span data-ttu-id="667a3-123">Nel caso della biografia di un autore, il contenuto di visualizzazione è ordinario e non richiede l'esecuzione di codice per produrre il contenuto per la pagina Web.</span><span class="sxs-lookup"><span data-stu-id="667a3-123">An author biography is ordinary view content and doesn't require code to execute in order to produce the content for the webpage.</span></span> <span data-ttu-id="667a3-124">Il contenuto della biografia di un autore è disponibile per la visualizzazione tramite la sola associazione di modelli, quindi l'uso di una visualizzazione parziale per questo tipo di contenuto è ideale.</span><span class="sxs-lookup"><span data-stu-id="667a3-124">Author biography content is available to the view by model binding alone, so using a partial view for this type of content is ideal.</span></span>

<span data-ttu-id="667a3-125">I [componenti di visualizzazione](xref:mvc/views/view-components) sono simili alle visualizzazioni parziali nel senso che consentono di ridurre il codice ripetitivo, ma sono adatti per visualizzare contenuto che richiede l'esecuzione di codice nel server al fine di eseguire il rendering della pagina Web.</span><span class="sxs-lookup"><span data-stu-id="667a3-125">[View components](xref:mvc/views/view-components) are similar to partial views in that they allow you to reduce repetitive code, but they're appropriate for view content that requires code to run on the server in order to render the webpage.</span></span> <span data-ttu-id="667a3-126">I componenti di visualizzazione sono utili quando il contenuto sottoposto a rendering richiede l'interazione con il database, ad esempio nel caso del carrello acquisti di un sito Web.</span><span class="sxs-lookup"><span data-stu-id="667a3-126">View components are useful when the rendered content requires database interaction, such as for a website shopping cart.</span></span> <span data-ttu-id="667a3-127">I componenti di visualizzazione non si limitano all'associazione di modelli per produrre l'output della pagina Web.</span><span class="sxs-lookup"><span data-stu-id="667a3-127">View components aren't limited to model binding in order to produce webpage output.</span></span>

## <a name="benefits-of-using-views"></a><span data-ttu-id="667a3-128">Vantaggi dell'uso delle visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="667a3-128">Benefits of using views</span></span>

<span data-ttu-id="667a3-129">Le visualizzazioni consentono di stabilire una [separazione dei concetti](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) all'interno di un'app MVC separando il markup dell'interfaccia utente da altre parti dell'app.</span><span class="sxs-lookup"><span data-stu-id="667a3-129">Views help to establish [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) within an MVC app by separating the user interface markup from other parts of the app.</span></span> <span data-ttu-id="667a3-130">La progettazione SoC rende l'app modulare offrendo diversi vantaggi:</span><span class="sxs-lookup"><span data-stu-id="667a3-130">Following SoC design makes your app modular, which provides several benefits:</span></span>

* <span data-ttu-id="667a3-131">L'app risulta più facile da gestire perché è organizzata meglio.</span><span class="sxs-lookup"><span data-stu-id="667a3-131">The app is easier to maintain because it's better organized.</span></span> <span data-ttu-id="667a3-132">Le visualizzazioni sono in genere raggruppate per funzionalità dell'app.</span><span class="sxs-lookup"><span data-stu-id="667a3-132">Views are generally grouped by app feature.</span></span> <span data-ttu-id="667a3-133">Individuare le visualizzazioni correlate quando si lavora su una funzionalità risulterà quindi più semplice.</span><span class="sxs-lookup"><span data-stu-id="667a3-133">This makes it easier to find related views when working on a feature.</span></span>
* <span data-ttu-id="667a3-134">Le parti dell'app sono a regime di controllo libero.</span><span class="sxs-lookup"><span data-stu-id="667a3-134">The parts of the app are loosely coupled.</span></span> <span data-ttu-id="667a3-135">È possibile compilare e aggiornare le visualizzazioni dell'app separatamente dai componenti di logica di business e accesso ai dati.</span><span class="sxs-lookup"><span data-stu-id="667a3-135">You can build and update the app's views separately from the business logic and data access components.</span></span> <span data-ttu-id="667a3-136">È possibile modificare le visualizzazioni dell'app senza dover necessariamente aggiornare altre parti dell'app.</span><span class="sxs-lookup"><span data-stu-id="667a3-136">You can modify the views of the app without necessarily having to update other parts of the app.</span></span>
* <span data-ttu-id="667a3-137">Poiché le visualizzazioni sono unità distinte, testare le parti dell'interfaccia utente dell'app risulta più semplice.</span><span class="sxs-lookup"><span data-stu-id="667a3-137">It's easier to test the user interface parts of the app because the views are separate units.</span></span>
* <span data-ttu-id="667a3-138">Grazie alla migliore organizzazione, le probabilità che le sezioni dell'interfaccia utente vengano ripetute accidentalmente sono minori.</span><span class="sxs-lookup"><span data-stu-id="667a3-138">Due to better organization, it's less likely that you'll accidentally repeat sections of the user interface.</span></span>

## <a name="creating-a-view"></a><span data-ttu-id="667a3-139">Creazione di una visualizzazione</span><span class="sxs-lookup"><span data-stu-id="667a3-139">Creating a view</span></span>

<span data-ttu-id="667a3-140">Le visualizzazioni specifiche di un controller vengono create nella cartella *Views/[ControllerName]*.</span><span class="sxs-lookup"><span data-stu-id="667a3-140">Views that are specific to a controller are created in the *Views/[ControllerName]* folder.</span></span> <span data-ttu-id="667a3-141">Le visualizzazioni condivise tra i controller vengono inserite nella cartella *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="667a3-141">Views that are shared among controllers are placed in the *Views/Shared* folder.</span></span> <span data-ttu-id="667a3-142">Per creare una visualizzazione aggiungere un nuovo file e assegnargli lo stesso nome dell'azione del controller a essa associata con l'estensione *cshtml*.</span><span class="sxs-lookup"><span data-stu-id="667a3-142">To create a view, add a new file and give it the same name as its associated controller action with the *.cshtml* file extension.</span></span> <span data-ttu-id="667a3-143">Per creare una visualizzazione che corrisponda all'azione *About* nel controller *Home*, creare un file *About.cshtml* nella cartella *Views/Home*:</span><span class="sxs-lookup"><span data-stu-id="667a3-143">To create a view that corresponds with the *About* action in the *Home* controller, create an *About.cshtml* file in the *Views/Home* folder:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

<span data-ttu-id="667a3-144">*Razor* il markup inizia con il `@` simbolo.</span><span class="sxs-lookup"><span data-stu-id="667a3-144">*Razor* markup starts with the `@` symbol.</span></span> <span data-ttu-id="667a3-145">Eseguire istruzioni C# inserendo il codice C# all'interno di [ Razor blocchi di codice](xref:mvc/views/razor#razor-code-blocks) impostati da parentesi graffe ( `{ ... }` ).</span><span class="sxs-lookup"><span data-stu-id="667a3-145">Run C# statements by placing C# code within [Razor code blocks](xref:mvc/views/razor#razor-code-blocks) set off by curly braces (`{ ... }`).</span></span> <span data-ttu-id="667a3-146">Vedere ad esempio l'assegnazione di "About" a `ViewData["Title"]` illustrato sopra.</span><span class="sxs-lookup"><span data-stu-id="667a3-146">For example, see the assignment of "About" to `ViewData["Title"]` shown above.</span></span> <span data-ttu-id="667a3-147">È possibile visualizzare i valori in HTML facendo semplicemente riferimento al valore con il simbolo `@`.</span><span class="sxs-lookup"><span data-stu-id="667a3-147">You can display values within HTML by simply referencing the value with the `@` symbol.</span></span> <span data-ttu-id="667a3-148">Vedere il contenuto degli elementi `<h2>` e `<h3>` riportati sopra.</span><span class="sxs-lookup"><span data-stu-id="667a3-148">See the contents of the `<h2>` and `<h3>` elements above.</span></span>

<span data-ttu-id="667a3-149">Il contenuto della visualizzazione illustrato sopra è solo una parte dell'intera pagina Web di cui viene eseguito il rendering per l'utente.</span><span class="sxs-lookup"><span data-stu-id="667a3-149">The view content shown above is only part of the entire webpage that's rendered to the user.</span></span> <span data-ttu-id="667a3-150">Il resto del layout della pagina e altri aspetti comuni della visualizzazione sono specificati in altri file.</span><span class="sxs-lookup"><span data-stu-id="667a3-150">The rest of the page's layout and other common aspects of the view are specified in other view files.</span></span> <span data-ttu-id="667a3-151">Per altre informazioni, vedere l'[argomento Layout](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="667a3-151">To learn more, see the [Layout topic](xref:mvc/views/layout).</span></span>

## <a name="how-controllers-specify-views"></a><span data-ttu-id="667a3-152">Modalità di scelta delle visualizzazioni da parte dei controller</span><span class="sxs-lookup"><span data-stu-id="667a3-152">How controllers specify views</span></span>

<span data-ttu-id="667a3-153">Le visualizzazioni vengono in genere restituite da azioni come [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), ovvero un tipo di [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult).</span><span class="sxs-lookup"><span data-stu-id="667a3-153">Views are typically returned from actions as a [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), which is a type of [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult).</span></span> <span data-ttu-id="667a3-154">Il metodo dell'azione può creare e restituire direttamente un elemento `ViewResult`, ma questa procedura non è molto comune.</span><span class="sxs-lookup"><span data-stu-id="667a3-154">Your action method can create and return a `ViewResult` directly, but that isn't commonly done.</span></span> <span data-ttu-id="667a3-155">Poiché la maggior parte dei controller ereditano da [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), si usa semplicemente il metodo helper `View` per restituire l'elemento `ViewResult`:</span><span class="sxs-lookup"><span data-stu-id="667a3-155">Since most controllers inherit from [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), you simply use the `View` helper method to return the `ViewResult`:</span></span>

<span data-ttu-id="667a3-156">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="667a3-156">*HomeController.cs*</span></span>

[!code-csharp[](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

<span data-ttu-id="667a3-157">Quando viene restituita questa azione, il rendering della visualizzazione *About.cshtml* nell'ultima sezione corrisponderà alla pagina Web seguente:</span><span class="sxs-lookup"><span data-stu-id="667a3-157">When this action returns, the *About.cshtml* view shown in the last section is rendered as the following webpage:</span></span>

![Rendering della pagina About nel browser Microsoft Edge](overview/_static/about-page.png)

<span data-ttu-id="667a3-159">Il metodo helper `View` ha diversi overload.</span><span class="sxs-lookup"><span data-stu-id="667a3-159">The `View` helper method has several overloads.</span></span> <span data-ttu-id="667a3-160">È possibile specificare:</span><span class="sxs-lookup"><span data-stu-id="667a3-160">You can optionally specify:</span></span>

* <span data-ttu-id="667a3-161">Una visualizzazione esplicita da restituire:</span><span class="sxs-lookup"><span data-stu-id="667a3-161">An explicit view to return:</span></span>

  ```csharp
  return View("Orders");
  ```

* <span data-ttu-id="667a3-162">Un [modello](xref:mvc/models/model-binding) da passare alla visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="667a3-162">A [model](xref:mvc/models/model-binding) to pass to the view:</span></span>

  ```csharp
  return View(Orders);
  ```

* <span data-ttu-id="667a3-163">Una visualizzazione e un modello:</span><span class="sxs-lookup"><span data-stu-id="667a3-163">Both a view and a model:</span></span>

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a><span data-ttu-id="667a3-164">Individuazione delle visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="667a3-164">View discovery</span></span>

<span data-ttu-id="667a3-165">Quando un'azione restituisce una visualizzazione viene eseguito un processo denominato *individuazione delle visualizzazioni*.</span><span class="sxs-lookup"><span data-stu-id="667a3-165">When an action returns a view, a process called *view discovery* takes place.</span></span> <span data-ttu-id="667a3-166">Questo processo determina il file di visualizzazione che verrà usato in base al nome della visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="667a3-166">This process determines which view file is used based on the view name.</span></span> 

<span data-ttu-id="667a3-167">In base al comportamento predefinito, il metodo `View` (`return View();`) restituisce una visualizzazione con lo stesso nome del metodo dell'azione dal quale viene chiamato.</span><span class="sxs-lookup"><span data-stu-id="667a3-167">The default behavior of the `View` method (`return View();`) is to return a view with the same name as the action method from which it's called.</span></span> <span data-ttu-id="667a3-168">Nel caso di \*About, ad esempio, il nome del metodo \* `ActionResult` del controller viene usato per cercare un file di visualizzazione denominato *About.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="667a3-168">For example, the *About* `ActionResult` method name of the controller is used to search for a view file named *About.cshtml*.</span></span> <span data-ttu-id="667a3-169">Il runtime cerca prima la visualizzazione nella cartella *Views/[ControllerName]*.</span><span class="sxs-lookup"><span data-stu-id="667a3-169">First, the runtime looks in the *Views/[ControllerName]* folder for the view.</span></span> <span data-ttu-id="667a3-170">Se la cartella non contiene una visualizzazione corrispondente, la ricerca passerà alla cartella *Shared*.</span><span class="sxs-lookup"><span data-stu-id="667a3-170">If it doesn't find a matching view there, it searches the *Shared* folder for the view.</span></span>

<span data-ttu-id="667a3-171">La restituzione implicita di `ViewResult` con `return View();` o il passaggio esplicito del nome della visualizzazione al metodo `View` con `return View("<ViewName>");` non sono rilevanti.</span><span class="sxs-lookup"><span data-stu-id="667a3-171">It doesn't matter if you implicitly return the `ViewResult` with `return View();` or explicitly pass the view name to the `View` method with `return View("<ViewName>");`.</span></span> <span data-ttu-id="667a3-172">In entrambi i casi, l'individuazione delle visualizzazioni cercherà un file di visualizzazione corrispondente in quest'ordine:</span><span class="sxs-lookup"><span data-stu-id="667a3-172">In both cases, view discovery searches for a matching view file in this order:</span></span>

   1. <span data-ttu-id="667a3-173">*Views/\[ControllerName]/\[ViewName].cshtml*</span><span class="sxs-lookup"><span data-stu-id="667a3-173">*Views/\[ControllerName]/\[ViewName].cshtml*</span></span>
   1. <span data-ttu-id="667a3-174">*Views/Shared/\[ViewName].cshtml*</span><span class="sxs-lookup"><span data-stu-id="667a3-174">*Views/Shared/\[ViewName].cshtml*</span></span>

<span data-ttu-id="667a3-175">È possibile fornire il percorso di un file di visualizzazione anziché il nome di una visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="667a3-175">A view file path can be provided instead of a view name.</span></span> <span data-ttu-id="667a3-176">Se si usa un percorso assoluto che inizia alla radice dell'app (che inizia facoltativamente con "/" o "~/") è necessario specificare l'estensione *cshtml*:</span><span class="sxs-lookup"><span data-stu-id="667a3-176">If using an absolute path starting at the app root (optionally starting with "/" or "~/"), the *.cshtml* extension must be specified:</span></span>

```csharp
return View("Views/Home/About.cshtml");
```

<span data-ttu-id="667a3-177">È anche possibile usare un percorso relativo per specificare le visualizzazioni presenti in directory diverse senza l'estensione *cshtml*.</span><span class="sxs-lookup"><span data-stu-id="667a3-177">You can also use a relative path to specify views in different directories without the *.cshtml* extension.</span></span> <span data-ttu-id="667a3-178">All'interno di `HomeController` è possibile restituire la visualizzazione *Index* delle visualizzazioni *Manage* con il percorso relativo:</span><span class="sxs-lookup"><span data-stu-id="667a3-178">Inside the `HomeController`, you can return the *Index* view of your *Manage* views with a relative path:</span></span>

```csharp
return View("../Manage/Index");
```

<span data-ttu-id="667a3-179">In modo analogo, è possibile indicare la directory corrente specifica dei controller con il prefisso "./":</span><span class="sxs-lookup"><span data-stu-id="667a3-179">Similarly, you can indicate the current controller-specific directory with the "./" prefix:</span></span>

```csharp
return View("./About");
```

<span data-ttu-id="667a3-180">Le [visualizzazioni parziali](xref:mvc/views/partial) e i [componenti di visualizzazione](xref:mvc/views/view-components) usano meccanismi di individuazione simili, ma non identici.</span><span class="sxs-lookup"><span data-stu-id="667a3-180">[Partial views](xref:mvc/views/partial) and [view components](xref:mvc/views/view-components) use similar (but not identical) discovery mechanisms.</span></span>

<span data-ttu-id="667a3-181">È possibile personalizzare la convenzione predefinita per la modalità di ricerca delle visualizzazioni all'interno dell'app usando un'interfaccia [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander) personalizzata.</span><span class="sxs-lookup"><span data-stu-id="667a3-181">You can customize the default convention for how views are located within the app by using a custom [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander).</span></span>

<span data-ttu-id="667a3-182">L'individuazione delle visualizzazioni si basa sull'individuazione dei file di visualizzazione in base al nome del file.</span><span class="sxs-lookup"><span data-stu-id="667a3-182">View discovery relies on finding view files by file name.</span></span> <span data-ttu-id="667a3-183">Se il file system sottostante prevede la distinzione tra maiuscole e minuscole, per i nomi delle visualizzazioni verrà probabilmente applicata questa distinzione.</span><span class="sxs-lookup"><span data-stu-id="667a3-183">If the underlying file system is case sensitive, view names are probably case sensitive.</span></span> <span data-ttu-id="667a3-184">Per la compatibilità tra sistemi operativi, rispettare la distinzione maiuscole/minuscole tra i nomi di controller e azioni e i nomi di cartelle e file di visualizzazione associati.</span><span class="sxs-lookup"><span data-stu-id="667a3-184">For compatibility across operating systems, match case between controller and action names and associated view folders and file names.</span></span> <span data-ttu-id="667a3-185">In caso di errore relativo a un file di visualizzazione non trovato durante l'uso di un file system con distinzione tra maiuscole e minuscole, verificare che tra il nome del file di visualizzazione richiesto e il nome effettivo del file di visualizzazione l'uso delle maiuscole e minuscole corrisponda.</span><span class="sxs-lookup"><span data-stu-id="667a3-185">If you encounter an error that a view file can't be found while working with a case-sensitive file system, confirm that the casing matches between the requested view file and the actual view file name.</span></span>

<span data-ttu-id="667a3-186">Per manutenibilità e chiarezza, seguire le procedure consigliate relative all'organizzazione della struttura di file per le visualizzazioni in modo da riflettere le relazioni tra controller, azioni e visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="667a3-186">Follow the best practice of organizing the file structure for your views to reflect the relationships among controllers, actions, and views for maintainability and clarity.</span></span>

## <a name="passing-data-to-views"></a><span data-ttu-id="667a3-187">Passaggio dei dati alle visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="667a3-187">Passing data to views</span></span>

<span data-ttu-id="667a3-188">Passare i dati alle visualizzazioni usando diversi approcci:</span><span class="sxs-lookup"><span data-stu-id="667a3-188">Pass data to views using several approaches:</span></span>

* <span data-ttu-id="667a3-189">Dati fortemente tipizzati: viewmodel</span><span class="sxs-lookup"><span data-stu-id="667a3-189">Strongly typed data: viewmodel</span></span>
* <span data-ttu-id="667a3-190">Dati con tipizzazione debole</span><span class="sxs-lookup"><span data-stu-id="667a3-190">Weakly typed data</span></span>
  * <span data-ttu-id="667a3-191">`ViewData` (`ViewDataAttribute`)</span><span class="sxs-lookup"><span data-stu-id="667a3-191">`ViewData` (`ViewDataAttribute`)</span></span>
  * `ViewBag`

### <a name="strongly-typed-data-viewmodel"></a><span data-ttu-id="667a3-192">Dati fortemente tipizzati: (viewmodel)</span><span class="sxs-lookup"><span data-stu-id="667a3-192">Strongly typed data (viewmodel)</span></span>

<span data-ttu-id="667a3-193">L'approccio più efficace consiste nello specificare un tipo di [modello](xref:mvc/models/model-binding) nella visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="667a3-193">The most robust approach is to specify a [model](xref:mvc/models/model-binding) type in the view.</span></span> <span data-ttu-id="667a3-194">Questo modello è comunemente noto come *viewmodel*.</span><span class="sxs-lookup"><span data-stu-id="667a3-194">This model is commonly referred to as a *viewmodel*.</span></span> <span data-ttu-id="667a3-195">Un'istanza del tipo viewmodel viene passato alla visualizzazione dall'azione.</span><span class="sxs-lookup"><span data-stu-id="667a3-195">You pass an instance of the viewmodel type to the view from the action.</span></span>

<span data-ttu-id="667a3-196">L'uso di un viewmodel per passare i dati a una visualizzazione consente a quest'ultima di trarre vantaggio dal controllo con tipizzazione *forte*.</span><span class="sxs-lookup"><span data-stu-id="667a3-196">Using a viewmodel to pass data to a view allows the view to take advantage of *strong* type checking.</span></span> <span data-ttu-id="667a3-197">L'espressione *tipizzazione forte* o *fortemente tipizzato* indica che ogni variabile e costante ha un tipo definito in modo esplicito, ad esempio `string`, `int` o `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="667a3-197">*Strong typing* (or *strongly typed*) means that every variable and constant has an explicitly defined type (for example, `string`, `int`, or `DateTime`).</span></span> <span data-ttu-id="667a3-198">La validità dei tipi usati in una visualizzazione viene verificata in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="667a3-198">The validity of types used in a view is checked at compile time.</span></span>

<span data-ttu-id="667a3-199">In [Visual Studio](https://visualstudio.microsoft.com) e [Visual Studio Code](https://code.visualstudio.com/) i membri delle classi fortemente tipizzati vengono elencati usando una funzionalità denominata [IntelliSense](/visualstudio/ide/using-intellisense).</span><span class="sxs-lookup"><span data-stu-id="667a3-199">[Visual Studio](https://visualstudio.microsoft.com) and [Visual Studio Code](https://code.visualstudio.com/) list strongly typed class members using a feature called [IntelliSense](/visualstudio/ide/using-intellisense).</span></span> <span data-ttu-id="667a3-200">Per visualizzare le proprietà di un elemento viewmodel, digitare il nome della variabile per l'elemento viewmodel seguito da un punto (`.`).</span><span class="sxs-lookup"><span data-stu-id="667a3-200">When you want to see the properties of a viewmodel, type the variable name for the viewmodel followed by a period (`.`).</span></span> <span data-ttu-id="667a3-201">Ciò consente di scrivere il codice più velocemente con un minor numero di errori.</span><span class="sxs-lookup"><span data-stu-id="667a3-201">This helps you write code faster with fewer errors.</span></span>

<span data-ttu-id="667a3-202">Specificare un modello usando la direttiva `@model`.</span><span class="sxs-lookup"><span data-stu-id="667a3-202">Specify a model using the `@model` directive.</span></span> <span data-ttu-id="667a3-203">Usare il modello con `@Model`:</span><span class="sxs-lookup"><span data-stu-id="667a3-203">Use the model with `@Model`:</span></span>

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

<span data-ttu-id="667a3-204">Per fornire il modello alla visualizzazione, il controller lo passa come parametro:</span><span class="sxs-lookup"><span data-stu-id="667a3-204">To provide the model to the view, the controller passes it as a parameter:</span></span>

```csharp
public IActionResult Contact()
{
    ViewData["Message"] = "Your contact page.";

    var viewModel = new Address()
    {
        Name = "Microsoft",
        Street = "One Microsoft Way",
        City = "Redmond",
        State = "WA",
        PostalCode = "98052-6399"
    };

    return View(viewModel);
}
```

<span data-ttu-id="667a3-205">Non ci sono limitazioni ai tipi di modello che è possibile fornire a una visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="667a3-205">There are no restrictions on the model types that you can provide to a view.</span></span> <span data-ttu-id="667a3-206">È consigliabile usare viewmodel Plain Old CLR Object (POCO) per i quali non siano stati definiti comportamenti (metodi) o ne siano stati definiti solo alcuni.</span><span class="sxs-lookup"><span data-stu-id="667a3-206">We recommend using Plain Old CLR Object (POCO) viewmodels with little or no behavior (methods) defined.</span></span> <span data-ttu-id="667a3-207">Le classi viewmodel sono in genere archiviate nella cartella *Models* o in una cartella *ViewModels* separata alla radice dell'app.</span><span class="sxs-lookup"><span data-stu-id="667a3-207">Usually, viewmodel classes are either stored in the *Models* folder or a separate *ViewModels* folder at the root of the app.</span></span> <span data-ttu-id="667a3-208">Il viewmodel *Address* usato nell'esempio precedente è un viewmodel POCO archiviato in un file denominato *Address.cs*:</span><span class="sxs-lookup"><span data-stu-id="667a3-208">The *Address* viewmodel used in the example above is a POCO viewmodel stored in a file named *Address.cs*:</span></span>

```csharp
namespace WebApplication1.ViewModels
{
    public class Address
    {
        public string Name { get; set; }
        public string Street { get; set; }
        public string City { get; set; }
        public string State { get; set; }
        public string PostalCode { get; set; }
    }
}
```

<span data-ttu-id="667a3-209">Nulla impedisce di usare le stesse classi per i tipi viewmodel e i tipi del modello aziendale.</span><span class="sxs-lookup"><span data-stu-id="667a3-209">Nothing prevents you from using the same classes for both your viewmodel types and your business model types.</span></span> <span data-ttu-id="667a3-210">L'uso di modelli separati consente tuttavia la variazione delle visualizzazioni indipendentemente dalle parti relative alla logica di business e all'accesso ai dati dell'app.</span><span class="sxs-lookup"><span data-stu-id="667a3-210">However, using separate models allows your views to vary independently from the business logic and data access parts of your app.</span></span> <span data-ttu-id="667a3-211">La separazione di modelli e viewmodel offre anche vantaggi in termini di sicurezza quando i modelli usano l'[associazione di modelli](xref:mvc/models/model-binding) e la [convalida](xref:mvc/models/validation) per i dati inviati all'app dall'utente.</span><span class="sxs-lookup"><span data-stu-id="667a3-211">Separation of models and viewmodels also offers security benefits when models use [model binding](xref:mvc/models/model-binding) and [validation](xref:mvc/models/validation) for data sent to the app by the user.</span></span>

<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-viewdata-attribute-and-viewbag"></a><span data-ttu-id="667a3-212">Dati con tipizzazione debole (ViewData, attributo ViewData e ViewBag)</span><span class="sxs-lookup"><span data-stu-id="667a3-212">Weakly typed data (ViewData, ViewData attribute, and ViewBag)</span></span>

<span data-ttu-id="667a3-213">`ViewBag`*non è disponibile in Razor Pagine.*</span><span class="sxs-lookup"><span data-stu-id="667a3-213">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="667a3-214">Oltre alle visualizzazioni fortemente tipizzate, le visualizzazioni hanno accesso a una raccolta di dati *con tipizzazione debole* o *debolmente tipizzati*.</span><span class="sxs-lookup"><span data-stu-id="667a3-214">In addition to strongly typed views, views have access to a *weakly typed* (also called *loosely typed*) collection of data.</span></span> <span data-ttu-id="667a3-215">A differenza della tipizzazione forte, la *tipizzazione debole*, o l'espressione *debolmente tipizzato*, indica che il tipo di dati in uso non viene dichiarato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="667a3-215">Unlike strong types, *weak types* (or *loose types*) means that you don't explicitly declare the type of data you're using.</span></span> <span data-ttu-id="667a3-216">È possibile usare la raccolta di dati con tipizzazione debole per passare piccole quantità di dati da e verso i controller e le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="667a3-216">You can use the collection of weakly typed data for passing small amounts of data in and out of controllers and views.</span></span>

| <span data-ttu-id="667a3-217">Passaggio dei dati tra...</span><span class="sxs-lookup"><span data-stu-id="667a3-217">Passing data between a ...</span></span>                        | <span data-ttu-id="667a3-218">Esempio</span><span class="sxs-lookup"><span data-stu-id="667a3-218">Example</span></span>                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| <span data-ttu-id="667a3-219">Un controller e una visualizzazione</span><span class="sxs-lookup"><span data-stu-id="667a3-219">Controller and a view</span></span>                             | <span data-ttu-id="667a3-220">Popolamento di dati in un elenco a discesa.</span><span class="sxs-lookup"><span data-stu-id="667a3-220">Populating a dropdown list with data.</span></span>                                          |
| <span data-ttu-id="667a3-221">Una visualizzazione e una [visualizzazione Layout](xref:mvc/views/layout)</span><span class="sxs-lookup"><span data-stu-id="667a3-221">View and a [layout view](xref:mvc/views/layout)</span></span>   | <span data-ttu-id="667a3-222">Impostazione del **\<title>** contenuto dell'elemento nella visualizzazione layout da un file di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="667a3-222">Setting the **\<title>** element content in the layout view from a view file.</span></span>  |
| <span data-ttu-id="667a3-223">Una [visualizzazione parziale](xref:mvc/views/partial) e una visualizzazione</span><span class="sxs-lookup"><span data-stu-id="667a3-223">[Partial view](xref:mvc/views/partial) and a view</span></span> | <span data-ttu-id="667a3-224">Widget che visualizza i dati in base alla pagina Web richiesta dall'utente.</span><span class="sxs-lookup"><span data-stu-id="667a3-224">A widget that displays data based on the webpage that the user requested.</span></span>      |

<span data-ttu-id="667a3-225">È possibile fare riferimento a questa raccolta tramite le proprietà `ViewData` o `ViewBag` nei controller e nelle visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="667a3-225">This collection can be referenced through either the `ViewData` or `ViewBag` properties on controllers and views.</span></span> <span data-ttu-id="667a3-226">La proprietà `ViewData` è un dizionario di oggetti con tipizzazione debole.</span><span class="sxs-lookup"><span data-stu-id="667a3-226">The `ViewData` property is a dictionary of weakly typed objects.</span></span> <span data-ttu-id="667a3-227">La proprietà `ViewBag` è un wrapper di `ViewData` che offre proprietà dinamiche per la raccolta `ViewData` sottostante.</span><span class="sxs-lookup"><span data-stu-id="667a3-227">The `ViewBag` property is a wrapper around `ViewData` that provides dynamic properties for the underlying `ViewData` collection.</span></span> <span data-ttu-id="667a3-228">Nota: le ricerche chiave non fanno distinzione tra maiuscole e minuscole sia per `ViewData` che per `ViewBag` .</span><span class="sxs-lookup"><span data-stu-id="667a3-228">Note: Key lookups are case-insensitive for both `ViewData` and `ViewBag`.</span></span>

<span data-ttu-id="667a3-229">`ViewData` e `ViewBag` vengono risolte in modo dinamico in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="667a3-229">`ViewData` and `ViewBag` are dynamically resolved at runtime.</span></span> <span data-ttu-id="667a3-230">Poiché non offrono il controllo del tipo in fase di compilazione, entrambe sono in genere più soggette a errori rispetto all'uso di un elemento viewmodel.</span><span class="sxs-lookup"><span data-stu-id="667a3-230">Since they don't offer compile-time type checking, both are generally more error-prone than using a viewmodel.</span></span> <span data-ttu-id="667a3-231">Per questo motivo, alcuni sviluppatori preferiscono non usare mai `ViewData` e `ViewBag` o usarle il meno possibile.</span><span class="sxs-lookup"><span data-stu-id="667a3-231">For that reason, some developers prefer to minimally or never use `ViewData` and `ViewBag`.</span></span>

<a name="VD"></a>

<span data-ttu-id="667a3-232">**ViewData**</span><span class="sxs-lookup"><span data-stu-id="667a3-232">**ViewData**</span></span>

<span data-ttu-id="667a3-233">`ViewData` è un oggetto [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) a cui si accede tramite le chiavi `string`.</span><span class="sxs-lookup"><span data-stu-id="667a3-233">`ViewData` is a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) object accessed through `string` keys.</span></span> <span data-ttu-id="667a3-234">I dati di tipo stringa possono essere archiviati e usati direttamente, senza la necessità di un cast, ma è necessario eseguire il cast di altri valori dell'oggetto `ViewData` in tipi specifici quando vengono estratti.</span><span class="sxs-lookup"><span data-stu-id="667a3-234">String data can be stored and used directly without the need for a cast, but you must cast other `ViewData` object values to specific types when you extract them.</span></span> <span data-ttu-id="667a3-235">È possibile usare `ViewData` per passare i dati dai controller alle visualizzazioni e al loro interno, inclusi [visualizzazioni parziali](xref:mvc/views/partial) e [layout](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="667a3-235">You can use `ViewData` to pass data from controllers to views and within views, including [partial views](xref:mvc/views/partial) and [layouts](xref:mvc/views/layout).</span></span>

<span data-ttu-id="667a3-236">Nell'esempio seguente vengono impostati i valori per una formula di saluto e un indirizzo usando `ViewData` in un'azione:</span><span class="sxs-lookup"><span data-stu-id="667a3-236">The following is an example that sets values for a greeting and an address using `ViewData` in an action:</span></span>

```csharp
public IActionResult SomeAction()
{
    ViewData["Greeting"] = "Hello";
    ViewData["Address"]  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

<span data-ttu-id="667a3-237">Lavorare con i dati in una visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="667a3-237">Work with the data in a view:</span></span>

```cshtml
@{
    // Since Address isn't a string, it requires a cast.
    var address = ViewData["Address"] as Address;
}

@ViewData["Greeting"] World!

<address>
    @address.Name<br>
    @address.Street<br>
    @address.City, @address.State @address.PostalCode
</address>
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="667a3-238">**Attributo viewData**</span><span class="sxs-lookup"><span data-stu-id="667a3-238">**ViewData attribute**</span></span>

<span data-ttu-id="667a3-239">Un altro approccio che usa [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) è [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="667a3-239">Another approach that uses the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) is [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="667a3-240">I valori delle proprietà nei controller o nei Razor modelli di pagina contrassegnati con l' `[ViewData]` attributo sono archiviati e caricati dal dizionario.</span><span class="sxs-lookup"><span data-stu-id="667a3-240">Properties on controllers or Razor Page models marked with the `[ViewData]` attribute have their values stored and loaded from the dictionary.</span></span>

<span data-ttu-id="667a3-241">Nell'esempio seguente il controller Home contiene una `Title` proprietà contrassegnata con `[ViewData]` .</span><span class="sxs-lookup"><span data-stu-id="667a3-241">In the following example, the Home controller contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="667a3-242">Il metodo `About` imposta il titolo per la visualizzazione About (Informazioni):</span><span class="sxs-lookup"><span data-stu-id="667a3-242">The `About` method sets the title for the About view:</span></span>

```csharp
public class HomeController : Controller
{
    [ViewData]
    public string Title { get; set; }

    public IActionResult About()
    {
        Title = "About Us";
        ViewData["Message"] = "Your application description page.";

        return View();
    }
}
```

<span data-ttu-id="667a3-243">Nel layout il titolo viene letto dal dizionario ViewData:</span><span class="sxs-lookup"><span data-stu-id="667a3-243">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

<span data-ttu-id="667a3-244">**ViewBag**</span><span class="sxs-lookup"><span data-stu-id="667a3-244">**ViewBag**</span></span>

<span data-ttu-id="667a3-245">`ViewBag`*non è disponibile in Razor Pagine.*</span><span class="sxs-lookup"><span data-stu-id="667a3-245">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="667a3-246">`ViewBag`è un oggetto [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) che fornisce l'accesso dinamico agli oggetti archiviati in `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="667a3-246">`ViewBag` is a [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) object that provides dynamic access to the objects stored in `ViewData`.</span></span> <span data-ttu-id="667a3-247">`ViewBag` può risultare più comodo da usare poiché non richiede l'esecuzione del cast.</span><span class="sxs-lookup"><span data-stu-id="667a3-247">`ViewBag` can be more convenient to work with, since it doesn't require casting.</span></span> <span data-ttu-id="667a3-248">Nell'esempio seguente viene illustrato come usare `ViewBag` con lo stesso risultato che si ottiene con l'uso di `ViewData` descritto in precedenza:</span><span class="sxs-lookup"><span data-stu-id="667a3-248">The following example shows how to use `ViewBag` with the same result as using `ViewData` above:</span></span>

```csharp
public IActionResult SomeAction()
{
    ViewBag.Greeting = "Hello";
    ViewBag.Address  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

```cshtml
@ViewBag.Greeting World!

<address>
    @ViewBag.Address.Name<br>
    @ViewBag.Address.Street<br>
    @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
</address>
```

<span data-ttu-id="667a3-249">**Uso simultaneo di ViewData e ViewBag**</span><span class="sxs-lookup"><span data-stu-id="667a3-249">**Using ViewData and ViewBag simultaneously**</span></span>

<span data-ttu-id="667a3-250">`ViewBag`*non è disponibile in Razor Pagine.*</span><span class="sxs-lookup"><span data-stu-id="667a3-250">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="667a3-251">Dal momento che `ViewData` e `ViewBag` fanno riferimento alla stessa raccolta `ViewData` sottostante, è possibile usare `ViewData` e `ViewBag` e combinarle durante la lettura e la scrittura dei valori.</span><span class="sxs-lookup"><span data-stu-id="667a3-251">Since `ViewData` and `ViewBag` refer to the same underlying `ViewData` collection, you can use both `ViewData` and `ViewBag` and mix and match between them when reading and writing values.</span></span>

<span data-ttu-id="667a3-252">Impostare il titolo usando `ViewBag` e la descrizione usando `ViewData` all'inizio di una visualizzazione *About.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="667a3-252">Set the title using `ViewBag` and the description using `ViewData` at the top of an *About.cshtml* view:</span></span>

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

<span data-ttu-id="667a3-253">Leggere le proprietà ma invertire l'uso di `ViewData` e `ViewBag`.</span><span class="sxs-lookup"><span data-stu-id="667a3-253">Read the properties but reverse the use of `ViewData` and `ViewBag`.</span></span> <span data-ttu-id="667a3-254">Nel file *_Layout.cshtml* ottenere il titolo usando `ViewData` e ottenere la descrizione usando `ViewBag`:</span><span class="sxs-lookup"><span data-stu-id="667a3-254">In the *_Layout.cshtml* file, obtain the title using `ViewData` and obtain the description using `ViewBag`:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

<span data-ttu-id="667a3-255">Tenere presente che per `ViewData` non è necessario eseguire il cast delle stringhe.</span><span class="sxs-lookup"><span data-stu-id="667a3-255">Remember that strings don't require a cast for `ViewData`.</span></span> <span data-ttu-id="667a3-256">È possibile usare `@ViewData["Title"]` senza eseguire il cast.</span><span class="sxs-lookup"><span data-stu-id="667a3-256">You can use `@ViewData["Title"]` without casting.</span></span>

<span data-ttu-id="667a3-257">L'uso simultaneo di `ViewData` e `ViewBag` funziona, così come funziona la combinazione di entrambe durante la lettura e la scrittura delle proprietà.</span><span class="sxs-lookup"><span data-stu-id="667a3-257">Using both `ViewData` and `ViewBag` at the same time works, as does mixing and matching reading and writing the properties.</span></span> <span data-ttu-id="667a3-258">Viene eseguito il rendering del markup seguente:</span><span class="sxs-lookup"><span data-stu-id="667a3-258">The following markup is rendered:</span></span>

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

<span data-ttu-id="667a3-259">**Riepilogo delle differenze tra ViewData e ViewBag**</span><span class="sxs-lookup"><span data-stu-id="667a3-259">**Summary of the differences between ViewData and ViewBag**</span></span>

 <span data-ttu-id="667a3-260">`ViewBag`non è disponibile nelle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="667a3-260">`ViewBag` isn't available in the Razor Pages.</span></span>

* `ViewData`
  * <span data-ttu-id="667a3-261">Deriva da [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), quindi presenta proprietà del dizionario che possono essere utili, ad esempio `ContainsKey` ,, `Add` `Remove` e `Clear` .</span><span class="sxs-lookup"><span data-stu-id="667a3-261">Derives from [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), so it has dictionary properties that can be useful, such as `ContainsKey`, `Add`, `Remove`, and `Clear`.</span></span>
  * <span data-ttu-id="667a3-262">Le chiavi nel dizionario sono stringhe, pertanto lo spazio vuoto è consentito.</span><span class="sxs-lookup"><span data-stu-id="667a3-262">Keys in the dictionary are strings, so whitespace is allowed.</span></span> <span data-ttu-id="667a3-263">Esempio: `ViewData["Some Key With Whitespace"]`</span><span class="sxs-lookup"><span data-stu-id="667a3-263">Example: `ViewData["Some Key With Whitespace"]`</span></span>
  * <span data-ttu-id="667a3-264">Per usare `ViewData` è necessario eseguire il cast di tutti i tipi diversi da `string` nella visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="667a3-264">Any type other than a `string` must be cast in the view to use `ViewData`.</span></span>
* `ViewBag`
  * <span data-ttu-id="667a3-265">Deriva da [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) e consente quindi la creazione di proprietà dinamiche usando la notazione del punto (`@ViewBag.SomeKey = <value or object>`). L'esecuzione del cast non è necessaria.</span><span class="sxs-lookup"><span data-stu-id="667a3-265">Derives from [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), so it allows the creation of dynamic properties using dot notation (`@ViewBag.SomeKey = <value or object>`), and no casting is required.</span></span> <span data-ttu-id="667a3-266">La sintassi di `ViewBag` velocizza l'aggiunta in controller e visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="667a3-266">The syntax of `ViewBag` makes it quicker to add to controllers and views.</span></span>
  * <span data-ttu-id="667a3-267">Verificare la presenza di valori Null è più facile.</span><span class="sxs-lookup"><span data-stu-id="667a3-267">Simpler to check for null values.</span></span> <span data-ttu-id="667a3-268">Esempio: `@ViewBag.Person?.Name`</span><span class="sxs-lookup"><span data-stu-id="667a3-268">Example: `@ViewBag.Person?.Name`</span></span>

<span data-ttu-id="667a3-269">**Quando usare ViewData o ViewBag**</span><span class="sxs-lookup"><span data-stu-id="667a3-269">**When to use ViewData or ViewBag**</span></span>

<span data-ttu-id="667a3-270">`ViewData` e `ViewBag` sono entrambi approcci ugualmente validi per passare piccole quantità di dati tra controller e visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="667a3-270">Both `ViewData` and `ViewBag` are equally valid approaches for passing small amounts of data among controllers and views.</span></span> <span data-ttu-id="667a3-271">La scelta di quello da usare è basata sulla preferenza.</span><span class="sxs-lookup"><span data-stu-id="667a3-271">The choice of which one to use is based on preference.</span></span> <span data-ttu-id="667a3-272">È possibile combinare oggetti `ViewData` e `ViewBag`. La lettura e la gestione del codice risulteranno tuttavia più semplici con un unico approccio usato in modo coerente.</span><span class="sxs-lookup"><span data-stu-id="667a3-272">You can mix and match `ViewData` and `ViewBag` objects, however, the code is easier to read and maintain with one approach used consistently.</span></span> <span data-ttu-id="667a3-273">Entrambi gli approcci vengono risolti in modo dinamico in fase di esecuzione e possono quindi determinare errori di runtime.</span><span class="sxs-lookup"><span data-stu-id="667a3-273">Both approaches are dynamically resolved at runtime and thus prone to causing runtime errors.</span></span> <span data-ttu-id="667a3-274">Alcuni team di sviluppo li evitano.</span><span class="sxs-lookup"><span data-stu-id="667a3-274">Some development teams avoid them.</span></span>

### <a name="dynamic-views"></a><span data-ttu-id="667a3-275">Visualizzazioni dinamiche</span><span class="sxs-lookup"><span data-stu-id="667a3-275">Dynamic views</span></span>

<span data-ttu-id="667a3-276">Le visualizzazioni che non dichiarano un tipo di modello usando `@model` ma a cui è stata passata l'istanza di un modello, ad esempio `return View(Address);`, possono fare riferimento alle proprietà dell'istanza in modo dinamico:</span><span class="sxs-lookup"><span data-stu-id="667a3-276">Views that don't declare a model type using `@model` but that have a model instance passed to them (for example, `return View(Address);`) can reference the instance's properties dynamically:</span></span>

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

<span data-ttu-id="667a3-277">Questa funzionalità offre flessibilità ma non offre la protezione della compilazione o IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="667a3-277">This feature offers flexibility but doesn't offer compilation protection or IntelliSense.</span></span> <span data-ttu-id="667a3-278">Se la proprietà non esiste, in fase di esecuzione la generazione della pagina Web non riesce.</span><span class="sxs-lookup"><span data-stu-id="667a3-278">If the property doesn't exist, webpage generation fails at runtime.</span></span>

## <a name="more-view-features"></a><span data-ttu-id="667a3-279">Altre funzionalità delle visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="667a3-279">More view features</span></span>

<span data-ttu-id="667a3-280">Gli [helper tag](xref:mvc/views/tag-helpers/intro) semplificano l'aggiunta del comportamento sul lato server ai tag HTML esistenti.</span><span class="sxs-lookup"><span data-stu-id="667a3-280">[Tag Helpers](xref:mvc/views/tag-helpers/intro) make it easy to add server-side behavior to existing HTML tags.</span></span> <span data-ttu-id="667a3-281">Grazie all'uso degli helper tag, è possibile evitare di scrivere codice personalizzato o helper all'interno delle visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="667a3-281">Using Tag Helpers avoids the need to write custom code or helpers within your views.</span></span> <span data-ttu-id="667a3-282">Gli helper tag vengono applicati come attributi agli elementi HTML e vengono ignorati dagli editor che non sono in grado di elaborarli.</span><span class="sxs-lookup"><span data-stu-id="667a3-282">Tag helpers are applied as attributes to HTML elements and are ignored by editors that can't process them.</span></span> <span data-ttu-id="667a3-283">Ciò consente di modificare ed eseguire il rendering del markup delle visualizzazioni in un'ampia gamma di strumenti.</span><span class="sxs-lookup"><span data-stu-id="667a3-283">This allows you to edit and render view markup in a variety of tools.</span></span>

<span data-ttu-id="667a3-284">La generazione di markup HTML personalizzato può essere ottenuta con molti helper HTML predefiniti.</span><span class="sxs-lookup"><span data-stu-id="667a3-284">Generating custom HTML markup can be achieved with many built-in HTML Helpers.</span></span> <span data-ttu-id="667a3-285">Una logica dell'interfaccia utente più complessa può essere gestita dai [componenti di visualizzazione](xref:mvc/views/view-components).</span><span class="sxs-lookup"><span data-stu-id="667a3-285">More complex user interface logic can be handled by [View Components](xref:mvc/views/view-components).</span></span> <span data-ttu-id="667a3-286">I componenti di visualizzazione offrono lo stesso tipo di progettazione SoC offerta dai controller e dalle visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="667a3-286">View components provide the same SoC that controllers and views offer.</span></span> <span data-ttu-id="667a3-287">Possono eliminare la necessità di azioni e visualizzazioni che gestiscono i dati usati da elementi comuni dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="667a3-287">They can eliminate the need for actions and views that deal with data used by common user interface elements.</span></span>

<span data-ttu-id="667a3-288">Come molti altri aspetti di ASP.NET Core, le visualizzazioni supportano l'[inserimento di dipendenze](xref:fundamentals/dependency-injection), consentendo che i servizi siano [inseriti nelle visualizzazioni](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="667a3-288">Like many other aspects of ASP.NET Core, views support [dependency injection](xref:fundamentals/dependency-injection), allowing services to be [injected into views](xref:mvc/views/dependency-injection).</span></span>
