---
title: Eseguire la migrazione da ASP.NET MVC ad ASP.NET Core MVC
author: ardalis
description: Informazioni su come iniziare a migrare un progetto MVC ASP.NET a ASP.NET Core MVC.
ms.author: riande
ms.date: 04/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/mvc
ms.openlocfilehash: 59a10c002958e5f719dbd59686f21df69da5f43e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777046"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="7f128-103">Eseguire la migrazione da ASP.NET MVC ad ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="7f128-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

<span data-ttu-id="7f128-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/)e [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="7f128-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="7f128-105">Questo articolo illustra come iniziare a migrare un progetto MVC ASP.NET a [ASP.NET Core MVC](../mvc/overview.md).</span><span class="sxs-lookup"><span data-stu-id="7f128-105">This article shows how to get started migrating an ASP.NET MVC project to [ASP.NET Core MVC](../mvc/overview.md).</span></span> <span data-ttu-id="7f128-106">Nel processo, vengono evidenziati molti degli elementi che sono stati modificati rispetto a ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="7f128-106">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="7f128-107">La migrazione da ASP.NET MVC è un processo in più passaggi e in questo articolo viene illustrata la configurazione iniziale, i controller e le visualizzazioni di base, il contenuto statico e le dipendenze lato client.</span><span class="sxs-lookup"><span data-stu-id="7f128-107">Migrating from ASP.NET MVC is a multiple step process and this article covers the initial setup, basic controllers and views, static content, and client-side dependencies.</span></span> <span data-ttu-id="7f128-108">Altri articoli riguardano la migrazione del codice di configurazione e di identità trovato in molti progetti MVC ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="7f128-108">Additional articles cover migrating configuration and identity code found in many ASP.NET MVC projects.</span></span>

> [!NOTE]
> <span data-ttu-id="7f128-109">I numeri di versione negli esempi potrebbero non essere aggiornati.</span><span class="sxs-lookup"><span data-stu-id="7f128-109">The version numbers in the samples might not be current.</span></span> <span data-ttu-id="7f128-110">Potrebbe essere necessario aggiornare i progetti di conseguenza.</span><span class="sxs-lookup"><span data-stu-id="7f128-110">You may need to update your projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="7f128-111">Creare il progetto MVC Starter ASP.NET</span><span class="sxs-lookup"><span data-stu-id="7f128-111">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="7f128-112">Per illustrare l'aggiornamento, si inizierà con la creazione di un'app MVC ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="7f128-112">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="7f128-113">Crearlo con il nome *app Web 1* in modo che lo spazio dei nomi corrisponda al progetto ASP.NET Core creato nel passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="7f128-113">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project we create in the next step.</span></span>

![Finestra di dialogo Nuovo progetto di Visual Studio](mvc/_static/new-project.png)

![Finestra di dialogo nuova applicazione Web: modello di progetto MVC selezionato nel pannello modelli ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="7f128-116">*Facoltativo:* Modificare il nome della soluzione da *app Web 1* a *Mvc5*.</span><span class="sxs-lookup"><span data-stu-id="7f128-116">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="7f128-117">In Visual Studio viene visualizzato il nuovo nome della soluzione (*Mvc5*), che rende più semplice la creazione di questo progetto dal progetto successivo.</span><span class="sxs-lookup"><span data-stu-id="7f128-117">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="7f128-118">Creare il progetto di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7f128-118">Create the ASP.NET Core project</span></span>

<span data-ttu-id="7f128-119">Creare una nuova app Web ASP.NET Core *vuota* con lo stesso nome del progetto precedente (*app Web 1*) in modo che gli spazi dei nomi nei due progetti corrispondano.</span><span class="sxs-lookup"><span data-stu-id="7f128-119">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="7f128-120">La presenza dello stesso spazio dei nomi rende più semplice la copia di codice tra i due progetti.</span><span class="sxs-lookup"><span data-stu-id="7f128-120">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="7f128-121">È necessario creare questo progetto in una directory diversa da quella del progetto precedente per usare lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="7f128-121">You'll have to create this project in a different directory than the previous project to use the same name.</span></span>

![Finestra di dialogo Nuovo progetto](mvc/_static/new_core.png)

![Finestra di dialogo nuova applicazione Web ASP.NET: modello di progetto vuoto selezionato nel pannello modelli ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="7f128-124">*Facoltativo:* Creare una nuova app ASP.NET Core usando il modello di progetto *applicazione Web* .</span><span class="sxs-lookup"><span data-stu-id="7f128-124">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="7f128-125">Denominare il progetto *app Web 1*e selezionare un'opzione di autenticazione per **singoli account utente**.</span><span class="sxs-lookup"><span data-stu-id="7f128-125">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="7f128-126">Rinominare l'app in *FullAspNetCore*.</span><span class="sxs-lookup"><span data-stu-id="7f128-126">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="7f128-127">La creazione di questo progetto consente di risparmiare tempo nella conversione.</span><span class="sxs-lookup"><span data-stu-id="7f128-127">Creating this project saves you time in the conversion.</span></span> <span data-ttu-id="7f128-128">È possibile esaminare il codice generato dal modello per visualizzare il risultato finale o copiare il codice nel progetto di conversione.</span><span class="sxs-lookup"><span data-stu-id="7f128-128">You can look at the template-generated code to see the end result or to copy code to the conversion project.</span></span> <span data-ttu-id="7f128-129">Questa operazione è utile anche quando ci si blocca in un passaggio di conversione da confrontare con il progetto generato dal modello.</span><span class="sxs-lookup"><span data-stu-id="7f128-129">It's also helpful when you get stuck on a conversion step to compare with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="7f128-130">Configurare il sito per l'uso di MVC</span><span class="sxs-lookup"><span data-stu-id="7f128-130">Configure the site to use MVC</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="7f128-131">Quando la destinazione è .NET Core, per impostazione predefinita viene fatto riferimento al [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="7f128-131">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="7f128-132">Questo pacchetto contiene i pacchetti usati comunemente dalle app MVC.</span><span class="sxs-lookup"><span data-stu-id="7f128-132">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="7f128-133">Se la destinazione è .NET Framework, i riferimenti ai pacchetti devono essere elencati singolarmente nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="7f128-133">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="7f128-134">Quando la destinazione è .NET Core, per impostazione predefinita viene fatto riferimento al [metapacchetto Microsoft. AspNetCore. All](xref:fundamentals/metapackage) .</span><span class="sxs-lookup"><span data-stu-id="7f128-134">When targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) is referenced by default.</span></span> <span data-ttu-id="7f128-135">Questo pacchetto contiene pacchetti usati comunemente da app MVC.</span><span class="sxs-lookup"><span data-stu-id="7f128-135">This package contains packages commonly used packages by MVC apps.</span></span> <span data-ttu-id="7f128-136">Se la destinazione è .NET Framework, i riferimenti ai pacchetti devono essere elencati singolarmente nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="7f128-136">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="7f128-137">Quando la destinazione è .NET Core o .NET Framework, i pacchetti usati comunemente da app MVC vengono elencati singolarmente nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="7f128-137">When targeting .NET Core or .NET Framework, packages commonly used packages by MVC apps are listed individually in the project file.</span></span>

::: moniker-end

<span data-ttu-id="7f128-138">`Microsoft.AspNetCore.Mvc`è il Framework di MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7f128-138">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="7f128-139">`Microsoft.AspNetCore.StaticFiles`è il gestore di file statici.</span><span class="sxs-lookup"><span data-stu-id="7f128-139">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="7f128-140">Il runtime di ASP.NET Core è modulare ed è necessario acconsentire esplicitamente a gestire i file statici (vedere [file statici](xref:fundamentals/static-files)).</span><span class="sxs-lookup"><span data-stu-id="7f128-140">The ASP.NET Core runtime is modular, and you must explicitly opt in to serve static files (see [Static files](xref:fundamentals/static-files)).</span></span>

* <span data-ttu-id="7f128-141">Aprire il file *Startup.cs* e modificare il codice in modo che corrisponda a quanto segue:</span><span class="sxs-lookup"><span data-stu-id="7f128-141">Open the *Startup.cs* file and change the code to match the following:</span></span>

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

<span data-ttu-id="7f128-142">Il `UseStaticFiles` metodo di estensione aggiunge il gestore di file statici.</span><span class="sxs-lookup"><span data-stu-id="7f128-142">The `UseStaticFiles` extension method adds the static file handler.</span></span> <span data-ttu-id="7f128-143">Come indicato in precedenza, il runtime di ASP.NET è modulare ed è necessario acconsentire esplicitamente a gestire i file statici.</span><span class="sxs-lookup"><span data-stu-id="7f128-143">As mentioned previously, the ASP.NET runtime is modular, and you must explicitly opt in to serve static files.</span></span> <span data-ttu-id="7f128-144">Il `UseMvc` metodo di estensione aggiunge il routing.</span><span class="sxs-lookup"><span data-stu-id="7f128-144">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="7f128-145">Per ulteriori informazioni, vedere Startup e [routing](xref:fundamentals/routing) [dell'applicazione](xref:fundamentals/startup) .</span><span class="sxs-lookup"><span data-stu-id="7f128-145">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="7f128-146">Aggiungere un controller e una visualizzazione</span><span class="sxs-lookup"><span data-stu-id="7f128-146">Add a controller and view</span></span>

<span data-ttu-id="7f128-147">In questa sezione si aggiungeranno un controller e una visualizzazione minimi per fungere da segnaposto per il controller e le visualizzazioni MVC ASP.NET di cui verrà eseguita la migrazione nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="7f128-147">In this section, you'll add a minimal controller and view to serve as placeholders for the ASP.NET MVC controller and views you'll migrate in the next section.</span></span>

* <span data-ttu-id="7f128-148">Aggiungere una cartella *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="7f128-148">Add a *Controllers* folder.</span></span>

* <span data-ttu-id="7f128-149">Aggiungere una **classe controller** denominata *HomeController.cs* alla cartella *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="7f128-149">Add a **Controller Class** named *HomeController.cs* to the *Controllers* folder.</span></span>

![Finestra di dialogo Aggiungi nuovo elemento](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="7f128-151">Aggiungere una cartella *views* .</span><span class="sxs-lookup"><span data-stu-id="7f128-151">Add a *Views* folder.</span></span>

* <span data-ttu-id="7f128-152">Aggiungere una cartella *views/Home* .</span><span class="sxs-lookup"><span data-stu-id="7f128-152">Add a *Views/Home* folder.</span></span>

* <span data-ttu-id="7f128-153">Aggiungere una \*\* Razor vista\*\* denominata *index. cshtml* alla cartella *views/Home* .</span><span class="sxs-lookup"><span data-stu-id="7f128-153">Add a **Razor View** named *Index.cshtml* to the *Views/Home* folder.</span></span>

![Finestra di dialogo Aggiungi nuovo elemento](mvc/_static/view.png)

<span data-ttu-id="7f128-155">La struttura del progetto è illustrata di seguito:</span><span class="sxs-lookup"><span data-stu-id="7f128-155">The project structure is shown below:</span></span>

![Esplora soluzioni la visualizzazione di file e cartelle di app Web 1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="7f128-157">Sostituire il contenuto del file *views/Home/index. cshtml* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="7f128-157">Replace the contents of the *Views/Home/Index.cshtml* file with the following:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="7f128-158">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="7f128-158">Run the app.</span></span>

![App Web aperta in Microsoft Edge](mvc/_static/hello-world.png)

<span data-ttu-id="7f128-160">Per ulteriori informazioni, vedere [controller](xref:mvc/controllers/actions) e [visualizzazioni](xref:mvc/views/overview) .</span><span class="sxs-lookup"><span data-stu-id="7f128-160">See [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview) for more information.</span></span>

<span data-ttu-id="7f128-161">Ora che è disponibile un progetto di ASP.NET Core di lavoro minimo, è possibile avviare la migrazione della funzionalità dal progetto MVC ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="7f128-161">Now that we have a minimal working ASP.NET Core project, we can start migrating functionality from the ASP.NET MVC project.</span></span> <span data-ttu-id="7f128-162">È necessario spostare gli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="7f128-162">We need to move the following:</span></span>

* <span data-ttu-id="7f128-163">contenuto lato client (CSS, tipi di carattere e script)</span><span class="sxs-lookup"><span data-stu-id="7f128-163">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="7f128-164">controllers</span><span class="sxs-lookup"><span data-stu-id="7f128-164">controllers</span></span>

* <span data-ttu-id="7f128-165">Viste</span><span class="sxs-lookup"><span data-stu-id="7f128-165">views</span></span>

* <span data-ttu-id="7f128-166">modelli</span><span class="sxs-lookup"><span data-stu-id="7f128-166">models</span></span>

* <span data-ttu-id="7f128-167">Bundle</span><span class="sxs-lookup"><span data-stu-id="7f128-167">bundling</span></span>

* <span data-ttu-id="7f128-168">filters</span><span class="sxs-lookup"><span data-stu-id="7f128-168">filters</span></span>

* <span data-ttu-id="7f128-169">Accesso/uscita Identity (operazione eseguita nell'esercitazione successiva).</span><span class="sxs-lookup"><span data-stu-id="7f128-169">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="7f128-170">Controller e visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="7f128-170">Controllers and views</span></span>

* <span data-ttu-id="7f128-171">Copiare tutti i metodi da ASP.NET MVC `HomeController` al nuovo. `HomeController`</span><span class="sxs-lookup"><span data-stu-id="7f128-171">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="7f128-172">Si noti che in ASP.NET MVC il tipo restituito del metodo di azione del controller del modello predefinito è [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC i metodi di azione restituiscono `IActionResult` invece.</span><span class="sxs-lookup"><span data-stu-id="7f128-172">Note that in ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="7f128-173">`ActionResult`implementa `IActionResult`, quindi non è necessario modificare il tipo restituito dei metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="7f128-173">`ActionResult` implements `IActionResult`, so there's no need to change the return type of your action methods.</span></span>

* <span data-ttu-id="7f128-174">Copiare i file *di visualizzazione About. cshtml*, *Contact. cshtml*e *index. cshtml* Razor dal progetto MVC ASP.NET al progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7f128-174">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

* <span data-ttu-id="7f128-175">Eseguire l'app ASP.NET Core e testare ogni metodo.</span><span class="sxs-lookup"><span data-stu-id="7f128-175">Run the ASP.NET Core app and test each method.</span></span> <span data-ttu-id="7f128-176">Non è ancora stata eseguita la migrazione del file di layout o degli stili, quindi le visualizzazioni sottoposte a rendering contengono solo il contenuto dei file di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="7f128-176">We haven't migrated the layout file or styles yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="7f128-177">Il file di layout non include collegamenti generati per le `About` visualizzazioni `Contact` e, quindi è necessario richiamarli dal browser (sostituire **4492** con il numero di porta usato nel progetto).</span><span class="sxs-lookup"><span data-stu-id="7f128-177">You won't have the layout file generated links for the `About` and `Contact` views, so you'll have to invoke them from the browser (replace **4492** with the port number used in your project).</span></span>

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![Pagina contatto](mvc/_static/contact-page.png)

<span data-ttu-id="7f128-179">Si noti la mancanza di stili e voci di menu.</span><span class="sxs-lookup"><span data-stu-id="7f128-179">Note the lack of styling and menu items.</span></span> <span data-ttu-id="7f128-180">Questo problema verrà corretto nella prossima sezione.</span><span class="sxs-lookup"><span data-stu-id="7f128-180">We'll fix that in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="7f128-181">Contenuto statico</span><span class="sxs-lookup"><span data-stu-id="7f128-181">Static content</span></span>

<span data-ttu-id="7f128-182">Nelle versioni precedenti di ASP.NET MVC, il contenuto statico era ospitato dalla radice del progetto Web ed era combinato con i file lato server.</span><span class="sxs-lookup"><span data-stu-id="7f128-182">In previous versions of ASP.NET MVC, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="7f128-183">In ASP.NET Core, il contenuto statico è ospitato nella cartella *wwwroot*</span><span class="sxs-lookup"><span data-stu-id="7f128-183">In ASP.NET Core, static content is hosted in the *wwwroot* folder.</span></span> <span data-ttu-id="7f128-184">È possibile copiare il contenuto statico dall'app ASP.NET MVC precedente alla cartella *wwwroot* nel progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7f128-184">You'll want to copy the static content from your old ASP.NET MVC app to the *wwwroot* folder in your ASP.NET Core project.</span></span> <span data-ttu-id="7f128-185">In questo esempio di conversione:</span><span class="sxs-lookup"><span data-stu-id="7f128-185">In this sample conversion:</span></span>

* <span data-ttu-id="7f128-186">Copiare il file *favicon. ico* dal progetto MVC precedente alla cartella *wwwroot* nel progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7f128-186">Copy the *favicon.ico* file from the old MVC project to the *wwwroot* folder in the ASP.NET Core project.</span></span>

<span data-ttu-id="7f128-187">Il vecchio progetto MVC ASP.NET usa [bootstrap](https://getbootstrap.com/) per lo stile e archivia i file bootstrap nelle cartelle *Content* e *Scripts* .</span><span class="sxs-lookup"><span data-stu-id="7f128-187">The old ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* folders.</span></span> <span data-ttu-id="7f128-188">Il modello, che ha generato il vecchio progetto MVC ASP.NET, fa riferimento a bootstrap nel file di layout (*Views/Shared/_Layout. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="7f128-188">The template, which generated the old ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="7f128-189">È possibile copiare i file *bootstrap. js* e *bootstrap. CSS* dal progetto MVC ASP.NET alla cartella *wwwroot* del nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="7f128-189">You could copy the *bootstrap.js* and *bootstrap.css* files from the ASP.NET MVC project to the *wwwroot* folder in the new project.</span></span> <span data-ttu-id="7f128-190">Al contrario, verrà aggiunto il supporto per bootstrap (e altre librerie lato client) usando CDNs nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="7f128-190">Instead, we'll add support for Bootstrap (and other client-side libraries) using CDNs in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="7f128-191">Eseguire la migrazione del file di layout</span><span class="sxs-lookup"><span data-stu-id="7f128-191">Migrate the layout file</span></span>

* <span data-ttu-id="7f128-192">Copiare il file *_ViewStart. cshtml* dalla cartella *views* del progetto MVC ASP.NET precedente nella cartella *views* del progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7f128-192">Copy the *_ViewStart.cshtml* file from the old ASP.NET MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="7f128-193">Il file *_ViewStart. cshtml* non è stato modificato in ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="7f128-193">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="7f128-194">Creare una cartella *Views/Shared* .</span><span class="sxs-lookup"><span data-stu-id="7f128-194">Create a *Views/Shared* folder.</span></span>

* <span data-ttu-id="7f128-195">*Facoltativo:* Copiare *_ViewImports. cshtml* dalla cartella *views* del progetto MVC *FullAspNetCore* nella cartella *views* del progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7f128-195">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="7f128-196">Rimuovere qualsiasi dichiarazione dello spazio dei nomi nel file *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="7f128-196">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="7f128-197">Il file *_ViewImports. cshtml* fornisce gli spazi dei nomi per tutti i file di visualizzazione e porta gli [Helper Tag](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="7f128-197">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="7f128-198">Gli helper tag vengono usati nel nuovo file di layout.</span><span class="sxs-lookup"><span data-stu-id="7f128-198">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="7f128-199">Il file *_ViewImports. cshtml* è nuovo per ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7f128-199">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="7f128-200">Copiare il file *_Layout. cshtml* dalla cartella *Views/Shared* del progetto MVC ASP.NET precedente nella cartella *views* /Shared del progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7f128-200">Copy the *_Layout.cshtml* file from the old ASP.NET MVC project's *Views/Shared* folder into the ASP.NET Core project's *Views/Shared* folder.</span></span>

<span data-ttu-id="7f128-201">Aprire *_Layout file cshtml* e apportare le modifiche seguenti (il codice completato è riportato di seguito):</span><span class="sxs-lookup"><span data-stu-id="7f128-201">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="7f128-202">Sostituire `@Styles.Render("~/Content/css")` con un `<link>` elemento per caricare *bootstrap. CSS* (vedere di seguito).</span><span class="sxs-lookup"><span data-stu-id="7f128-202">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="7f128-203">Rimuovere `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="7f128-203">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="7f128-204">Imposta come commento `@Html.Partial("_LoginPartial")` la riga, che racchiude la `@*...*@`riga con.</span><span class="sxs-lookup"><span data-stu-id="7f128-204">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="7f128-205">Per ulteriori informazioni, vedere la pagina relativa [alla migrazione dell'autenticazione Identity e alla ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="7f128-205">For more information see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="7f128-206">Sostituire `@Scripts.Render("~/bundles/jquery")` con un `<script>` elemento (vedere di seguito).</span><span class="sxs-lookup"><span data-stu-id="7f128-206">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="7f128-207">Sostituire `@Scripts.Render("~/bundles/bootstrap")` con un `<script>` elemento (vedere di seguito).</span><span class="sxs-lookup"><span data-stu-id="7f128-207">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="7f128-208">Markup sostitutivo per l'inclusione CSS bootstrap:</span><span class="sxs-lookup"><span data-stu-id="7f128-208">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="7f128-209">Markup sostitutivo per jQuery e bootstrap JavaScript inclusion:</span><span class="sxs-lookup"><span data-stu-id="7f128-209">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="7f128-210">Il file *_Layout. cshtml* aggiornato è illustrato di seguito:</span><span class="sxs-lookup"><span data-stu-id="7f128-210">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="7f128-211">Visualizzare il sito nel browser.</span><span class="sxs-lookup"><span data-stu-id="7f128-211">View the site in the browser.</span></span> <span data-ttu-id="7f128-212">A questo punto, dovrebbe essere caricato correttamente con gli stili previsti.</span><span class="sxs-lookup"><span data-stu-id="7f128-212">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="7f128-213">*Facoltativo:* Potrebbe essere necessario provare a utilizzare il nuovo file di layout.</span><span class="sxs-lookup"><span data-stu-id="7f128-213">*Optional:* You might want to try using the new layout file.</span></span> <span data-ttu-id="7f128-214">Per questo progetto è possibile copiare il file di layout dal progetto *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="7f128-214">For this project you can copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="7f128-215">Il nuovo file di layout utilizza gli [Helper Tag](xref:mvc/views/tag-helpers/intro) e presenta altri miglioramenti.</span><span class="sxs-lookup"><span data-stu-id="7f128-215">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="7f128-216">Configurare la creazione di bundle e minification</span><span class="sxs-lookup"><span data-stu-id="7f128-216">Configure bundling and minification</span></span>

<span data-ttu-id="7f128-217">Per informazioni su come configurare la creazione di bundle e minification, vedere [aggregazione e minification](../client-side/bundling-and-minification.md).</span><span class="sxs-lookup"><span data-stu-id="7f128-217">For information about how to configure bundling and minification, see [Bundling and Minification](../client-side/bundling-and-minification.md).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="7f128-218">Risolvere gli errori HTTP 500</span><span class="sxs-lookup"><span data-stu-id="7f128-218">Solve HTTP 500 errors</span></span>

<span data-ttu-id="7f128-219">Ci sono molti problemi che possono causare un messaggio di errore HTTP 500 che non contiene informazioni sull'origine del problema.</span><span class="sxs-lookup"><span data-stu-id="7f128-219">There are many problems that can cause a HTTP 500 error message that contain no information on the source of the problem.</span></span> <span data-ttu-id="7f128-220">Se, ad esempio, il file *views/_ViewImports. cshtml* contiene uno spazio dei nomi che non esiste nel progetto, verrà ricevuto un errore HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="7f128-220">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in your project, you'll get a HTTP 500 error.</span></span> <span data-ttu-id="7f128-221">Per impostazione predefinita, nelle app ASP.NET Core `UseDeveloperExceptionPage` , l'estensione viene aggiunta `IApplicationBuilder` a ed eseguita quando la configurazione è in *fase di sviluppo*.</span><span class="sxs-lookup"><span data-stu-id="7f128-221">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="7f128-222">Questa operazione è descritta in dettaglio nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="7f128-222">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

<span data-ttu-id="7f128-223">ASP.NET Core converte le eccezioni non gestite in un'app Web in risposte di errore HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="7f128-223">ASP.NET Core converts unhandled exceptions in a web app into HTTP 500 error responses.</span></span> <span data-ttu-id="7f128-224">In genere, i dettagli dell'errore non sono inclusi in queste risposte per impedire la divulgazione di informazioni potenzialmente riservate sul server.</span><span class="sxs-lookup"><span data-stu-id="7f128-224">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="7f128-225">Per ulteriori informazioni, vedere **utilizzo della pagina delle eccezioni per sviluppatori** in [Gestisci errori](../fundamentals/error-handling.md) .</span><span class="sxs-lookup"><span data-stu-id="7f128-225">See **Using the Developer Exception Page** in [Handle errors](../fundamentals/error-handling.md) for more information.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7f128-226">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="7f128-226">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>
