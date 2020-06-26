---
title: 'Esercitazione: Introduzione alle Razor pagine in ASP.NET Core'
author: rick-anderson
description: Questa serie di esercitazioni illustra come usare le Razor pagine in ASP.NET Core. Informazioni su come creare un modello, generare codice per Razor le pagine, usare Entity Framework Core e SQL Server per l'accesso ai dati, aggiungere funzionalità di ricerca, aggiungere la convalida dell'input e usare le migrazioni per aggiornare il modello.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 97e3f60480bc8e7e88c8361e5b13f02d98765d9e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405302"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="468fa-104">Esercitazione: Introduzione alle Razor pagine in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="468fa-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="468fa-105">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="468fa-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="468fa-106">Questa è la prima esercitazione di una serie che illustra le nozioni di base per la creazione di un' Razor app Web di ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="468fa-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="468fa-107">Al termine della serie si otterrà un'app che gestisce un database di film.</span><span class="sxs-lookup"><span data-stu-id="468fa-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="468fa-108">In questa esercitazione:</span><span class="sxs-lookup"><span data-stu-id="468fa-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="468fa-109">Creare un' Razor app Web di pagine.</span><span class="sxs-lookup"><span data-stu-id="468fa-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="468fa-110">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="468fa-110">Run the app.</span></span>
> * <span data-ttu-id="468fa-111">Esaminare i file di progetto.</span><span class="sxs-lookup"><span data-stu-id="468fa-111">Examine the project files.</span></span>

<span data-ttu-id="468fa-112">Al termine di questa esercitazione, si disporrà di un' Razor app Web di pagine di lavoro che verrà compilata nelle esercitazioni successive.</span><span class="sxs-lookup"><span data-stu-id="468fa-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Pagina Home o di indice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="468fa-114">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="468fa-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="468fa-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="468fa-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="468fa-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="468fa-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="468fa-117">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="468fa-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="468fa-118">Creare un' Razor app Web di pagine</span><span class="sxs-lookup"><span data-stu-id="468fa-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="468fa-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="468fa-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="468fa-120">Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.</span><span class="sxs-lookup"><span data-stu-id="468fa-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="468fa-121">Creare una nuova applicazione Web ASP.NET Core e selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="468fa-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="468fa-122">![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="468fa-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="468fa-123">Denominare il progetto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="468fa-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="468fa-124">È importante denominare il progetto *RazorPagesMovie* in modo che gli spazi dei nomi corrispondano nell'operazione copia/incolla del codice.</span><span class="sxs-lookup"><span data-stu-id="468fa-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="468fa-125">![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="468fa-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="468fa-126">Selezionare **ASP.NET Core 3,1** nell'elenco a discesa, **applicazione Web**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="468fa-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="468fa-128">Viene creato il progetto iniziale seguente:</span><span class="sxs-lookup"><span data-stu-id="468fa-128">The following starter project is created:</span></span>

  ![Esplora soluzioni](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="468fa-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="468fa-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="468fa-131">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="468fa-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="468fa-132">Passare alla directory (`cd`) che conterrà il progetto.</span><span class="sxs-lookup"><span data-stu-id="468fa-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="468fa-133">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="468fa-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="468fa-134">Il `dotnet new` comando crea un nuovo Razor progetto di pagine nella cartella *RazorPagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="468fa-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="468fa-135">Il comando `code` apre la cartella *RazorPagesMovie* nell'istanza corrente di Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="468fa-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="468fa-136">Dopo che l'icona di OmniSharp Flame della barra di stato è verde, una finestra di dialogo richiede che le **risorse necessarie per la compilazione e il debug siano mancanti da' RazorPagesMovie '. Aggiungerli?**</span><span class="sxs-lookup"><span data-stu-id="468fa-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="468fa-137">Selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="468fa-137">Select **Yes**.</span></span>

  <span data-ttu-id="468fa-138">Una directory *.vscode* che contiene i file *launch.json* e *tasks.json* viene aggiunta alla directory radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="468fa-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="468fa-139">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="468fa-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="468fa-140">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="468fa-140">Select **File** > **New Solution**.</span></span>

  ![Nuova soluzione macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="468fa-142">In Visual Studio per Mac precedente alla versione 8,6 selezionare **.NET Core**  >  **App**  >  **applicazione Web**app .NET Core  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="468fa-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="468fa-143">Nella versione 8,6 o successive selezionare applicazione Web **e**  >  **app**  >  **Web dell'** app console  >  **successiva**.</span><span class="sxs-lookup"><span data-stu-id="468fa-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![selezione del modello di app Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="468fa-145">Verificare le configurazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="468fa-145">Confirm the following configurations:</span></span>

  * <span data-ttu-id="468fa-146">Il **Framework di destinazione** è impostato su **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="468fa-146">**Target Framework** set to **.NET Core 3.1**.</span></span>
  * <span data-ttu-id="468fa-147">**L'autenticazione** è impostata su **Nessuna autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="468fa-147">**Authentication** set to **No Authentication**.</span></span>
   
  <span data-ttu-id="468fa-148">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="468fa-148">Select **Next**.</span></span>

  ![selezione di macOS .NET Core 3,1](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="468fa-150">Denominare il progetto **RazorPagesMovie**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="468fa-150">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![macOS nome del progetto](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="468fa-152">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="468fa-152">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="468fa-153">Esaminare i file di progetto</span><span class="sxs-lookup"><span data-stu-id="468fa-153">Examine the project files</span></span>

<span data-ttu-id="468fa-154">Di seguito viene visualizzata una panoramica delle cartelle e dei file principali del progetto, che verranno usati in esercitazioni successive.</span><span class="sxs-lookup"><span data-stu-id="468fa-154">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="468fa-155">Cartella Pages</span><span class="sxs-lookup"><span data-stu-id="468fa-155">Pages folder</span></span>

<span data-ttu-id="468fa-156">Contiene Razor pagine e file di supporto.</span><span class="sxs-lookup"><span data-stu-id="468fa-156">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="468fa-157">Ogni Razor pagina è una coppia di file:</span><span class="sxs-lookup"><span data-stu-id="468fa-157">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="468fa-158">Un file con *estensione cshtml* che contiene markup HTML con codice C# che utilizza la Razor sintassi.</span><span class="sxs-lookup"><span data-stu-id="468fa-158">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="468fa-159">Un file con estensione *cshtml.cs* contenente il codice C# per la gestione degli eventi della pagina.</span><span class="sxs-lookup"><span data-stu-id="468fa-159">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="468fa-160">I nomi dei file di supporto iniziano con un carattere di sottolineatura.</span><span class="sxs-lookup"><span data-stu-id="468fa-160">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="468fa-161">Ad esempio, il file *_Layout. cshtml* configura gli elementi dell'interfaccia utente comuni a tutte le pagine.</span><span class="sxs-lookup"><span data-stu-id="468fa-161">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="468fa-162">Questo file imposta il menu di navigazione nella parte superiore della pagina e le informazioni sul copyright in fondo alla pagina.</span><span class="sxs-lookup"><span data-stu-id="468fa-162">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="468fa-163">Per altre informazioni, vedere <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="468fa-163">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="468fa-164">Cartella wwwroot</span><span class="sxs-lookup"><span data-stu-id="468fa-164">wwwroot folder</span></span>

<span data-ttu-id="468fa-165">Contiene i file statici, ad esempio i file HTML, i file JavaScript e i file CSS.</span><span class="sxs-lookup"><span data-stu-id="468fa-165">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="468fa-166">Per altre informazioni, vedere <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="468fa-166">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="468fa-167">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="468fa-167">appSettings.json</span></span>

<span data-ttu-id="468fa-168">Contiene i dati di configurazione, ad esempio le stringhe di connessione.</span><span class="sxs-lookup"><span data-stu-id="468fa-168">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="468fa-169">Per altre informazioni, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="468fa-169">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="468fa-170">Program.cs</span><span class="sxs-lookup"><span data-stu-id="468fa-170">Program.cs</span></span>

<span data-ttu-id="468fa-171">Contiene il punto di ingresso per il programma.</span><span class="sxs-lookup"><span data-stu-id="468fa-171">Contains the entry point for the program.</span></span> <span data-ttu-id="468fa-172">Per altre informazioni, vedere <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="468fa-172">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="468fa-173">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="468fa-173">Startup.cs</span></span>

<span data-ttu-id="468fa-174">Contiene codice che consente di configurare il comportamento dell'app.</span><span class="sxs-lookup"><span data-stu-id="468fa-174">Contains code that configures app behavior.</span></span> <span data-ttu-id="468fa-175">Per altre informazioni, vedere <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="468fa-175">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="468fa-176">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="468fa-176">Next steps</span></span>

<span data-ttu-id="468fa-177">Passare all'esercitazione successiva della serie:</span><span class="sxs-lookup"><span data-stu-id="468fa-177">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="468fa-178">Aggiungere un modello</span><span class="sxs-lookup"><span data-stu-id="468fa-178">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="468fa-179">Questa è la prima esercitazione di una serie.</span><span class="sxs-lookup"><span data-stu-id="468fa-179">This is the first tutorial of a series.</span></span> <span data-ttu-id="468fa-180">[La serie](xref:tutorials/razor-pages/index) illustra le nozioni di base per la creazione di un' Razor app Web di ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="468fa-180">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="468fa-181">Al termine della serie si otterrà un'app che gestisce un database di film.</span><span class="sxs-lookup"><span data-stu-id="468fa-181">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="468fa-182">In questa esercitazione:</span><span class="sxs-lookup"><span data-stu-id="468fa-182">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="468fa-183">Creare un' Razor app Web di pagine.</span><span class="sxs-lookup"><span data-stu-id="468fa-183">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="468fa-184">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="468fa-184">Run the app.</span></span>
> * <span data-ttu-id="468fa-185">Esaminare i file di progetto.</span><span class="sxs-lookup"><span data-stu-id="468fa-185">Examine the project files.</span></span>

<span data-ttu-id="468fa-186">Al termine di questa esercitazione, si disporrà di un' Razor app Web di pagine di lavoro che verrà compilata nelle esercitazioni successive.</span><span class="sxs-lookup"><span data-stu-id="468fa-186">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Pagina Home o di indice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="468fa-188">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="468fa-188">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="468fa-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="468fa-189">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="468fa-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="468fa-190">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="468fa-191">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="468fa-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="468fa-192">Creare un' Razor app Web di pagine</span><span class="sxs-lookup"><span data-stu-id="468fa-192">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="468fa-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="468fa-193">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="468fa-194">Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.</span><span class="sxs-lookup"><span data-stu-id="468fa-194">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="468fa-195">Creare una nuova applicazione Web ASP.NET Core e selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="468fa-195">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="468fa-197">Denominare il progetto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="468fa-197">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="468fa-198">È importante denominare il progetto *RazorPagesMovie* in modo che gli spazi dei nomi corrispondano nell'operazione copia/incolla del codice.</span><span class="sxs-lookup"><span data-stu-id="468fa-198">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="468fa-200">Selezionare\*\* ASP.NET Core 2.2\*\* nell'elenco a discesa **Applicazione Web** e quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="468fa-200">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="468fa-202">Viene creato il progetto iniziale seguente:</span><span class="sxs-lookup"><span data-stu-id="468fa-202">The following starter project is created:</span></span>

  ![Esplora soluzioni](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="468fa-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="468fa-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="468fa-205">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="468fa-205">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="468fa-206">Passare alla directory (`cd`) che conterrà il progetto.</span><span class="sxs-lookup"><span data-stu-id="468fa-206">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="468fa-207">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="468fa-207">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="468fa-208">Il `dotnet new` comando crea un nuovo Razor progetto di pagine nella cartella *RazorPagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="468fa-208">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="468fa-209">Il comando `code` apre la cartella *RazorPagesMovie* nell'istanza corrente di Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="468fa-209">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="468fa-210">Dopo che l'icona di OmniSharp Flame della barra di stato è verde, una finestra di dialogo richiede che le **risorse necessarie per la compilazione e il debug siano mancanti da' RazorPagesMovie '. Aggiungerli?**</span><span class="sxs-lookup"><span data-stu-id="468fa-210">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="468fa-211">Selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="468fa-211">Select **Yes**.</span></span>

  <span data-ttu-id="468fa-212">Una directory *.vscode* che contiene i file *launch.json* e *tasks.json* viene aggiunta alla directory radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="468fa-212">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="468fa-213">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="468fa-213">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="468fa-214">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="468fa-214">Select **File** > **New Solution**.</span></span>

![Nuova soluzione macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="468fa-216">In Visual Studio per Mac precedente alla versione 8,6 selezionare **.NET Core**  >  **App**  >  **applicazione Web**app .NET Core  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="468fa-216">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="468fa-217">Nella versione 8,6 o successive selezionare applicazione Web **e**  >  **app**  >  **Web dell'** app console  >  **successiva**.</span><span class="sxs-lookup"><span data-stu-id="468fa-217">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="468fa-218">Nella finestra di dialogo **configurare la nuova ASP.NET Core API Web** impostare il **Framework di destinazione** su **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="468fa-218">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![Selezione di .NET Core 3.0 per macOS](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="468fa-220">Denominare il progetto **RazorPagesMovie**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="468fa-220">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="468fa-222">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="468fa-222">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="468fa-223">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="468fa-223">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="468fa-224">Premere CTRL+F5 per l'esecuzione senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="468fa-224">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="468fa-225">Visual Studio avvia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ed esegue l'app.</span><span class="sxs-lookup"><span data-stu-id="468fa-225">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="468fa-226">La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="468fa-226">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="468fa-227">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="468fa-227">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="468fa-228">Localhost viene usato solo per le richieste web del computer locale.</span><span class="sxs-lookup"><span data-stu-id="468fa-228">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="468fa-229">Quando Visual Studio crea un progetto Web, per il server Web viene usata una porta casuale.</span><span class="sxs-lookup"><span data-stu-id="468fa-229">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="468fa-230">Nella home page dell'app selezionare **Accetto** per autorizzare il rilevamento.</span><span class="sxs-lookup"><span data-stu-id="468fa-230">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="468fa-231">Questa app non tiene traccia delle informazioni personali, ma il modello di progetto include la funzionalità di consenso nel caso in cui risulti necessaria la conformità al [Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="468fa-231">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Pagina Home o di indice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="468fa-233">La figura seguente visualizza l'app dopo che è stato impostato il consenso al rilevamento:</span><span class="sxs-lookup"><span data-stu-id="468fa-233">The following image shows the app after you give consent to tracking:</span></span>

  ![Pagina Home o di indice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="468fa-235">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="468fa-235">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="468fa-236">Premere **CTRL+F5** per l'esecuzione senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="468fa-236">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="468fa-237">Visual Studio Code avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="468fa-237">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="468fa-238">La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="468fa-238">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="468fa-239">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="468fa-239">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="468fa-240">Localhost viene usato solo per le richieste web del computer locale.</span><span class="sxs-lookup"><span data-stu-id="468fa-240">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="468fa-241">Nella home page dell'app selezionare **Accetto** per autorizzare il rilevamento.</span><span class="sxs-lookup"><span data-stu-id="468fa-241">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="468fa-242">Questa app non tiene traccia delle informazioni personali, ma il modello di progetto include la funzionalità di consenso nel caso in cui risulti necessaria la conformità al [Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="468fa-242">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Pagina Home o di indice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="468fa-244">La figura seguente visualizza l'app dopo che è stato impostato il consenso al rilevamento:</span><span class="sxs-lookup"><span data-stu-id="468fa-244">The following image shows the app after you give consent to tracking:</span></span>

  ![Pagina Home o di indice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="468fa-246">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="468fa-246">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="468fa-247">Premere **Cmd-Opt-F5** per l'esecuzione senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="468fa-247">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="468fa-248">Visual Studio avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="468fa-248">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="468fa-249">Nella home page dell'app selezionare **Accetto** per autorizzare il rilevamento.</span><span class="sxs-lookup"><span data-stu-id="468fa-249">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="468fa-250">Questa app non tiene traccia delle informazioni personali, ma il modello di progetto include la funzionalità di consenso nel caso in cui risulti necessaria la conformità al [Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="468fa-250">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Pagina Home o di indice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="468fa-252">La figura seguente visualizza l'app dopo che è stato impostato il consenso al rilevamento:</span><span class="sxs-lookup"><span data-stu-id="468fa-252">The following image shows the app after you give consent to tracking:</span></span>

  ![Pagina Home o di indice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="468fa-254">Esaminare i file di progetto</span><span class="sxs-lookup"><span data-stu-id="468fa-254">Examine the project files</span></span>

<span data-ttu-id="468fa-255">Di seguito viene visualizzata una panoramica delle cartelle e dei file principali del progetto, che verranno usati in esercitazioni successive.</span><span class="sxs-lookup"><span data-stu-id="468fa-255">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="468fa-256">Cartella Pages</span><span class="sxs-lookup"><span data-stu-id="468fa-256">Pages folder</span></span>

<span data-ttu-id="468fa-257">Contiene Razor pagine e file di supporto.</span><span class="sxs-lookup"><span data-stu-id="468fa-257">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="468fa-258">Ogni Razor pagina è una coppia di file:</span><span class="sxs-lookup"><span data-stu-id="468fa-258">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="468fa-259">Un file con *estensione cshtml* che contiene markup HTML con codice C# che utilizza la Razor sintassi.</span><span class="sxs-lookup"><span data-stu-id="468fa-259">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="468fa-260">Un file con estensione *cshtml.cs* contenente il codice C# per la gestione degli eventi della pagina.</span><span class="sxs-lookup"><span data-stu-id="468fa-260">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="468fa-261">I nomi dei file di supporto iniziano con un carattere di sottolineatura.</span><span class="sxs-lookup"><span data-stu-id="468fa-261">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="468fa-262">Ad esempio, il file *_Layout. cshtml* configura gli elementi dell'interfaccia utente comuni a tutte le pagine.</span><span class="sxs-lookup"><span data-stu-id="468fa-262">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="468fa-263">Questo file imposta il menu di navigazione nella parte superiore della pagina e le informazioni sul copyright in fondo alla pagina.</span><span class="sxs-lookup"><span data-stu-id="468fa-263">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="468fa-264">Per altre informazioni, vedere <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="468fa-264">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="468fa-265">Cartella wwwroot</span><span class="sxs-lookup"><span data-stu-id="468fa-265">wwwroot folder</span></span>

<span data-ttu-id="468fa-266">Contiene i file statici, ad esempio i file HTML, i file JavaScript e i file CSS.</span><span class="sxs-lookup"><span data-stu-id="468fa-266">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="468fa-267">Per altre informazioni, vedere <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="468fa-267">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="468fa-268">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="468fa-268">appSettings.json</span></span>

<span data-ttu-id="468fa-269">Contiene i dati di configurazione, ad esempio le stringhe di connessione.</span><span class="sxs-lookup"><span data-stu-id="468fa-269">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="468fa-270">Per altre informazioni, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="468fa-270">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="468fa-271">Program.cs</span><span class="sxs-lookup"><span data-stu-id="468fa-271">Program.cs</span></span>

<span data-ttu-id="468fa-272">Contiene il punto di ingresso per il programma.</span><span class="sxs-lookup"><span data-stu-id="468fa-272">Contains the entry point for the program.</span></span> <span data-ttu-id="468fa-273">Per altre informazioni, vedere <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="468fa-273">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="468fa-274">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="468fa-274">Startup.cs</span></span>

<span data-ttu-id="468fa-275">Contiene codice che configura il comportamento dell'app, ad esempio se richiede il consenso per i cookie.</span><span class="sxs-lookup"><span data-stu-id="468fa-275">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="468fa-276">Per altre informazioni, vedere <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="468fa-276">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="468fa-277">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="468fa-277">Additional resources</span></span>

* [<span data-ttu-id="468fa-278">Versione di YouTube di questa esercitazione</span><span class="sxs-lookup"><span data-stu-id="468fa-278">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="468fa-279">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="468fa-279">Next steps</span></span>

<span data-ttu-id="468fa-280">Passare all'esercitazione successiva della serie:</span><span class="sxs-lookup"><span data-stu-id="468fa-280">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="468fa-281">Aggiungere un modello</span><span class="sxs-lookup"><span data-stu-id="468fa-281">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
