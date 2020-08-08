---
title: 'Esercitazione: Introduzione alle Razor pagine in ASP.NET Core'
author: rick-anderson
description: Questa serie di esercitazioni illustra come usare le Razor pagine in ASP.NET Core. Informazioni su come creare un modello, generare codice per Razor le pagine, usare Entity Framework Core e SQL Server per l'accesso ai dati, aggiungere funzionalità di ricerca, aggiungere la convalida dell'input e usare le migrazioni per aggiornare il modello.
ms.author: riande
ms.date: 11/12/2019
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 5fb841de2fa9a04cf05aaf08f255041ee1952638
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021094"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="939b3-104">Esercitazione: Introduzione alle Razor pagine in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="939b3-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="939b3-105">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="939b3-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="939b3-106">Questa è la prima esercitazione di una serie che illustra le nozioni di base per la creazione di un' Razor app Web di ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="939b3-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="939b3-107">Al termine della serie si otterrà un'app che gestisce un database di film.</span><span class="sxs-lookup"><span data-stu-id="939b3-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="939b3-108">In questa esercitazione:</span><span class="sxs-lookup"><span data-stu-id="939b3-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="939b3-109">Creare un' Razor app Web di pagine.</span><span class="sxs-lookup"><span data-stu-id="939b3-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="939b3-110">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="939b3-110">Run the app.</span></span>
> * <span data-ttu-id="939b3-111">Esaminare i file di progetto.</span><span class="sxs-lookup"><span data-stu-id="939b3-111">Examine the project files.</span></span>

<span data-ttu-id="939b3-112">Al termine di questa esercitazione, si disporrà di un' Razor app Web di pagine di lavoro che verrà compilata nelle esercitazioni successive.</span><span class="sxs-lookup"><span data-stu-id="939b3-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Pagina Home o di indice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="939b3-114">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="939b3-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="939b3-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="939b3-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="939b3-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="939b3-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="939b3-117">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="939b3-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="939b3-118">Creare un' Razor app Web di pagine</span><span class="sxs-lookup"><span data-stu-id="939b3-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="939b3-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="939b3-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="939b3-120">Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.</span><span class="sxs-lookup"><span data-stu-id="939b3-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="939b3-121">Creare una nuova applicazione Web ASP.NET Core e selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="939b3-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="939b3-122">![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="939b3-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="939b3-123">Denominare il progetto \*\* Razor PagesMovie\*\*.</span><span class="sxs-lookup"><span data-stu-id="939b3-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="939b3-124">È importante denominare il progetto \* Razor PagesMovie\* in modo che gli spazi dei nomi corrispondano quando si copia e incolla il codice.</span><span class="sxs-lookup"><span data-stu-id="939b3-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="939b3-125">![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="939b3-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="939b3-126">Selezionare **ASP.NET Core 3,1** nell'elenco a discesa, **applicazione Web**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="939b3-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="939b3-128">Viene creato il progetto iniziale seguente:</span><span class="sxs-lookup"><span data-stu-id="939b3-128">The following starter project is created:</span></span>

  ![Esplora soluzioni](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="939b3-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="939b3-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="939b3-131">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="939b3-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="939b3-132">Passare alla directory (`cd`) che conterrà il progetto.</span><span class="sxs-lookup"><span data-stu-id="939b3-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="939b3-133">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="939b3-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="939b3-134">Il `dotnet new` comando crea un nuovo Razor progetto di pagine nella cartella \* Razor PagesMovie\* .</span><span class="sxs-lookup"><span data-stu-id="939b3-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="939b3-135">Il `code` comando apre la cartella \* Razor PagesMovie\* nell'istanza corrente di Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="939b3-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="939b3-136">Dopo che l'icona di OmniSharp Flame della barra di stato è verde, una finestra di dialogo richiede che le **risorse necessarie per la compilazione e il debug siano mancanti da' Razor PagesMovie '. Aggiungerli?**</span><span class="sxs-lookup"><span data-stu-id="939b3-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="939b3-137">Selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="939b3-137">Select **Yes**.</span></span>

  <span data-ttu-id="939b3-138">Una directory *.vscode* che contiene i file *launch.json* e *tasks.json* viene aggiunta alla directory radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="939b3-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="939b3-139">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="939b3-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="939b3-140">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="939b3-140">Select **File** > **New Solution**.</span></span>

  ![Nuova soluzione macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="939b3-142">In Visual Studio per Mac precedente alla versione 8,6 selezionare **.NET Core**  >  **App**  >  **applicazione Web**app .NET Core  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="939b3-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="939b3-143">Nella versione 8,6 o successive selezionare applicazione Web **e**  >  **app**  >  **Web dell'** app console  >  **successiva**.</span><span class="sxs-lookup"><span data-stu-id="939b3-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![selezione del modello di app Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="939b3-145">Nella finestra di dialogo **Configura nuova applicazione Web** :</span><span class="sxs-lookup"><span data-stu-id="939b3-145">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="939b3-146">Verificare che **l'autenticazione** sia impostata su **Nessuna autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="939b3-146">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="939b3-147">Se viene visualizzata un'opzione per la selezione di un **Framework di destinazione**, selezionare la versione 3. x più recente.</span><span class="sxs-lookup"><span data-stu-id="939b3-147">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="939b3-148">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="939b3-148">Select **Next**.</span></span>

* <span data-ttu-id="939b3-149">Assegnare al progetto il nome \*\* Razor PagesMovie\*\*e quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="939b3-149">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![macOS nome del progetto](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="939b3-151">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="939b3-151">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="939b3-152">Esaminare i file di progetto</span><span class="sxs-lookup"><span data-stu-id="939b3-152">Examine the project files</span></span>

<span data-ttu-id="939b3-153">Di seguito viene visualizzata una panoramica delle cartelle e dei file principali del progetto, che verranno usati in esercitazioni successive.</span><span class="sxs-lookup"><span data-stu-id="939b3-153">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="939b3-154">Cartella Pages</span><span class="sxs-lookup"><span data-stu-id="939b3-154">Pages folder</span></span>

<span data-ttu-id="939b3-155">Contiene Razor pagine e file di supporto.</span><span class="sxs-lookup"><span data-stu-id="939b3-155">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="939b3-156">Ogni Razor pagina è una coppia di file:</span><span class="sxs-lookup"><span data-stu-id="939b3-156">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="939b3-157">Un file con *estensione cshtml* che contiene markup HTML con codice C# che utilizza la Razor sintassi.</span><span class="sxs-lookup"><span data-stu-id="939b3-157">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="939b3-158">Un file con estensione *cshtml.cs* contenente il codice C# per la gestione degli eventi della pagina.</span><span class="sxs-lookup"><span data-stu-id="939b3-158">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="939b3-159">I nomi dei file di supporto iniziano con un carattere di sottolineatura.</span><span class="sxs-lookup"><span data-stu-id="939b3-159">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="939b3-160">Ad esempio, il file *_Layout. cshtml* configura gli elementi dell'interfaccia utente comuni a tutte le pagine.</span><span class="sxs-lookup"><span data-stu-id="939b3-160">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="939b3-161">Questo file imposta il menu di navigazione nella parte superiore della pagina e le informazioni sul copyright in fondo alla pagina.</span><span class="sxs-lookup"><span data-stu-id="939b3-161">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="939b3-162">Per altre informazioni, vedere <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="939b3-162">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="939b3-163">Cartella wwwroot</span><span class="sxs-lookup"><span data-stu-id="939b3-163">wwwroot folder</span></span>

<span data-ttu-id="939b3-164">Contiene i file statici, ad esempio i file HTML, i file JavaScript e i file CSS.</span><span class="sxs-lookup"><span data-stu-id="939b3-164">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="939b3-165">Per altre informazioni, vedere <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="939b3-165">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="939b3-166">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="939b3-166">appSettings.json</span></span>

<span data-ttu-id="939b3-167">Contiene i dati di configurazione, ad esempio le stringhe di connessione.</span><span class="sxs-lookup"><span data-stu-id="939b3-167">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="939b3-168">Per altre informazioni, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="939b3-168">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="939b3-169">Program.cs</span><span class="sxs-lookup"><span data-stu-id="939b3-169">Program.cs</span></span>

<span data-ttu-id="939b3-170">Contiene il punto di ingresso per il programma.</span><span class="sxs-lookup"><span data-stu-id="939b3-170">Contains the entry point for the program.</span></span> <span data-ttu-id="939b3-171">Per altre informazioni, vedere <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="939b3-171">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="939b3-172">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="939b3-172">Startup.cs</span></span>

<span data-ttu-id="939b3-173">Contiene codice che consente di configurare il comportamento dell'app.</span><span class="sxs-lookup"><span data-stu-id="939b3-173">Contains code that configures app behavior.</span></span> <span data-ttu-id="939b3-174">Per altre informazioni, vedere <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="939b3-174">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="939b3-175">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="939b3-175">Next steps</span></span>

<span data-ttu-id="939b3-176">Passare all'esercitazione successiva della serie:</span><span class="sxs-lookup"><span data-stu-id="939b3-176">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="939b3-177">Aggiunta di un modello</span><span class="sxs-lookup"><span data-stu-id="939b3-177">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="939b3-178">Questa è la prima esercitazione di una serie.</span><span class="sxs-lookup"><span data-stu-id="939b3-178">This is the first tutorial of a series.</span></span> <span data-ttu-id="939b3-179">[La serie](xref:tutorials/razor-pages/index) illustra le nozioni di base per la creazione di un' Razor app Web di ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="939b3-179">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="939b3-180">Al termine della serie si otterrà un'app che gestisce un database di film.</span><span class="sxs-lookup"><span data-stu-id="939b3-180">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="939b3-181">In questa esercitazione:</span><span class="sxs-lookup"><span data-stu-id="939b3-181">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="939b3-182">Creare un' Razor app Web di pagine.</span><span class="sxs-lookup"><span data-stu-id="939b3-182">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="939b3-183">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="939b3-183">Run the app.</span></span>
> * <span data-ttu-id="939b3-184">Esaminare i file di progetto.</span><span class="sxs-lookup"><span data-stu-id="939b3-184">Examine the project files.</span></span>

<span data-ttu-id="939b3-185">Al termine di questa esercitazione, si disporrà di un' Razor app Web di pagine di lavoro che verrà compilata nelle esercitazioni successive.</span><span class="sxs-lookup"><span data-stu-id="939b3-185">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Pagina Home o di indice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="939b3-187">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="939b3-187">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="939b3-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="939b3-188">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="939b3-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="939b3-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="939b3-190">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="939b3-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="939b3-191">Creare un' Razor app Web di pagine</span><span class="sxs-lookup"><span data-stu-id="939b3-191">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="939b3-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="939b3-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="939b3-193">Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.</span><span class="sxs-lookup"><span data-stu-id="939b3-193">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="939b3-194">Creare una nuova applicazione Web ASP.NET Core e selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="939b3-194">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="939b3-196">Denominare il progetto \*\* Razor PagesMovie\*\*.</span><span class="sxs-lookup"><span data-stu-id="939b3-196">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="939b3-197">È importante denominare il progetto \* Razor PagesMovie\* in modo che gli spazi dei nomi corrispondano quando si copia e incolla il codice.</span><span class="sxs-lookup"><span data-stu-id="939b3-197">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="939b3-199">Selezionare\*\* ASP.NET Core 2.2\*\* nell'elenco a discesa **Applicazione Web** e quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="939b3-199">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="939b3-201">Viene creato il progetto iniziale seguente:</span><span class="sxs-lookup"><span data-stu-id="939b3-201">The following starter project is created:</span></span>

  ![Esplora soluzioni](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="939b3-203">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="939b3-203">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="939b3-204">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="939b3-204">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="939b3-205">Passare alla directory (`cd`) che conterrà il progetto.</span><span class="sxs-lookup"><span data-stu-id="939b3-205">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="939b3-206">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="939b3-206">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="939b3-207">Il `dotnet new` comando crea un nuovo Razor progetto di pagine nella cartella \* Razor PagesMovie\* .</span><span class="sxs-lookup"><span data-stu-id="939b3-207">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="939b3-208">Il `code` comando apre la cartella \* Razor PagesMovie\* nell'istanza corrente di Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="939b3-208">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="939b3-209">Dopo che l'icona di OmniSharp Flame della barra di stato è verde, una finestra di dialogo richiede che le **risorse necessarie per la compilazione e il debug siano mancanti da' Razor PagesMovie '. Aggiungerli?**</span><span class="sxs-lookup"><span data-stu-id="939b3-209">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="939b3-210">Selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="939b3-210">Select **Yes**.</span></span>

  <span data-ttu-id="939b3-211">Una directory *.vscode* che contiene i file *launch.json* e *tasks.json* viene aggiunta alla directory radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="939b3-211">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="939b3-212">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="939b3-212">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="939b3-213">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="939b3-213">Select **File** > **New Solution**.</span></span>

![Nuova soluzione macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="939b3-215">In Visual Studio per Mac precedente alla versione 8,6 selezionare **.NET Core**  >  **App**  >  **applicazione Web**app .NET Core  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="939b3-215">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="939b3-216">Nella versione 8,6 o successive selezionare applicazione Web **e**  >  **app**  >  **Web dell'** app console  >  **successiva**.</span><span class="sxs-lookup"><span data-stu-id="939b3-216">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="939b3-217">Nella finestra di dialogo **Configura nuova applicazione Web** :</span><span class="sxs-lookup"><span data-stu-id="939b3-217">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="939b3-218">Verificare che **l'autenticazione** sia impostata su **Nessuna autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="939b3-218">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="939b3-219">Se viene visualizzata un'opzione per la selezione di un **Framework di destinazione**, selezionare la versione 2. x più recente.</span><span class="sxs-lookup"><span data-stu-id="939b3-219">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="939b3-220">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="939b3-220">Select **Next**.</span></span>

* <span data-ttu-id="939b3-221">Assegnare al progetto il nome \*\* Razor PagesMovie\*\*e quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="939b3-221">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="939b3-223">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="939b3-223">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="939b3-224">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="939b3-224">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="939b3-225">Premere CTRL+F5 per l'esecuzione senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="939b3-225">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="939b3-226">Visual Studio avvia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ed esegue l'app.</span><span class="sxs-lookup"><span data-stu-id="939b3-226">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="939b3-227">La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="939b3-227">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="939b3-228">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="939b3-228">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="939b3-229">Localhost viene usato solo per le richieste web del computer locale.</span><span class="sxs-lookup"><span data-stu-id="939b3-229">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="939b3-230">Quando Visual Studio crea un progetto Web, viene usata una porta casuale per il server Web.</span><span class="sxs-lookup"><span data-stu-id="939b3-230">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="939b3-231">Nella home page dell'app selezionare **Accetto** per autorizzare il rilevamento.</span><span class="sxs-lookup"><span data-stu-id="939b3-231">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="939b3-232">Questa app non tiene traccia delle informazioni personali, ma il modello di progetto include la funzionalità di consenso nel caso in cui risulti necessaria la conformità al [Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="939b3-232">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Pagina Home o di indice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="939b3-234">La figura seguente visualizza l'app dopo che è stato impostato il consenso al rilevamento:</span><span class="sxs-lookup"><span data-stu-id="939b3-234">The following image shows the app after you give consent to tracking:</span></span>

  ![Pagina Home o di indice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="939b3-236">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="939b3-236">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="939b3-237">Premere **CTRL+F5** per l'esecuzione senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="939b3-237">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="939b3-238">Visual Studio Code avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="939b3-238">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="939b3-239">La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="939b3-239">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="939b3-240">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="939b3-240">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="939b3-241">Localhost viene usato solo per le richieste web del computer locale.</span><span class="sxs-lookup"><span data-stu-id="939b3-241">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="939b3-242">Nella home page dell'app selezionare **Accetto** per autorizzare il rilevamento.</span><span class="sxs-lookup"><span data-stu-id="939b3-242">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="939b3-243">Questa app non tiene traccia delle informazioni personali, ma il modello di progetto include la funzionalità di consenso nel caso in cui risulti necessaria la conformità al [Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="939b3-243">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Pagina Home o di indice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="939b3-245">La figura seguente visualizza l'app dopo che è stato impostato il consenso al rilevamento:</span><span class="sxs-lookup"><span data-stu-id="939b3-245">The following image shows the app after you give consent to tracking:</span></span>

  ![Pagina Home o di indice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="939b3-247">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="939b3-247">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="939b3-248">Premere **Cmd-Opt-F5** per l'esecuzione senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="939b3-248">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="939b3-249">Visual Studio avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="939b3-249">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="939b3-250">Nella home page dell'app selezionare **Accetto** per autorizzare il rilevamento.</span><span class="sxs-lookup"><span data-stu-id="939b3-250">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="939b3-251">Questa app non tiene traccia delle informazioni personali, ma il modello di progetto include la funzionalità di consenso nel caso in cui risulti necessaria la conformità al [Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="939b3-251">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Pagina Home o di indice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="939b3-253">La figura seguente visualizza l'app dopo che è stato impostato il consenso al rilevamento:</span><span class="sxs-lookup"><span data-stu-id="939b3-253">The following image shows the app after you give consent to tracking:</span></span>

  ![Pagina Home o di indice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="939b3-255">Esaminare i file di progetto</span><span class="sxs-lookup"><span data-stu-id="939b3-255">Examine the project files</span></span>

<span data-ttu-id="939b3-256">Di seguito viene visualizzata una panoramica delle cartelle e dei file principali del progetto, che verranno usati in esercitazioni successive.</span><span class="sxs-lookup"><span data-stu-id="939b3-256">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="939b3-257">Cartella Pages</span><span class="sxs-lookup"><span data-stu-id="939b3-257">Pages folder</span></span>

<span data-ttu-id="939b3-258">Contiene Razor pagine e file di supporto.</span><span class="sxs-lookup"><span data-stu-id="939b3-258">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="939b3-259">Ogni Razor pagina è una coppia di file:</span><span class="sxs-lookup"><span data-stu-id="939b3-259">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="939b3-260">Un file con *estensione cshtml* che contiene markup HTML con codice C# che utilizza la Razor sintassi.</span><span class="sxs-lookup"><span data-stu-id="939b3-260">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="939b3-261">Un file con estensione *cshtml.cs* contenente il codice C# per la gestione degli eventi della pagina.</span><span class="sxs-lookup"><span data-stu-id="939b3-261">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="939b3-262">I nomi dei file di supporto iniziano con un carattere di sottolineatura.</span><span class="sxs-lookup"><span data-stu-id="939b3-262">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="939b3-263">Ad esempio, il file *_Layout. cshtml* configura gli elementi dell'interfaccia utente comuni a tutte le pagine.</span><span class="sxs-lookup"><span data-stu-id="939b3-263">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="939b3-264">Questo file imposta il menu di navigazione nella parte superiore della pagina e le informazioni sul copyright in fondo alla pagina.</span><span class="sxs-lookup"><span data-stu-id="939b3-264">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="939b3-265">Per altre informazioni, vedere <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="939b3-265">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="939b3-266">Cartella wwwroot</span><span class="sxs-lookup"><span data-stu-id="939b3-266">wwwroot folder</span></span>

<span data-ttu-id="939b3-267">Contiene i file statici, ad esempio i file HTML, i file JavaScript e i file CSS.</span><span class="sxs-lookup"><span data-stu-id="939b3-267">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="939b3-268">Per altre informazioni, vedere <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="939b3-268">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="939b3-269">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="939b3-269">appSettings.json</span></span>

<span data-ttu-id="939b3-270">Contiene i dati di configurazione, ad esempio le stringhe di connessione.</span><span class="sxs-lookup"><span data-stu-id="939b3-270">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="939b3-271">Per altre informazioni, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="939b3-271">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="939b3-272">Program.cs</span><span class="sxs-lookup"><span data-stu-id="939b3-272">Program.cs</span></span>

<span data-ttu-id="939b3-273">Contiene il punto di ingresso per il programma.</span><span class="sxs-lookup"><span data-stu-id="939b3-273">Contains the entry point for the program.</span></span> <span data-ttu-id="939b3-274">Per altre informazioni, vedere <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="939b3-274">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="939b3-275">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="939b3-275">Startup.cs</span></span>

<span data-ttu-id="939b3-276">Contiene il codice che configura il comportamento dell'app, ad esempio se richiede il consenso per cookie s.</span><span class="sxs-lookup"><span data-stu-id="939b3-276">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="939b3-277">Per altre informazioni, vedere <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="939b3-277">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="939b3-278">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="939b3-278">Additional resources</span></span>

* [<span data-ttu-id="939b3-279">Versione di YouTube di questa esercitazione</span><span class="sxs-lookup"><span data-stu-id="939b3-279">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="939b3-280">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="939b3-280">Next steps</span></span>

<span data-ttu-id="939b3-281">Passare all'esercitazione successiva della serie:</span><span class="sxs-lookup"><span data-stu-id="939b3-281">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="939b3-282">Aggiunta di un modello</span><span class="sxs-lookup"><span data-stu-id="939b3-282">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
