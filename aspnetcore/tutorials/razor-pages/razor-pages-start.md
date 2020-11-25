---
title: 'Esercitazione: Introduzione alle Razor pagine in ASP.NET Core'
author: rick-anderson
description: Questa è la prima esercitazione di una serie che illustra le nozioni di base per la creazione di un' Razor app Web di ASP.NET Core Pages.
ms.author: riande
ms.date: 09/15/2020
no-loc:
- Index
- Create
- Delete
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: fa113a3e0a2a69fb4aa1318056dcfc6e261490f6
ms.sourcegitcommit: 8b867c4cb0c3b39bbc4d2d87815610d2ef858ae7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "96025026"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="130e6-103">Esercitazione: Introduzione alle Razor pagine in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="130e6-103">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="130e6-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="130e6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="130e6-105">Questa è la prima esercitazione di una serie che illustra le nozioni di base per la creazione di un' Razor app Web di ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="130e6-105">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="130e6-106">Per un'introduzione più avanzata destinata agli sviluppatori che hanno familiarità con i controller e le visualizzazioni, vedere [Introduzione alle Razor pagine](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="130e6-106">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="130e6-107">Al termine della serie si otterrà un'app che gestisce un database di film.</span><span class="sxs-lookup"><span data-stu-id="130e6-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="130e6-108">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="130e6-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="130e6-109">In questa esercitazione:</span><span class="sxs-lookup"><span data-stu-id="130e6-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="130e6-110">Create un' Razor app Web di pagine.</span><span class="sxs-lookup"><span data-stu-id="130e6-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="130e6-111">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="130e6-111">Run the app.</span></span>
> * <span data-ttu-id="130e6-112">Esaminare i file di progetto.</span><span class="sxs-lookup"><span data-stu-id="130e6-112">Examine the project files.</span></span>

<span data-ttu-id="130e6-113">Al termine di questa esercitazione, si disporrà di un' Razor app Web di pagine di lavoro che verrà migliorata nelle esercitazioni successive.</span><span class="sxs-lookup"><span data-stu-id="130e6-113">At the end of this tutorial, you'll have a working Razor Pages web app that you'll enhance in later tutorials.</span></span>

![Home o::: NO-LOC (index)::: page](razor-pages-start/_static/5/home5.png)

## <a name="prerequisites"></a><span data-ttu-id="130e6-115">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="130e6-115">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="130e6-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="130e6-116">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="130e6-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="130e6-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="130e6-118">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="130e6-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a><span data-ttu-id="130e6-119">CreateRazorapp Web di pagine</span><span class="sxs-lookup"><span data-stu-id="130e6-119">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="130e6-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="130e6-120">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="130e6-121">Avviare Visual Studio e selezionare **Create un nuovo progetto**.</span><span class="sxs-lookup"><span data-stu-id="130e6-121">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="130e6-122">Per ulteriori informazioni, vedere [ Create un nuovo progetto in Visual Studio](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="130e6-122">For more information, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

   ![::: NO-LOC (Create)::: un nuovo progetto dalla finestra di avvio](razor-pages-start/_static/5/start-window-create-new-project.png)

1. <span data-ttu-id="130e6-124">Nella finestra di dialogo **Create nuovo progetto** selezionare **ASP.NET Core applicazione Web** e quindi fare clic su **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="130e6-124">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

    ![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/5/np.png)
    
1. <span data-ttu-id="130e6-126">Nella finestra di dialogo **Configura nuovo progetto** immettere `RazorPagesMovie` per **nome progetto**.</span><span class="sxs-lookup"><span data-stu-id="130e6-126">In the **Configure your new project** dialog, enter `RazorPagesMovie` for **Project name**.</span></span> <span data-ttu-id="130e6-127">È importante denominare il progetto *Razor PagesMovie*, inclusa la corrispondenza con le maiuscole, in modo che gli spazi dei nomi corrispondano quando si copia e incolla il codice di esempio.</span><span class="sxs-lookup"><span data-stu-id="130e6-127">It's important to name the project *RazorPagesMovie*, including matching the capitalization, so the namespaces will match when you copy and paste example code.</span></span>

1. <span data-ttu-id="130e6-128">Selezionare **Create**.</span><span class="sxs-lookup"><span data-stu-id="130e6-128">Select **Create**.</span></span>

    ![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/config.png)

1. <span data-ttu-id="130e6-130">Nella finestra di dialogo **Create nuova applicazione Web ASP.NET Core** Selezionare:</span><span class="sxs-lookup"><span data-stu-id="130e6-130">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="130e6-131">**.NET Core** e **ASP.NET Core 5,0** negli elenchi a discesa.</span><span class="sxs-lookup"><span data-stu-id="130e6-131">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="130e6-132">**Applicazione Web**.</span><span class="sxs-lookup"><span data-stu-id="130e6-132">**Web Application**.</span></span>
    1. <span data-ttu-id="130e6-133">**Create**.</span><span class="sxs-lookup"><span data-stu-id="130e6-133">**Create**.</span></span>

     ![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/5/npx.png)

    <span data-ttu-id="130e6-135">Viene creato il progetto iniziale seguente:</span><span class="sxs-lookup"><span data-stu-id="130e6-135">The following starter project is created:</span></span>

    ![Esplora soluzioni](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="130e6-137">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="130e6-137">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="130e6-138">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="130e6-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

1. <span data-ttu-id="130e6-139">Passare alla directory (`cd`) che conterrà il progetto.</span><span class="sxs-lookup"><span data-stu-id="130e6-139">Change to the directory (`cd`) which will contain the project.</span></span>

1. <span data-ttu-id="130e6-140">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="130e6-140">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

   * <span data-ttu-id="130e6-141">Il `dotnet new` comando crea un nuovo Razor progetto di pagine nella cartella *Razor PagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="130e6-141">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
   * <span data-ttu-id="130e6-142">Il `code` comando apre la cartella *Razor PagesMovie* nell'istanza corrente di Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="130e6-142">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="130e6-143">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="130e6-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="130e6-144">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="130e6-144">Select **File** > **New Solution**.</span></span>

    ![Nuova soluzione macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

1. <span data-ttu-id="130e6-146">In Visual Studio per Mac precedente alla versione 8,6 selezionare **.NET Core**  >  **App**  >  **applicazione Web** app .NET Core  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="130e6-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="130e6-147">Nella versione 8,6 o successive selezionare applicazione Web **e**  >  **app**  >  **Web dell'** app console  >  **successiva**.</span><span class="sxs-lookup"><span data-stu-id="130e6-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

    ![selezione del modello di app Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

1. <span data-ttu-id="130e6-149">Nella finestra di dialogo **Configura nuova applicazione Web** :</span><span class="sxs-lookup"><span data-stu-id="130e6-149">In the **Configure the new Web Application** dialog:</span></span>

    1. <span data-ttu-id="130e6-150">Verificare che **l'autenticazione** sia impostata su **Nessuna autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="130e6-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
    1. <span data-ttu-id="130e6-151">Se viene visualizzata un'opzione per la selezione di un **Framework di destinazione**, selezionare la versione più recente di .NET 5. x.</span><span class="sxs-lookup"><span data-stu-id="130e6-151">If presented an option to select a **Target Framework**, select the latest .NET 5.x version.</span></span>
    1. <span data-ttu-id="130e6-152">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="130e6-152">Select **Next**.</span></span>

1. <span data-ttu-id="130e6-153">Denominare il progetto *Razor PagesMovie* e selezionare **Create** .</span><span class="sxs-lookup"><span data-stu-id="130e6-153">Name the project *RazorPagesMovie* and select **Create**.</span></span>

    ![macOS nome del progetto](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="130e6-155">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="130e6-155">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="130e6-156">Esaminare i file di progetto</span><span class="sxs-lookup"><span data-stu-id="130e6-156">Examine the project files</span></span>

<span data-ttu-id="130e6-157">Di seguito viene visualizzata una panoramica delle cartelle e dei file principali del progetto, che verranno usati in esercitazioni successive.</span><span class="sxs-lookup"><span data-stu-id="130e6-157">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="130e6-158">Cartella Pages</span><span class="sxs-lookup"><span data-stu-id="130e6-158">Pages folder</span></span>

<span data-ttu-id="130e6-159">Contiene Razor pagine e file di supporto.</span><span class="sxs-lookup"><span data-stu-id="130e6-159">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="130e6-160">Ogni Razor pagina è una coppia di file:</span><span class="sxs-lookup"><span data-stu-id="130e6-160">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="130e6-161">Un file con *estensione cshtml* con markup HTML con codice C# che usa la Razor sintassi.</span><span class="sxs-lookup"><span data-stu-id="130e6-161">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="130e6-162">Un file con *estensione cshtml.cs* con codice C# che gestisce gli eventi di pagina.</span><span class="sxs-lookup"><span data-stu-id="130e6-162">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="130e6-163">I nomi dei file di supporto iniziano con un carattere di sottolineatura.</span><span class="sxs-lookup"><span data-stu-id="130e6-163">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="130e6-164">Ad esempio, il file *_Layout. cshtml* configura gli elementi dell'interfaccia utente comuni a tutte le pagine.</span><span class="sxs-lookup"><span data-stu-id="130e6-164">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="130e6-165">Questo file imposta il menu di navigazione nella parte superiore della pagina e le informazioni sul copyright in fondo alla pagina.</span><span class="sxs-lookup"><span data-stu-id="130e6-165">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="130e6-166">Per altre informazioni, vedere <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="130e6-166">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="130e6-167">Cartella wwwroot</span><span class="sxs-lookup"><span data-stu-id="130e6-167">wwwroot folder</span></span>

<span data-ttu-id="130e6-168">Contiene risorse statiche, ad esempio file HTML, file JavaScript e file CSS.</span><span class="sxs-lookup"><span data-stu-id="130e6-168">Contains static assets, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="130e6-169">Per altre informazioni, vedere <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="130e6-169">For more information, see <xref:fundamentals/static-files>.</span></span>

### appsettings.json

<span data-ttu-id="130e6-170">Contiene i dati di configurazione, come le stringhe di connessione.</span><span class="sxs-lookup"><span data-stu-id="130e6-170">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="130e6-171">Per altre informazioni, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="130e6-171">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="130e6-172">Program.cs</span><span class="sxs-lookup"><span data-stu-id="130e6-172">Program.cs</span></span>

<span data-ttu-id="130e6-173">Contiene il punto di ingresso per l'app.</span><span class="sxs-lookup"><span data-stu-id="130e6-173">Contains the entry point for the app.</span></span> <span data-ttu-id="130e6-174">Per altre informazioni, vedere <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="130e6-174">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="130e6-175">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="130e6-175">Startup.cs</span></span>

<span data-ttu-id="130e6-176">Contiene codice che consente di configurare il comportamento dell'app.</span><span class="sxs-lookup"><span data-stu-id="130e6-176">Contains code that configures app behavior.</span></span> <span data-ttu-id="130e6-177">Per altre informazioni, vedere <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="130e6-177">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="130e6-178">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="130e6-178">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="130e6-179">Passaggio successivo: aggiungere un modello</span><span class="sxs-lookup"><span data-stu-id="130e6-179">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-5.0" -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="130e6-180">Questa è la prima esercitazione di una serie che illustra le nozioni di base per la creazione di un' Razor app Web di ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="130e6-180">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="130e6-181">Per un'introduzione più avanzata destinata agli sviluppatori che hanno familiarità con i controller e le visualizzazioni, vedere [Introduzione alle Razor pagine](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="130e6-181">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="130e6-182">Al termine della serie si otterrà un'app che gestisce un database di film.</span><span class="sxs-lookup"><span data-stu-id="130e6-182">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="130e6-183">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="130e6-183">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="130e6-184">In questa esercitazione:</span><span class="sxs-lookup"><span data-stu-id="130e6-184">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="130e6-185">Create un' Razor app Web di pagine.</span><span class="sxs-lookup"><span data-stu-id="130e6-185">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="130e6-186">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="130e6-186">Run the app.</span></span>
> * <span data-ttu-id="130e6-187">Esaminare i file di progetto.</span><span class="sxs-lookup"><span data-stu-id="130e6-187">Examine the project files.</span></span>

<span data-ttu-id="130e6-188">Al termine di questa esercitazione, si disporrà di un' Razor app Web di pagine di lavoro che verrà compilata nelle esercitazioni successive.</span><span class="sxs-lookup"><span data-stu-id="130e6-188">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Home o::: NO-LOC (index)::: page](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="130e6-190">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="130e6-190">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="130e6-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="130e6-191">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="130e6-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="130e6-192">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="130e6-193">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="130e6-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a><span data-ttu-id="130e6-194">CreateRazorapp Web di pagine</span><span class="sxs-lookup"><span data-stu-id="130e6-194">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="130e6-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="130e6-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="130e6-196">Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.</span><span class="sxs-lookup"><span data-stu-id="130e6-196">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="130e6-197">Create un nuovo ASP.NET Core applicazione Web e selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="130e6-197">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="130e6-198">![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="130e6-198">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="130e6-199">Denominare il progetto **Razor PagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="130e6-199">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="130e6-200">È importante denominare il progetto *Razor PagesMovie* in modo che gli spazi dei nomi corrispondano quando si copia e incolla il codice.</span><span class="sxs-lookup"><span data-stu-id="130e6-200">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="130e6-201">![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="130e6-201">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="130e6-202">Selezionare **ASP.NET Core 3,1** nell'elenco a discesa, **applicazione Web**, quindi selezionare **Create** .</span><span class="sxs-lookup"><span data-stu-id="130e6-202">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="130e6-204">Viene creato il progetto iniziale seguente:</span><span class="sxs-lookup"><span data-stu-id="130e6-204">The following starter project is created:</span></span>

  ![Esplora soluzioni](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="130e6-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="130e6-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="130e6-207">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="130e6-207">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="130e6-208">Passare alla directory (`cd`) che conterrà il progetto.</span><span class="sxs-lookup"><span data-stu-id="130e6-208">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="130e6-209">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="130e6-209">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="130e6-210">Il `dotnet new` comando crea un nuovo Razor progetto di pagine nella cartella *Razor PagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="130e6-210">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="130e6-211">Il `code` comando apre la cartella *Razor PagesMovie* nell'istanza corrente di Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="130e6-211">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="130e6-212">Dopo che l'icona di OmniSharp Flame della barra di stato è verde, una finestra di dialogo richiede che le **risorse necessarie per la compilazione e il debug siano mancanti da' Razor PagesMovie '. Aggiungerli?**</span><span class="sxs-lookup"><span data-stu-id="130e6-212">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="130e6-213">Selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="130e6-213">Select **Yes**.</span></span>

  <span data-ttu-id="130e6-214">Una directory *.vscode* che contiene i file *launch.json* e *tasks.json* viene aggiunta alla directory radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="130e6-214">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="130e6-215">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="130e6-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="130e6-216">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="130e6-216">Select **File** > **New Solution**.</span></span>

  ![Nuova soluzione macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="130e6-218">In Visual Studio per Mac precedente alla versione 8,6 selezionare **.NET Core**  >  **App**  >  **applicazione Web** app .NET Core  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="130e6-218">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="130e6-219">Nella versione 8,6 o successive selezionare applicazione Web **e**  >  **app**  >  **Web dell'** app console  >  **successiva**.</span><span class="sxs-lookup"><span data-stu-id="130e6-219">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![selezione del modello di app Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="130e6-221">Nella finestra di dialogo **Configura nuova applicazione Web** :</span><span class="sxs-lookup"><span data-stu-id="130e6-221">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="130e6-222">Verificare che **l'autenticazione** sia impostata su **Nessuna autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="130e6-222">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="130e6-223">Se viene visualizzata un'opzione per la selezione di un **Framework di destinazione**, selezionare la versione 3. x più recente.</span><span class="sxs-lookup"><span data-stu-id="130e6-223">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="130e6-224">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="130e6-224">Select **Next**.</span></span>

* <span data-ttu-id="130e6-225">Assegnare al progetto il nome **Razor PagesMovie** e quindi selezionare **Create** .</span><span class="sxs-lookup"><span data-stu-id="130e6-225">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![macOS nome del progetto](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="130e6-227">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="130e6-227">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="130e6-228">Esaminare i file di progetto</span><span class="sxs-lookup"><span data-stu-id="130e6-228">Examine the project files</span></span>

<span data-ttu-id="130e6-229">Di seguito viene visualizzata una panoramica delle cartelle e dei file principali del progetto, che verranno usati in esercitazioni successive.</span><span class="sxs-lookup"><span data-stu-id="130e6-229">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="130e6-230">Cartella Pages</span><span class="sxs-lookup"><span data-stu-id="130e6-230">Pages folder</span></span>

<span data-ttu-id="130e6-231">Contiene Razor pagine e file di supporto.</span><span class="sxs-lookup"><span data-stu-id="130e6-231">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="130e6-232">Ogni Razor pagina è una coppia di file:</span><span class="sxs-lookup"><span data-stu-id="130e6-232">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="130e6-233">Un file con *estensione cshtml* con markup HTML con codice C# che usa la Razor sintassi.</span><span class="sxs-lookup"><span data-stu-id="130e6-233">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="130e6-234">Un file con *estensione cshtml.cs* con codice C# che gestisce gli eventi di pagina.</span><span class="sxs-lookup"><span data-stu-id="130e6-234">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="130e6-235">I nomi dei file di supporto iniziano con un carattere di sottolineatura.</span><span class="sxs-lookup"><span data-stu-id="130e6-235">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="130e6-236">Ad esempio, il file *_Layout. cshtml* configura gli elementi dell'interfaccia utente comuni a tutte le pagine.</span><span class="sxs-lookup"><span data-stu-id="130e6-236">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="130e6-237">Questo file imposta il menu di navigazione nella parte superiore della pagina e le informazioni sul copyright in fondo alla pagina.</span><span class="sxs-lookup"><span data-stu-id="130e6-237">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="130e6-238">Per altre informazioni, vedere <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="130e6-238">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="130e6-239">Cartella wwwroot</span><span class="sxs-lookup"><span data-stu-id="130e6-239">wwwroot folder</span></span>

<span data-ttu-id="130e6-240">Contiene i file statici, ad esempio i file HTML, i file JavaScript e i file CSS.</span><span class="sxs-lookup"><span data-stu-id="130e6-240">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="130e6-241">Per altre informazioni, vedere <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="130e6-241">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="130e6-242">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="130e6-242">appSettings.json</span></span>

<span data-ttu-id="130e6-243">Contiene i dati di configurazione, come le stringhe di connessione.</span><span class="sxs-lookup"><span data-stu-id="130e6-243">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="130e6-244">Per altre informazioni, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="130e6-244">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="130e6-245">Program.cs</span><span class="sxs-lookup"><span data-stu-id="130e6-245">Program.cs</span></span>

<span data-ttu-id="130e6-246">Contiene il punto di ingresso per il programma.</span><span class="sxs-lookup"><span data-stu-id="130e6-246">Contains the entry point for the program.</span></span> <span data-ttu-id="130e6-247">Per altre informazioni, vedere <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="130e6-247">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="130e6-248">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="130e6-248">Startup.cs</span></span>

<span data-ttu-id="130e6-249">Contiene codice che consente di configurare il comportamento dell'app.</span><span class="sxs-lookup"><span data-stu-id="130e6-249">Contains code that configures app behavior.</span></span> <span data-ttu-id="130e6-250">Per altre informazioni, vedere <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="130e6-250">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="130e6-251">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="130e6-251">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="130e6-252">Passaggio successivo: aggiungere un modello</span><span class="sxs-lookup"><span data-stu-id="130e6-252">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="130e6-253">Questa è la prima esercitazione di una serie.</span><span class="sxs-lookup"><span data-stu-id="130e6-253">This is the first tutorial of a series.</span></span> <span data-ttu-id="130e6-254">[La serie](xref:tutorials/razor-pages/index) illustra le nozioni di base per la creazione di un' Razor app Web di ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="130e6-254">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="130e6-255">Per un'introduzione più avanzata destinata agli sviluppatori che hanno familiarità con i controller e le visualizzazioni, vedere [Introduzione alle Razor pagine](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="130e6-255">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="130e6-256">Al termine della serie si otterrà un'app che gestisce un database di film.</span><span class="sxs-lookup"><span data-stu-id="130e6-256">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="130e6-257">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="130e6-257">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="130e6-258">In questa esercitazione:</span><span class="sxs-lookup"><span data-stu-id="130e6-258">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="130e6-259">Create un' Razor app Web di pagine.</span><span class="sxs-lookup"><span data-stu-id="130e6-259">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="130e6-260">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="130e6-260">Run the app.</span></span>
> * <span data-ttu-id="130e6-261">Esaminare i file di progetto.</span><span class="sxs-lookup"><span data-stu-id="130e6-261">Examine the project files.</span></span>

<span data-ttu-id="130e6-262">Al termine di questa esercitazione, si disporrà di un' Razor app Web di pagine di lavoro che verrà compilata nelle esercitazioni successive.</span><span class="sxs-lookup"><span data-stu-id="130e6-262">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Home o::: NO-LOC (index)::: page](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="130e6-264">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="130e6-264">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="130e6-265">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="130e6-265">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="130e6-266">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="130e6-266">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="130e6-267">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="130e6-267">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a><span data-ttu-id="130e6-268">CreateRazorapp Web di pagine</span><span class="sxs-lookup"><span data-stu-id="130e6-268">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="130e6-269">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="130e6-269">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="130e6-270">Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.</span><span class="sxs-lookup"><span data-stu-id="130e6-270">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="130e6-271">Create un nuovo ASP.NET Core applicazione Web e selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="130e6-271">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="130e6-273">Denominare il progetto **Razor PagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="130e6-273">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="130e6-274">È importante denominare il progetto *Razor PagesMovie* in modo che gli spazi dei nomi corrispondano quando si copia e incolla il codice.</span><span class="sxs-lookup"><span data-stu-id="130e6-274">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="130e6-276">Selezionare **ASP.NET Core 2,2** nell'elenco a discesa, **applicazione Web**, quindi selezionare **Create** .</span><span class="sxs-lookup"><span data-stu-id="130e6-276">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="130e6-278">Viene creato il progetto iniziale seguente:</span><span class="sxs-lookup"><span data-stu-id="130e6-278">The following starter project is created:</span></span>

  ![Esplora soluzioni](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="130e6-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="130e6-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="130e6-281">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="130e6-281">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="130e6-282">Passare alla directory (`cd`) che conterrà il progetto.</span><span class="sxs-lookup"><span data-stu-id="130e6-282">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="130e6-283">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="130e6-283">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="130e6-284">Il `dotnet new` comando crea un nuovo Razor progetto di pagine nella cartella *Razor PagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="130e6-284">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="130e6-285">Il `code` comando apre la cartella *Razor PagesMovie* nell'istanza corrente di Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="130e6-285">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="130e6-286">Dopo che l'icona di OmniSharp Flame della barra di stato è verde, una finestra di dialogo richiede che le **risorse necessarie per la compilazione e il debug siano mancanti da' Razor PagesMovie '. Aggiungerli?**</span><span class="sxs-lookup"><span data-stu-id="130e6-286">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="130e6-287">Selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="130e6-287">Select **Yes**.</span></span>

  <span data-ttu-id="130e6-288">Una directory *.vscode* che contiene i file *launch.json* e *tasks.json* viene aggiunta alla directory radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="130e6-288">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="130e6-289">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="130e6-289">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="130e6-290">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="130e6-290">Select **File** > **New Solution**.</span></span>

![Nuova soluzione macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="130e6-292">In Visual Studio per Mac precedente alla versione 8,6 selezionare **.NET Core**  >  **App**  >  **applicazione Web** app .NET Core  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="130e6-292">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="130e6-293">Nella versione 8,6 o successive selezionare applicazione Web **e**  >  **app**  >  **Web dell'** app console  >  **successiva**.</span><span class="sxs-lookup"><span data-stu-id="130e6-293">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="130e6-294">Nella finestra di dialogo **Configura nuova applicazione Web** :</span><span class="sxs-lookup"><span data-stu-id="130e6-294">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="130e6-295">Verificare che **l'autenticazione** sia impostata su **Nessuna autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="130e6-295">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="130e6-296">Se viene visualizzata un'opzione per la selezione di un **Framework di destinazione**, selezionare la versione 2. x più recente.</span><span class="sxs-lookup"><span data-stu-id="130e6-296">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="130e6-297">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="130e6-297">Select **Next**.</span></span>

* <span data-ttu-id="130e6-298">Assegnare al progetto il nome **Razor PagesMovie** e quindi selezionare **Create** .</span><span class="sxs-lookup"><span data-stu-id="130e6-298">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="130e6-300">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="130e6-300">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="130e6-301">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="130e6-301">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="130e6-302">Premere CTRL+F5 per l'esecuzione senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="130e6-302">Press Ctrl+F5 to run without the debugger.</span></span>

  <span data-ttu-id="130e6-303">L'avvio dell'app con <kbd>CTRL + F5</kbd> (modalità non di debug) consente di apportare modifiche al codice, salvare il file, aggiornare il browser e visualizzare le modifiche apportate al codice.</span><span class="sxs-lookup"><span data-stu-id="130e6-303">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="130e6-304">Molti sviluppatori preferiscono usare la modalità non di debug per avviare l'app rapidamente e visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="130e6-304">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="130e6-305">Visual Studio avvia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ed esegue l'app.</span><span class="sxs-lookup"><span data-stu-id="130e6-305">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="130e6-306">La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="130e6-306">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="130e6-307">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="130e6-307">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="130e6-308">Localhost viene usato solo per le richieste web del computer locale.</span><span class="sxs-lookup"><span data-stu-id="130e6-308">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="130e6-309">Quando Visual Studio crea un progetto Web, viene usata una porta casuale per il server Web.</span><span class="sxs-lookup"><span data-stu-id="130e6-309">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="130e6-310">Nella home page dell'app selezionare **Accetto** per autorizzare il rilevamento.</span><span class="sxs-lookup"><span data-stu-id="130e6-310">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="130e6-311">Questa app non tiene traccia delle informazioni personali, ma il modello di progetto include la funzionalità di consenso nel caso in cui risulti necessaria la conformità al [Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="130e6-311">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home o::: NO-LOC (index)::: page](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="130e6-313">L'immagine seguente mostra l'app dopo il consenso al rilevamento:</span><span class="sxs-lookup"><span data-stu-id="130e6-313">The following image shows the app after consent to tracking is provided:</span></span>

  ![Home o::: NO-LOC (index)::: page](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="130e6-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="130e6-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="130e6-316">Premere <kbd>CTRL + F5</kbd> per eseguire senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="130e6-316">Press <kbd>Ctrl+F5</kbd> to run without the debugger.</span></span>

  <span data-ttu-id="130e6-317">L'avvio dell'app con <kbd>CTRL + F5</kbd> (modalità non di debug) consente di apportare modifiche al codice, salvare il file, aggiornare il browser e visualizzare le modifiche apportate al codice.</span><span class="sxs-lookup"><span data-stu-id="130e6-317">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="130e6-318">Molti sviluppatori preferiscono usare la modalità non di debug per avviare l'app rapidamente e visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="130e6-318">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="130e6-319">Visual Studio Code avvia [gheppio](xref:fundamentals/servers/kestrel), avvia un browser e passa a `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="130e6-319">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span> <span data-ttu-id="130e6-320">La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="130e6-320">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="130e6-321">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="130e6-321">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="130e6-322">Localhost viene usato solo per le richieste web del computer locale.</span><span class="sxs-lookup"><span data-stu-id="130e6-322">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="130e6-323">Nella home page dell'app selezionare **Accetto** per autorizzare il rilevamento.</span><span class="sxs-lookup"><span data-stu-id="130e6-323">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="130e6-324">Questa app non tiene traccia delle informazioni personali, ma il modello di progetto include la funzionalità di consenso nel caso in cui risulti necessaria la conformità al [Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="130e6-324">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home o::: NO-LOC (index)::: page](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="130e6-326">La figura seguente visualizza l'app dopo che è stato impostato il consenso al rilevamento:</span><span class="sxs-lookup"><span data-stu-id="130e6-326">The following image shows the app after you give consent to tracking:</span></span>

  ![Home o::: NO-LOC (index)::: page](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="130e6-328">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="130e6-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="130e6-329">Premere **Cmd-Opt-F5** per l'esecuzione senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="130e6-329">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="130e6-330">L'avvio dell'app con <kbd>cmd + opt + F5</kbd> (modalità non di debug) consente di apportare modifiche al codice, salvare il file, aggiornare il browser e visualizzare le modifiche del codice.</span><span class="sxs-lookup"><span data-stu-id="130e6-330">Launching the app with <kbd>Cmd+Opt+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="130e6-331">Molti sviluppatori preferiscono usare la modalità non di debug per avviare l'app rapidamente e visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="130e6-331">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="130e6-332">Visual Studio avvia [gheppio](xref:fundamentals/servers/kestrel), avvia un browser e passa a `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="130e6-332">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span>

* <span data-ttu-id="130e6-333">Nella home page dell'app selezionare **Accetto** per autorizzare il rilevamento.</span><span class="sxs-lookup"><span data-stu-id="130e6-333">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="130e6-334">Questa app non tiene traccia delle informazioni personali, ma il modello di progetto include la funzionalità di consenso nel caso in cui risulti necessaria la conformità al [Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="130e6-334">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Home o::: NO-LOC (index)::: page](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="130e6-336">L'immagine seguente mostra l'app dopo il consenso al rilevamento:</span><span class="sxs-lookup"><span data-stu-id="130e6-336">The following image shows the app after consent to tracking is provided:</span></span>

  ![Home o::: NO-LOC (index)::: page](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="130e6-338">Esaminare i file di progetto</span><span class="sxs-lookup"><span data-stu-id="130e6-338">Examine the project files</span></span>

<span data-ttu-id="130e6-339">Di seguito viene visualizzata una panoramica delle cartelle e dei file principali del progetto, che verranno usati in esercitazioni successive.</span><span class="sxs-lookup"><span data-stu-id="130e6-339">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="130e6-340">Cartella Pages</span><span class="sxs-lookup"><span data-stu-id="130e6-340">Pages folder</span></span>

<span data-ttu-id="130e6-341">Contiene Razor pagine e file di supporto.</span><span class="sxs-lookup"><span data-stu-id="130e6-341">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="130e6-342">Ogni Razor pagina è una coppia di file:</span><span class="sxs-lookup"><span data-stu-id="130e6-342">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="130e6-343">Un file con *estensione cshtml* con markup HTML con codice C# che usa la Razor sintassi.</span><span class="sxs-lookup"><span data-stu-id="130e6-343">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="130e6-344">Un file con *estensione cshtml.cs* con codice C# che gestisce gli eventi di pagina.</span><span class="sxs-lookup"><span data-stu-id="130e6-344">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="130e6-345">I nomi dei file di supporto iniziano con un carattere di sottolineatura.</span><span class="sxs-lookup"><span data-stu-id="130e6-345">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="130e6-346">Ad esempio, il file *_Layout. cshtml* configura gli elementi dell'interfaccia utente comuni a tutte le pagine.</span><span class="sxs-lookup"><span data-stu-id="130e6-346">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="130e6-347">Questo file imposta il menu di navigazione nella parte superiore della pagina e le informazioni sul copyright in fondo alla pagina.</span><span class="sxs-lookup"><span data-stu-id="130e6-347">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="130e6-348">Per altre informazioni, vedere <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="130e6-348">For more information, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="130e6-349">Razor Le pagine sono derivate da `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="130e6-349">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="130e6-350">Per convenzione, la `PageModel` classe derivata da è denominata `<PageName>Model` .</span><span class="sxs-lookup"><span data-stu-id="130e6-350">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="130e6-351">Cartella wwwroot</span><span class="sxs-lookup"><span data-stu-id="130e6-351">wwwroot folder</span></span>

<span data-ttu-id="130e6-352">Contiene i file statici, ad esempio i file HTML, i file JavaScript e i file CSS.</span><span class="sxs-lookup"><span data-stu-id="130e6-352">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="130e6-353">Per altre informazioni, vedere <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="130e6-353">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="130e6-354">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="130e6-354">appSettings.json</span></span>

<span data-ttu-id="130e6-355">Contiene i dati di configurazione, come le stringhe di connessione.</span><span class="sxs-lookup"><span data-stu-id="130e6-355">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="130e6-356">Per altre informazioni, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="130e6-356">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="130e6-357">Program.cs</span><span class="sxs-lookup"><span data-stu-id="130e6-357">Program.cs</span></span>

<span data-ttu-id="130e6-358">Contiene il punto di ingresso per il programma.</span><span class="sxs-lookup"><span data-stu-id="130e6-358">Contains the entry point for the program.</span></span> <span data-ttu-id="130e6-359">Per altre informazioni, vedere <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="130e6-359">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="130e6-360">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="130e6-360">Startup.cs</span></span>

<span data-ttu-id="130e6-361">Contiene il codice che configura il comportamento dell'app, ad esempio se richiede il consenso per cookie s.</span><span class="sxs-lookup"><span data-stu-id="130e6-361">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="130e6-362">Per altre informazioni, vedere <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="130e6-362">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="130e6-363">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="130e6-363">Additional resources</span></span>

* [<span data-ttu-id="130e6-364">Versione YouTube dell'esercitazione</span><span class="sxs-lookup"><span data-stu-id="130e6-364">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="130e6-365">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="130e6-365">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="130e6-366">Passaggio successivo: aggiungere un modello</span><span class="sxs-lookup"><span data-stu-id="130e6-366">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
