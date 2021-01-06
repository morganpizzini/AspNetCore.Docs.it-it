---
title: "Parte 3: aggiungere una visualizzazione a un'app MVC ASP.NET Core"
author: rick-anderson
description: Parte 3 della serie di esercitazioni su ASP.NET Core MVC.
ms.author: riande
ms.date: 11/16/2019
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
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: ce2754d22b61578bdbe5e78a69e08bdc1e83190d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "94688352"
---
# <a name="part-3-add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="70c41-103">Parte 3: aggiungere una visualizzazione a un'app MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="70c41-103">Part 3, add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="70c41-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="70c41-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="70c41-105">In questa sezione viene modificata la `HelloWorldController` classe per l'utilizzo [Razor](xref:mvc/views/razor) dei file di visualizzazione per incapsulare in modo semplice il processo di generazione di risposte HTML a un client.</span><span class="sxs-lookup"><span data-stu-id="70c41-105">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="70c41-106">Si crea un file di modello di visualizzazione utilizzando Razor .</span><span class="sxs-lookup"><span data-stu-id="70c41-106">You create a view template file using Razor.</span></span> <span data-ttu-id="70c41-107">Razori modelli di vista basati su hanno un'estensione di file *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="70c41-107">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="70c41-108">Consentono di creare l'output HTML in modo accurato con C#.</span><span class="sxs-lookup"><span data-stu-id="70c41-108">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="70c41-109">Attualmente il metodo `Index` restituisce una stringa con un messaggio hardcoded nella classe controller.</span><span class="sxs-lookup"><span data-stu-id="70c41-109">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="70c41-110">Nella classe `HelloWorldController` sostituire il metodo `Index` con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="70c41-110">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="70c41-111">Il codice precedente chiama il metodo <xref:Microsoft.AspNetCore.Mvc.Controller.View*> del controller.</span><span class="sxs-lookup"><span data-stu-id="70c41-111">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="70c41-112">Usa un modello di visualizzazione per generare una risposta HTML.</span><span class="sxs-lookup"><span data-stu-id="70c41-112">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="70c41-113">I metodi del controller, noti anche come *metodi di azione*, ad esempio il metodo `Index` descritto in precedenza, restituiscono in genere un <xref:Microsoft.AspNetCore.Mvc.IActionResult> (o una classe derivata da <xref:Microsoft.AspNetCore.Mvc.ActionResult>) e non un tipo come `string`.</span><span class="sxs-lookup"><span data-stu-id="70c41-113">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="70c41-114">Aggiungere una vista</span><span class="sxs-lookup"><span data-stu-id="70c41-114">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="70c41-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="70c41-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="70c41-116">Fare clic con il pulsante destro del mouse sulla cartella *Views*, quindi su **Aggiungi > Nuova cartella** e denominare la cartella *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="70c41-116">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="70c41-117">Fare clic con il pulsante destro del mouse sulla cartella *Views/HelloWorld*, quindi su **Aggiungi > Nuovo elemento**.</span><span class="sxs-lookup"><span data-stu-id="70c41-117">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="70c41-118">Nella finestra di dialogo **Aggiungi nuovo elemento - MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="70c41-118">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="70c41-119">Nella casella di ricerca in alto a destra immettere *view* (vista)</span><span class="sxs-lookup"><span data-stu-id="70c41-119">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="70c41-120">Selezione **Razor visualizzazione**</span><span class="sxs-lookup"><span data-stu-id="70c41-120">Select **Razor View**</span></span>

  * <span data-ttu-id="70c41-121">Mantenere il valore della casella **Nome**, *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="70c41-121">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="70c41-122">Selezionare **Aggiungi**</span><span class="sxs-lookup"><span data-stu-id="70c41-122">Select **Add**</span></span>

![Finestra di dialogo Aggiungi nuovo elemento](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="70c41-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="70c41-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="70c41-125">Aggiungere una vista `Index` per `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="70c41-125">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="70c41-126">Aggiungere una nuova cartella denominata *Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="70c41-126">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="70c41-127">Aggiungere un nuovo file al nome di cartella *Views/HelloWorld\*\*Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="70c41-127">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="70c41-128">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="70c41-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="70c41-129">Fare clic con il pulsante destro del mouse sulla cartella *Views*, quindi su **Aggiungi > Nuova cartella** e denominare la cartella *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="70c41-129">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="70c41-130">Fare clic con il pulsante destro del mouse sulla cartella *Views/HelloWorld*, quindi su **Aggiungi > Nuovo file**.</span><span class="sxs-lookup"><span data-stu-id="70c41-130">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="70c41-131">Nel finestra di dialogo **Nuovo file**:</span><span class="sxs-lookup"><span data-stu-id="70c41-131">In the **New File** dialog:</span></span>

  * <span data-ttu-id="70c41-132">Selezionare **ASP .NET Core** nel riquadro sinistro.</span><span class="sxs-lookup"><span data-stu-id="70c41-132">Select **ASP .NET Core** in the left pane.</span></span>
  * <span data-ttu-id="70c41-133">Selezionare **Razor Visualizza** nel riquadro centrale.</span><span class="sxs-lookup"><span data-stu-id="70c41-133">Select **Razor View** in the center pane.</span></span>
  * <span data-ttu-id="70c41-134">Digitare *index* nella casella **nome** .</span><span class="sxs-lookup"><span data-stu-id="70c41-134">Type *Index* in the **Name** box.</span></span>
  * <span data-ttu-id="70c41-135">Selezionare **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="70c41-135">Select **New**.</span></span>

![Finestra di dialogo Aggiungi nuovo elemento](adding-view/_static/add_view_macVSM8.9.png)

---

<span data-ttu-id="70c41-137">Sostituire il contenuto del file di visualizzazione *views/HelloWorld/index. cshtml* Razor con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="70c41-137">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="70c41-138">Accedere a `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="70c41-138">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="70c41-139">Il metodo `Index` in `HelloWorldController` non ha eseguito molte operazioni; ha eseguito l'istruzione `return View();` che ha specificato che il metodo deve usare un file di modello della vista per eseguire il rendering di una risposta al browser.</span><span class="sxs-lookup"><span data-stu-id="70c41-139">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="70c41-140">Poiché non è stato specificato un nome di file di modello di visualizzazione, per impostazione predefinita MVC usa il file di visualizzazione predefinito.</span><span class="sxs-lookup"><span data-stu-id="70c41-140">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="70c41-141">Il file di visualizzazione predefinito ha lo stesso nome del metodo ( `Index` ), quindi viene usato il modello di visualizzazione in */views/HelloWorld/index.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="70c41-141">The default view file has the same name as the method (`Index`), so the view template in */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="70c41-142">L'immagine seguente mostra la stringa "Hello from our View Template!"</span><span class="sxs-lookup"><span data-stu-id="70c41-142">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="70c41-143">hardcoded nella vista.</span><span class="sxs-lookup"><span data-stu-id="70c41-143">hard-coded in the view.</span></span>

![Finestra del browser](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="70c41-145">Modificare le viste e le pagine di layout</span><span class="sxs-lookup"><span data-stu-id="70c41-145">Change views and layout pages</span></span>

<span data-ttu-id="70c41-146">Selezionare i collegamenti di menu: **MvcMovie**, **Home** e **Privacy**.</span><span class="sxs-lookup"><span data-stu-id="70c41-146">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="70c41-147">Ogni pagina mostra lo stesso layout di menu.</span><span class="sxs-lookup"><span data-stu-id="70c41-147">Each page shows the same menu layout.</span></span> <span data-ttu-id="70c41-148">Il layout di menu viene implementato nel file *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="70c41-148">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="70c41-149">Aprire il file *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="70c41-149">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="70c41-150">I modelli di [layout](xref:mvc/views/layout) consentono di specificare il layout del contenitore HTML del sito in un'unica posizione e quindi di applicarlo in più pagine del sito.</span><span class="sxs-lookup"><span data-stu-id="70c41-150">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="70c41-151">Trovare la riga `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="70c41-151">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="70c41-152">`RenderBody` è un segnaposto dove tutte le pagine specifiche della vista vengono presentate, *incapsulate* nella pagina di layout.</span><span class="sxs-lookup"><span data-stu-id="70c41-152">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="70c41-153">Ad esempio, se si seleziona il collegamento **Privacy**, il rendering della vista **Views/Home/Privacy.cshtml** viene eseguito all'interno del metodo `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="70c41-153">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="70c41-154">Modificare il titolo, il piè di pagina e il collegamento di menu nel file di layout</span><span class="sxs-lookup"><span data-stu-id="70c41-154">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="70c41-155">Sostituire il contenuto del file *Views/Shared/_Layout. cshtml* con il markup seguente.</span><span class="sxs-lookup"><span data-stu-id="70c41-155">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="70c41-156">Le modifiche sono evidenziate:</span><span class="sxs-lookup"><span data-stu-id="70c41-156">The changes are highlighted:</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Views/Shared/_Layout.cshtml?highlight=6,14,40)]
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="70c41-157">Il markup precedente ha apportato le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="70c41-157">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="70c41-158">3 occorrenze di `MvcMovie` sostituite con `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="70c41-158">3 occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="70c41-159">L'elemento di ancoraggio `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` è stato sostituito con `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="70c41-159">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="70c41-160">Nel markup precedente, l'attributo `asp-area=""` [Helper Tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) e il valore dell'attributo sono stati omessi perché questa app non usa le [Aree](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="70c41-160">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="70c41-161">**Nota**: il `Movies` controller non è stato implementato.</span><span class="sxs-lookup"><span data-stu-id="70c41-161">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="70c41-162">A questo punto, il collegamento `Movie App` non è funzionale.</span><span class="sxs-lookup"><span data-stu-id="70c41-162">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="70c41-163">Salvare le modifiche e selezionare il collegamento **Privacy**.</span><span class="sxs-lookup"><span data-stu-id="70c41-163">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="70c41-164">Si noti come il titolo sulla scheda del browser visualizzi **Privacy Policy - Movie App** anziché **Privacy Policy - Mvc Movie**:</span><span class="sxs-lookup"><span data-stu-id="70c41-164">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Scheda Privacy](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="70c41-166">Toccare il collegamento **Home** e notare che anche il titolo e il testo di ancoraggio visualizzano **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="70c41-166">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="70c41-167">La modifica è stata apportata una volta nel modello di layout e tutte le pagine nel sito riflettono il nuovo testo del collegamento e il nuovo titolo.</span><span class="sxs-lookup"><span data-stu-id="70c41-167">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="70c41-168">Esaminare il file *Views/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="70c41-168">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="70c41-169">Il file *Views/_ViewStart.cshtml* attiva il file *Views/Shared/_Layout.cshtml* per ogni vista.</span><span class="sxs-lookup"><span data-stu-id="70c41-169">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="70c41-170">È possibile usare la proprietà `Layout` per impostare una vista di layout differente oppure impostarla su `null` e quindi non verrà usato alcun file di layout.</span><span class="sxs-lookup"><span data-stu-id="70c41-170">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="70c41-171">Modificare il titolo e l'elemento `<h2>` del file *Views/HelloWorld/Index.cshtml* di visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="70c41-171">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="70c41-172">Il titolo e l'elemento `<h2>`sono leggermente diversi in modo da poter esaminare la parte specifica di codice che modifica la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="70c41-172">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="70c41-173">`ViewData["Title"] = "Movie List";` nel codice precedente imposta la proprietà `Title` del dizionario `ViewData` su "Movie List".</span><span class="sxs-lookup"><span data-stu-id="70c41-173">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="70c41-174">La proprietà `Title` viene usata nell'elemento HTML `<title>` nella pagina di layout:</span><span class="sxs-lookup"><span data-stu-id="70c41-174">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="70c41-175">Salvare la modifica e passare a `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="70c41-175">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="70c41-176">Si noti che il titolo del browser, l'intestazione primaria e le intestazioni secondarie sono cambiati.</span><span class="sxs-lookup"><span data-stu-id="70c41-176">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="70c41-177">Se le modifiche non sono visibili nel browser, è possibile che si stia visualizzando il contenuto memorizzato nella cache.</span><span class="sxs-lookup"><span data-stu-id="70c41-177">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="70c41-178">Premere CTRL + F5 nel browser per forzare il caricamento della risposta dal server. Il titolo del browser viene creato con l' `ViewData["Title"]` impostazione nel modello di vista *index. cshtml* e l'aggiunta di "-Movie app" nel file di layout.</span><span class="sxs-lookup"><span data-stu-id="70c41-178">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="70c41-179">Il contenuto del modello di vista *Index.cshtml* viene unito al modello di vista *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="70c41-179">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="70c41-180">Viene inviata una singola risposta HTML al browser.</span><span class="sxs-lookup"><span data-stu-id="70c41-180">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="70c41-181">I modelli di layout rendono semplice apportare modifiche che si applicano a tutte le pagine di un'app.</span><span class="sxs-lookup"><span data-stu-id="70c41-181">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="70c41-182">Per altre informazioni, vedere [layout](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="70c41-182">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![Vista dell'elenco di film](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="70c41-184">Il breve testo di "dati", in questo caso il messaggio "Hello from our View Template!",</span><span class="sxs-lookup"><span data-stu-id="70c41-184">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="70c41-185">è tuttavia hardcoded.</span><span class="sxs-lookup"><span data-stu-id="70c41-185">message) is hard-coded, though.</span></span> <span data-ttu-id="70c41-186">L'applicazione MVC ha un elemento "V" (vista) ed è stato ottenuto un elemento "C" (controller), ma non ancora un elemento "M" (modello).</span><span class="sxs-lookup"><span data-stu-id="70c41-186">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="70c41-187">Passaggio di dati dal controller alla vista</span><span class="sxs-lookup"><span data-stu-id="70c41-187">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="70c41-188">Le azioni del controller vengono richiamate in risposta a una richiesta URL in ingresso.</span><span class="sxs-lookup"><span data-stu-id="70c41-188">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="70c41-189">Una classe controller è il punto in cui viene scritto il codice che gestisce le richieste in ingresso del browser.</span><span class="sxs-lookup"><span data-stu-id="70c41-189">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="70c41-190">Il controller recupera i dati da un'origine dati e determina il tipo di risposta da inviare al browser.</span><span class="sxs-lookup"><span data-stu-id="70c41-190">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="70c41-191">I modelli di vista possono essere usati da un controller per generare e formattare una risposta HTML al browser.</span><span class="sxs-lookup"><span data-stu-id="70c41-191">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="70c41-192">Il compito dei controller è fornire i dati necessari affinché un modello di vista possa eseguire il rendering di una risposta.</span><span class="sxs-lookup"><span data-stu-id="70c41-192">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="70c41-193">Procedura consigliata: i modelli di vista **non** devono eseguire la logica di business o interagire direttamente con un database.</span><span class="sxs-lookup"><span data-stu-id="70c41-193">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="70c41-194">Un modello di vista deve invece usare solo i dati forniti dal controller.</span><span class="sxs-lookup"><span data-stu-id="70c41-194">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="70c41-195">Questa "separazione delle problematiche" consente di mantenere il codice pulito e semplifica la gestione e i test.</span><span class="sxs-lookup"><span data-stu-id="70c41-195">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="70c41-196">Attualmente il metodo `Welcome` nella classe `HelloWorldController` accetta un parametro `name` e `ID` e quindi genera i valori direttamente al browser.</span><span class="sxs-lookup"><span data-stu-id="70c41-196">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="70c41-197">Invece di ottenere il rendering di questa risposta come stringa, cambiare il controller in modo che usi un modello di vista.</span><span class="sxs-lookup"><span data-stu-id="70c41-197">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="70c41-198">Il modello di vista genera una risposta dinamica, il che significa che è necessario passare i bit di dati appropriati dal controller alla vista per generare la risposta.</span><span class="sxs-lookup"><span data-stu-id="70c41-198">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="70c41-199">A tale scopo, fare in modo che il controller inserisca i dati dinamici (parametri) di cui il modello di vista necessita in un dizionario `ViewData` a cui il modello di vista può accedere.</span><span class="sxs-lookup"><span data-stu-id="70c41-199">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="70c41-200">In *HelloWorldController.cs*, modificare il metodo `Welcome` in modo da aggiungere un valore `Message` e `NumTimes` al dizionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="70c41-200">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="70c41-201">Il dizionario `ViewData` è un oggetto dinamico, ovvero è possibile usare qualunque tipo; l'oggetto `ViewData` non dispone di proprietà definite fino a quando non si inserisce un elemento al suo interno.</span><span class="sxs-lookup"><span data-stu-id="70c41-201">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="70c41-202">Il sistema di [associazione di modelli MVC](xref:mvc/models/model-binding) esegue il mapping dei parametri denominati (`name` e `numTimes`) dalla stringa di query nella barra degli indirizzi ai parametri nel metodo.</span><span class="sxs-lookup"><span data-stu-id="70c41-202">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="70c41-203">Il file *HelloWorldController.cs* completo avrà un aspetto simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="70c41-203">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="70c41-204">L'oggetto di dizionario `ViewData` contiene i dati che verranno passati alla vista.</span><span class="sxs-lookup"><span data-stu-id="70c41-204">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="70c41-205">Creare un modello di vista Welcome denominato *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="70c41-205">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="70c41-206">Si creerà un ciclo nel modello di vista *Welcome.cshtml* che visualizza la stringa "Hello" `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="70c41-206">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="70c41-207">Sostituire il contenuto di *Views/HelloWorld/Welcome.cshtml* con quanto segue:</span><span class="sxs-lookup"><span data-stu-id="70c41-207">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="70c41-208">Salvare le modifiche e passare all'URL seguente:</span><span class="sxs-lookup"><span data-stu-id="70c41-208">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="70c41-209">I dati vengono prelevati dall'URL e passati al controller usando lo [strumento di associazione di modelli MVC](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="70c41-209">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="70c41-210">Il controller crea un pacchetto di dati in un dizionario `ViewData` e passa tale oggetto alla vista.</span><span class="sxs-lookup"><span data-stu-id="70c41-210">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="70c41-211">La vista esegue quindi il rendering dei dati in formato HTML al browser.</span><span class="sxs-lookup"><span data-stu-id="70c41-211">The view then renders the data as HTML to the browser.</span></span>

![Vista Privacy con un'etichetta di benvenuto e la frase Hello Rick riportata quattro volte](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="70c41-213">Nell'esempio precedente è stato usato il dizionario `ViewData` per passare i dati dal controller a una vista.</span><span class="sxs-lookup"><span data-stu-id="70c41-213">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="70c41-214">Più avanti nell'esercitazione si userà un modello di vista per passare i dati da un controller a una vista.</span><span class="sxs-lookup"><span data-stu-id="70c41-214">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="70c41-215">In genere l'approccio basato sul modello di vista per passare i dati è preferito rispetto all'approccio basato sul dizionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="70c41-215">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="70c41-216">Per altre informazioni, vedere [quando usare ViewBag, ViewData o TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) .</span><span class="sxs-lookup"><span data-stu-id="70c41-216">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="70c41-217">Nella prossima esercitazione, viene creato un database di film.</span><span class="sxs-lookup"><span data-stu-id="70c41-217">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="70c41-218">[Precedente](adding-controller.md) 
>  [Avanti](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="70c41-218">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="70c41-219">In questa sezione viene modificata la `HelloWorldController` classe per l'utilizzo [Razor](xref:mvc/views/razor) dei file di visualizzazione per incapsulare in modo semplice il processo di generazione di risposte HTML a un client.</span><span class="sxs-lookup"><span data-stu-id="70c41-219">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="70c41-220">Si crea un file di modello di visualizzazione utilizzando Razor .</span><span class="sxs-lookup"><span data-stu-id="70c41-220">You create a view template file using Razor.</span></span> <span data-ttu-id="70c41-221">Razori modelli di vista basati su hanno un'estensione di file *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="70c41-221">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="70c41-222">Consentono di creare l'output HTML in modo accurato con C#.</span><span class="sxs-lookup"><span data-stu-id="70c41-222">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="70c41-223">Attualmente il metodo `Index` restituisce una stringa con un messaggio hardcoded nella classe controller.</span><span class="sxs-lookup"><span data-stu-id="70c41-223">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="70c41-224">Nella classe `HelloWorldController` sostituire il metodo `Index` con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="70c41-224">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="70c41-225">Il codice precedente chiama il metodo <xref:Microsoft.AspNetCore.Mvc.Controller.View*> del controller.</span><span class="sxs-lookup"><span data-stu-id="70c41-225">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="70c41-226">Usa un modello di visualizzazione per generare una risposta HTML.</span><span class="sxs-lookup"><span data-stu-id="70c41-226">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="70c41-227">I metodi del controller, noti anche come *metodi di azione*, ad esempio il metodo `Index` descritto in precedenza, restituiscono in genere un <xref:Microsoft.AspNetCore.Mvc.IActionResult> (o una classe derivata da <xref:Microsoft.AspNetCore.Mvc.ActionResult>) e non un tipo come `string`.</span><span class="sxs-lookup"><span data-stu-id="70c41-227">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="70c41-228">Aggiungere una vista</span><span class="sxs-lookup"><span data-stu-id="70c41-228">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="70c41-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="70c41-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="70c41-230">Fare clic con il pulsante destro del mouse sulla cartella *Views*, quindi su **Aggiungi > Nuova cartella** e denominare la cartella *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="70c41-230">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="70c41-231">Fare clic con il pulsante destro del mouse sulla cartella *Views/HelloWorld*, quindi su **Aggiungi > Nuovo elemento**.</span><span class="sxs-lookup"><span data-stu-id="70c41-231">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="70c41-232">Nella finestra di dialogo **Aggiungi nuovo elemento - MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="70c41-232">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="70c41-233">Nella casella di ricerca in alto a destra immettere *view* (vista)</span><span class="sxs-lookup"><span data-stu-id="70c41-233">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="70c41-234">Selezione **Razor visualizzazione**</span><span class="sxs-lookup"><span data-stu-id="70c41-234">Select **Razor View**</span></span>

  * <span data-ttu-id="70c41-235">Mantenere il valore della casella **Nome**, *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="70c41-235">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="70c41-236">Selezionare **Aggiungi**</span><span class="sxs-lookup"><span data-stu-id="70c41-236">Select **Add**</span></span>

![Finestra di dialogo Aggiungi nuovo elemento](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="70c41-238">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="70c41-238">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="70c41-239">Aggiungere una vista `Index` per `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="70c41-239">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="70c41-240">Aggiungere una nuova cartella denominata *Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="70c41-240">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="70c41-241">Aggiungere un nuovo file al nome di cartella *Views/HelloWorld\*\*Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="70c41-241">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="70c41-242">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="70c41-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="70c41-243">Fare clic con il pulsante destro del mouse sulla cartella *Views*, quindi su **Aggiungi > Nuova cartella** e denominare la cartella *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="70c41-243">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="70c41-244">Fare clic con il pulsante destro del mouse sulla cartella *Views/HelloWorld*, quindi su **Aggiungi > Nuovo file**.</span><span class="sxs-lookup"><span data-stu-id="70c41-244">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="70c41-245">Nel finestra di dialogo **Nuovo file**:</span><span class="sxs-lookup"><span data-stu-id="70c41-245">In the **New File** dialog:</span></span>

  * <span data-ttu-id="70c41-246">Selezionare **Web** nel riquadro a sinistra.</span><span class="sxs-lookup"><span data-stu-id="70c41-246">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="70c41-247">Selezionare **File HTML vuoto** nel riquadro centrale.</span><span class="sxs-lookup"><span data-stu-id="70c41-247">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="70c41-248">Digitare *Index.cshtml* nella casella **Nome**.</span><span class="sxs-lookup"><span data-stu-id="70c41-248">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="70c41-249">Selezionare **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="70c41-249">Select **New**.</span></span>

![Finestra di dialogo Aggiungi nuovo elemento](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="70c41-251">Sostituire il contenuto del file di visualizzazione *views/HelloWorld/index. cshtml* Razor con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="70c41-251">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="70c41-252">Accedere a `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="70c41-252">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="70c41-253">Il metodo `Index` in `HelloWorldController` non ha eseguito molte operazioni; ha eseguito l'istruzione `return View();` che ha specificato che il metodo deve usare un file di modello della vista per eseguire il rendering di una risposta al browser.</span><span class="sxs-lookup"><span data-stu-id="70c41-253">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="70c41-254">Poiché non è stato specificato un nome di file di modello di visualizzazione, per impostazione predefinita MVC usa il file di visualizzazione predefinito.</span><span class="sxs-lookup"><span data-stu-id="70c41-254">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="70c41-255">Il file di visualizzazione predefinito ha lo stesso nome del metodo (`Index`), quindi viene usato */Views/HelloWorld/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="70c41-255">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="70c41-256">L'immagine seguente mostra la stringa "Hello from our View Template!"</span><span class="sxs-lookup"><span data-stu-id="70c41-256">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="70c41-257">hardcoded nella vista.</span><span class="sxs-lookup"><span data-stu-id="70c41-257">hard-coded in the view.</span></span>

![Finestra del browser](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="70c41-259">Modificare le viste e le pagine di layout</span><span class="sxs-lookup"><span data-stu-id="70c41-259">Change views and layout pages</span></span>

<span data-ttu-id="70c41-260">Selezionare i collegamenti di menu: **MvcMovie**, **Home** e **Privacy**.</span><span class="sxs-lookup"><span data-stu-id="70c41-260">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="70c41-261">Ogni pagina mostra lo stesso layout di menu.</span><span class="sxs-lookup"><span data-stu-id="70c41-261">Each page shows the same menu layout.</span></span> <span data-ttu-id="70c41-262">Il layout di menu viene implementato nel file *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="70c41-262">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="70c41-263">Aprire il file *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="70c41-263">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="70c41-264">I modelli di [layout](xref:mvc/views/layout) consentono di specificare il layout del contenitore HTML del sito in un'unica posizione e quindi di applicarlo in più pagine del sito.</span><span class="sxs-lookup"><span data-stu-id="70c41-264">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="70c41-265">Trovare la riga `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="70c41-265">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="70c41-266">`RenderBody` è un segnaposto dove tutte le pagine specifiche della vista vengono presentate, *incapsulate* nella pagina di layout.</span><span class="sxs-lookup"><span data-stu-id="70c41-266">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="70c41-267">Ad esempio, se si seleziona il collegamento **Privacy**, il rendering della vista **Views/Home/Privacy.cshtml** viene eseguito all'interno del metodo `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="70c41-267">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="70c41-268">Modificare il titolo, il piè di pagina e il collegamento di menu nel file di layout</span><span class="sxs-lookup"><span data-stu-id="70c41-268">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="70c41-269">Negli elementi del titolo e del piè di pagina modificare `MvcMovie` in `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="70c41-269">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="70c41-270">Modificare l'elemento ancora `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` in `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="70c41-270">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="70c41-271">Il markup seguente visualizza le modifiche evidenziate:</span><span class="sxs-lookup"><span data-stu-id="70c41-271">The following markup shows the highlighted changes:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="70c41-272">Nel markup precedente, l'attributo `asp-area` [Helper Tag di ancoraggio](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) è stato omesso perché questa app non usa le [Aree](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="70c41-272">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="70c41-273">**Nota**: il `Movies` controller non è stato implementato.</span><span class="sxs-lookup"><span data-stu-id="70c41-273">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="70c41-274">A questo punto, il collegamento `Movie App` non è funzionale.</span><span class="sxs-lookup"><span data-stu-id="70c41-274">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="70c41-275">Salvare le modifiche e selezionare il collegamento **Privacy**.</span><span class="sxs-lookup"><span data-stu-id="70c41-275">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="70c41-276">Si noti come il titolo sulla scheda del browser visualizzi **Privacy Policy - Movie App** anziché **Privacy Policy - Mvc Movie**:</span><span class="sxs-lookup"><span data-stu-id="70c41-276">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Scheda Privacy](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="70c41-278">Toccare il collegamento **Home** e notare che anche il titolo e il testo di ancoraggio visualizzano **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="70c41-278">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="70c41-279">La modifica è stata apportata una volta nel modello di layout e tutte le pagine nel sito riflettono il nuovo testo del collegamento e il nuovo titolo.</span><span class="sxs-lookup"><span data-stu-id="70c41-279">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="70c41-280">Esaminare il file *Views/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="70c41-280">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="70c41-281">Il file *Views/_ViewStart.cshtml* attiva il file *Views/Shared/_Layout.cshtml* per ogni vista.</span><span class="sxs-lookup"><span data-stu-id="70c41-281">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="70c41-282">È possibile usare la proprietà `Layout` per impostare una vista di layout differente oppure impostarla su `null` e quindi non verrà usato alcun file di layout.</span><span class="sxs-lookup"><span data-stu-id="70c41-282">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="70c41-283">Modificare il titolo e l'elemento `<h2>` del file *Views/HelloWorld/Index.cshtml* di visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="70c41-283">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="70c41-284">Il titolo e l'elemento `<h2>`sono leggermente diversi in modo da poter esaminare la parte specifica di codice che modifica la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="70c41-284">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="70c41-285">`ViewData["Title"] = "Movie List";` nel codice precedente imposta la proprietà `Title` del dizionario `ViewData` su "Movie List".</span><span class="sxs-lookup"><span data-stu-id="70c41-285">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="70c41-286">La proprietà `Title` viene usata nell'elemento HTML `<title>` nella pagina di layout:</span><span class="sxs-lookup"><span data-stu-id="70c41-286">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="70c41-287">Salvare la modifica e passare a `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="70c41-287">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="70c41-288">Si noti che il titolo del browser, l'intestazione primaria e le intestazioni secondarie sono cambiati.</span><span class="sxs-lookup"><span data-stu-id="70c41-288">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="70c41-289">Se le modifiche non sono visibili nel browser, è possibile che si stia visualizzando il contenuto memorizzato nella cache.</span><span class="sxs-lookup"><span data-stu-id="70c41-289">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="70c41-290">Premere CTRL + F5 nel browser per forzare il caricamento della risposta dal server. Il titolo del browser viene creato con l' `ViewData["Title"]` impostazione nel modello di vista *index. cshtml* e l'aggiunta di "-Movie app" nel file di layout.</span><span class="sxs-lookup"><span data-stu-id="70c41-290">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="70c41-291">Si noti anche come il contenuto del modello di vista *Index.cshtml* sia stato unito con il modello di vista *Views/Shared/_Layout.cshtml* e sia stata inviata una singola risposta HTML al browser.</span><span class="sxs-lookup"><span data-stu-id="70c41-291">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="70c41-292">I modelli di layout rendono molto semplice apportare modifiche che si applicano a tutte le pagine dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="70c41-292">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="70c41-293">Per altre informazioni, vedere [Layout](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="70c41-293">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![Vista dell'elenco di film](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="70c41-295">Il breve testo di "dati", in questo caso il messaggio "Hello from our View Template!",</span><span class="sxs-lookup"><span data-stu-id="70c41-295">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="70c41-296">è tuttavia hardcoded.</span><span class="sxs-lookup"><span data-stu-id="70c41-296">message) is hard-coded, though.</span></span> <span data-ttu-id="70c41-297">L'applicazione MVC ha un elemento "V" (vista) ed è stato ottenuto un elemento "C" (controller), ma non ancora un elemento "M" (modello).</span><span class="sxs-lookup"><span data-stu-id="70c41-297">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="70c41-298">Passaggio di dati dal controller alla vista</span><span class="sxs-lookup"><span data-stu-id="70c41-298">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="70c41-299">Le azioni del controller vengono richiamate in risposta a una richiesta URL in ingresso.</span><span class="sxs-lookup"><span data-stu-id="70c41-299">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="70c41-300">Una classe controller è il punto in cui viene scritto il codice che gestisce le richieste in ingresso del browser.</span><span class="sxs-lookup"><span data-stu-id="70c41-300">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="70c41-301">Il controller recupera i dati da un'origine dati e determina il tipo di risposta da inviare al browser.</span><span class="sxs-lookup"><span data-stu-id="70c41-301">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="70c41-302">I modelli di vista possono essere usati da un controller per generare e formattare una risposta HTML al browser.</span><span class="sxs-lookup"><span data-stu-id="70c41-302">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="70c41-303">Il compito dei controller è fornire i dati necessari affinché un modello di vista possa eseguire il rendering di una risposta.</span><span class="sxs-lookup"><span data-stu-id="70c41-303">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="70c41-304">Procedura consigliata: i modelli di vista **non** devono eseguire la logica di business o interagire direttamente con un database.</span><span class="sxs-lookup"><span data-stu-id="70c41-304">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="70c41-305">Un modello di vista deve invece usare solo i dati forniti dal controller.</span><span class="sxs-lookup"><span data-stu-id="70c41-305">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="70c41-306">Questa "separazione delle problematiche" consente di mantenere il codice pulito e semplifica la gestione e i test.</span><span class="sxs-lookup"><span data-stu-id="70c41-306">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="70c41-307">Attualmente il metodo `Welcome` nella classe `HelloWorldController` accetta un parametro `name` e `ID` e quindi genera i valori direttamente al browser.</span><span class="sxs-lookup"><span data-stu-id="70c41-307">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="70c41-308">Invece di ottenere il rendering di questa risposta come stringa, cambiare il controller in modo che usi un modello di vista.</span><span class="sxs-lookup"><span data-stu-id="70c41-308">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="70c41-309">Il modello di vista genera una risposta dinamica, il che significa che è necessario passare i bit di dati appropriati dal controller alla vista per generare la risposta.</span><span class="sxs-lookup"><span data-stu-id="70c41-309">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="70c41-310">A tale scopo, fare in modo che il controller inserisca i dati dinamici (parametri) di cui il modello di vista necessita in un dizionario `ViewData` a cui il modello di vista può accedere.</span><span class="sxs-lookup"><span data-stu-id="70c41-310">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="70c41-311">In *HelloWorldController.cs*, modificare il metodo `Welcome` in modo da aggiungere un valore `Message` e `NumTimes` al dizionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="70c41-311">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="70c41-312">Il dizionario `ViewData` è un oggetto dinamico, ovvero è possibile usare qualunque tipo; l'oggetto `ViewData` non dispone di proprietà definite fino a quando non si inserisce un elemento al suo interno.</span><span class="sxs-lookup"><span data-stu-id="70c41-312">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="70c41-313">Il sistema di [associazione di modelli MVC](xref:mvc/models/model-binding) esegue il mapping dei parametri denominati (`name` e `numTimes`) dalla stringa di query nella barra degli indirizzi ai parametri nel metodo.</span><span class="sxs-lookup"><span data-stu-id="70c41-313">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="70c41-314">Il file *HelloWorldController.cs* completo avrà un aspetto simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="70c41-314">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="70c41-315">L'oggetto di dizionario `ViewData` contiene i dati che verranno passati alla vista.</span><span class="sxs-lookup"><span data-stu-id="70c41-315">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="70c41-316">Creare un modello di vista Welcome denominato *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="70c41-316">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="70c41-317">Si creerà un ciclo nel modello di vista *Welcome.cshtml* che visualizza la stringa "Hello" `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="70c41-317">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="70c41-318">Sostituire il contenuto di *Views/HelloWorld/Welcome.cshtml* con quanto segue:</span><span class="sxs-lookup"><span data-stu-id="70c41-318">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="70c41-319">Salvare le modifiche e passare all'URL seguente:</span><span class="sxs-lookup"><span data-stu-id="70c41-319">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="70c41-320">I dati vengono prelevati dall'URL e passati al controller usando lo [strumento di associazione di modelli MVC](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="70c41-320">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="70c41-321">Il controller crea un pacchetto di dati in un dizionario `ViewData` e passa tale oggetto alla vista.</span><span class="sxs-lookup"><span data-stu-id="70c41-321">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="70c41-322">La vista esegue quindi il rendering dei dati in formato HTML al browser.</span><span class="sxs-lookup"><span data-stu-id="70c41-322">The view then renders the data as HTML to the browser.</span></span>

![Vista Privacy con un'etichetta di benvenuto e la frase Hello Rick riportata quattro volte](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="70c41-324">Nell'esempio precedente è stato usato il dizionario `ViewData` per passare i dati dal controller a una vista.</span><span class="sxs-lookup"><span data-stu-id="70c41-324">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="70c41-325">Più avanti nell'esercitazione si userà un modello di vista per passare i dati da un controller a una vista.</span><span class="sxs-lookup"><span data-stu-id="70c41-325">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="70c41-326">In genere l'approccio basato sul modello di vista per passare i dati è preferito rispetto all'approccio basato sul dizionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="70c41-326">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="70c41-327">Per altre informazioni, vedere [quando usare ViewBag, ViewData o TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) .</span><span class="sxs-lookup"><span data-stu-id="70c41-327">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="70c41-328">Nella prossima esercitazione, viene creato un database di film.</span><span class="sxs-lookup"><span data-stu-id="70c41-328">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="70c41-329">[Precedente](adding-controller.md) 
>  [Avanti](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="70c41-329">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end
