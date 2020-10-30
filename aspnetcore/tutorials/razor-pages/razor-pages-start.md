---
title: 'Esercitazione: Introduzione alle :::no-loc(Razor)::: pagine in ASP.NET Core'
author: rick-anderson
description: "Questa serie di esercitazioni illustra come usare le :::no-loc(Razor)::: pagine in ASP.NET Core. Informazioni su come creare un modello, generare codice per :::no-loc(Razor)::: le pagine, usare Entity Framework Core e SQL Server per l'accesso ai dati, aggiungere funzionalità di ricerca, aggiungere la convalida dell'input e usare le migrazioni per aggiornare il modello."
ms.author: riande
ms.date: 11/12/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: ab890b956b1242f183054b7ab4575a59072b4f50
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060235"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="6d479-104">Esercitazione: Introduzione alle :::no-loc(Razor)::: pagine in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6d479-104">Tutorial: Get started with :::no-loc(Razor)::: Pages in ASP.NET Core</span></span>

<span data-ttu-id="6d479-105">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6d479-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="6d479-106">Questa è la prima esercitazione di una serie che illustra le nozioni di base per la creazione di un' :::no-loc(Razor)::: app Web di ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="6d479-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core :::no-loc(Razor)::: Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="6d479-107">Al termine della serie si otterrà un'app che gestisce un database di film.</span><span class="sxs-lookup"><span data-stu-id="6d479-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="6d479-108">In questa esercitazione:</span><span class="sxs-lookup"><span data-stu-id="6d479-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6d479-109">Creare un' :::no-loc(Razor)::: app Web di pagine.</span><span class="sxs-lookup"><span data-stu-id="6d479-109">Create a :::no-loc(Razor)::: Pages web app.</span></span>
> * <span data-ttu-id="6d479-110">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="6d479-110">Run the app.</span></span>
> * <span data-ttu-id="6d479-111">Esaminare i file di progetto.</span><span class="sxs-lookup"><span data-stu-id="6d479-111">Examine the project files.</span></span>

<span data-ttu-id="6d479-112">Al termine di questa esercitazione, si disporrà di un' :::no-loc(Razor)::: app Web di pagine di lavoro che verrà compilata nelle esercitazioni successive.</span><span class="sxs-lookup"><span data-stu-id="6d479-112">At the end of this tutorial, you'll have a working :::no-loc(Razor)::: Pages web app that you'll build on in later tutorials.</span></span>

![Pagina Home o di indice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="6d479-114">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="6d479-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6d479-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6d479-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6d479-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6d479-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6d479-117">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6d479-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="6d479-118">Creare un' :::no-loc(Razor)::: app Web di pagine</span><span class="sxs-lookup"><span data-stu-id="6d479-118">Create a :::no-loc(Razor)::: Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6d479-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6d479-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6d479-120">Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto** .</span><span class="sxs-lookup"><span data-stu-id="6d479-120">From the Visual Studio **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="6d479-121">Creare una nuova applicazione Web ASP.NET Core e selezionare **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="6d479-121">Create a new ASP.NET Core Web Application and select **Next** .</span></span>
  <span data-ttu-id="6d479-122">![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="6d479-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="6d479-123">Denominare il progetto **:::no-loc(Razor)::: PagesMovie** .</span><span class="sxs-lookup"><span data-stu-id="6d479-123">Name the project **:::no-loc(Razor):::PagesMovie** .</span></span> <span data-ttu-id="6d479-124">È importante denominare il progetto *:::no-loc(Razor)::: PagesMovie* in modo che gli spazi dei nomi corrispondano quando si copia e incolla il codice.</span><span class="sxs-lookup"><span data-stu-id="6d479-124">It's important to name the project *:::no-loc(Razor):::PagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="6d479-125">![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="6d479-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="6d479-126">Selezionare **ASP.NET Core 3,1** nell'elenco a discesa, **applicazione Web** , quindi selezionare **Crea** .</span><span class="sxs-lookup"><span data-stu-id="6d479-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application** , and then select **Create** .</span></span>

![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="6d479-128">Viene creato il progetto iniziale seguente:</span><span class="sxs-lookup"><span data-stu-id="6d479-128">The following starter project is created:</span></span>

  ![Esplora soluzioni](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="6d479-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6d479-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6d479-131">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="6d479-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="6d479-132">Passare alla directory (`cd`) che conterrà il progetto.</span><span class="sxs-lookup"><span data-stu-id="6d479-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="6d479-133">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="6d479-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o :::no-loc(Razor):::PagesMovie
  code -r :::no-loc(Razor):::PagesMovie
  ```

  * <span data-ttu-id="6d479-134">Il `dotnet new` comando crea un nuovo :::no-loc(Razor)::: progetto di pagine nella cartella *:::no-loc(Razor)::: PagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="6d479-134">The `dotnet new` command creates a new :::no-loc(Razor)::: Pages project in the *:::no-loc(Razor):::PagesMovie* folder.</span></span>
  * <span data-ttu-id="6d479-135">Il `code` comando apre la cartella *:::no-loc(Razor)::: PagesMovie* nell'istanza corrente di Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="6d479-135">The `code` command opens the *:::no-loc(Razor):::PagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="6d479-136">Dopo che l'icona di OmniSharp Flame della barra di stato è verde, una finestra di dialogo richiede che le **risorse necessarie per la compilazione e il debug siano mancanti da' :::no-loc(Razor)::: PagesMovie '. Aggiungerli?**</span><span class="sxs-lookup"><span data-stu-id="6d479-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from ':::no-loc(Razor):::PagesMovie'. Add them?**</span></span> <span data-ttu-id="6d479-137">Selezionare **Sì** .</span><span class="sxs-lookup"><span data-stu-id="6d479-137">Select **Yes** .</span></span>

  <span data-ttu-id="6d479-138">Una directory *.vscode* che contiene i file *launch.json* e *tasks.json* viene aggiunta alla directory radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="6d479-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6d479-139">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6d479-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6d479-140">Selezionare **File** > **Nuova soluzione** .</span><span class="sxs-lookup"><span data-stu-id="6d479-140">Select **File** > **New Solution** .</span></span>

  ![Nuova soluzione macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="6d479-142">In Visual Studio per Mac precedente alla versione 8,6 selezionare **.NET Core**  >  **App**  >  **applicazione Web** app .NET Core  >  **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="6d479-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next** .</span></span> <span data-ttu-id="6d479-143">Nella versione 8,6 o successive selezionare applicazione Web **e**  >  **app**  >  **Web dell'** app console  >  **successiva** .</span><span class="sxs-lookup"><span data-stu-id="6d479-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next** .</span></span>

  ![selezione del modello di app Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="6d479-145">Nella finestra di dialogo **Configura nuova applicazione Web** :</span><span class="sxs-lookup"><span data-stu-id="6d479-145">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="6d479-146">Verificare che **l'autenticazione** sia impostata su **Nessuna autenticazione** .</span><span class="sxs-lookup"><span data-stu-id="6d479-146">Confirm that **Authentication** is set to **No Authentication** .</span></span>
  * <span data-ttu-id="6d479-147">Se viene visualizzata un'opzione per la selezione di un **Framework di destinazione** , selezionare la versione 3. x più recente.</span><span class="sxs-lookup"><span data-stu-id="6d479-147">If presented an option to select a **Target Framework** , select the latest 3.x version.</span></span>

  <span data-ttu-id="6d479-148">Selezionare **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="6d479-148">Select **Next** .</span></span>

* <span data-ttu-id="6d479-149">Assegnare al progetto il nome **:::no-loc(Razor)::: PagesMovie** e quindi selezionare **Crea** .</span><span class="sxs-lookup"><span data-stu-id="6d479-149">Name the project **:::no-loc(Razor):::PagesMovie** , and then select **Create** .</span></span>

  ![macOS nome del progetto](razor-pages-start/_static/:::no-loc(Razor):::PagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="6d479-151">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="6d479-151">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="6d479-152">Esaminare i file di progetto</span><span class="sxs-lookup"><span data-stu-id="6d479-152">Examine the project files</span></span>

<span data-ttu-id="6d479-153">Di seguito viene visualizzata una panoramica delle cartelle e dei file principali del progetto, che verranno usati in esercitazioni successive.</span><span class="sxs-lookup"><span data-stu-id="6d479-153">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="6d479-154">Cartella Pages</span><span class="sxs-lookup"><span data-stu-id="6d479-154">Pages folder</span></span>

<span data-ttu-id="6d479-155">Contiene :::no-loc(Razor)::: pagine e file di supporto.</span><span class="sxs-lookup"><span data-stu-id="6d479-155">Contains :::no-loc(Razor)::: pages and supporting files.</span></span> <span data-ttu-id="6d479-156">Ogni :::no-loc(Razor)::: pagina è una coppia di file:</span><span class="sxs-lookup"><span data-stu-id="6d479-156">Each :::no-loc(Razor)::: page is a pair of files:</span></span>

* <span data-ttu-id="6d479-157">Un file con *estensione cshtml* che contiene markup HTML con codice C# che utilizza la :::no-loc(Razor)::: sintassi.</span><span class="sxs-lookup"><span data-stu-id="6d479-157">A *.cshtml* file that contains HTML markup with C# code using :::no-loc(Razor)::: syntax.</span></span>
* <span data-ttu-id="6d479-158">Un file con estensione *cshtml.cs* contenente il codice C# per la gestione degli eventi della pagina.</span><span class="sxs-lookup"><span data-stu-id="6d479-158">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="6d479-159">I nomi dei file di supporto iniziano con un carattere di sottolineatura.</span><span class="sxs-lookup"><span data-stu-id="6d479-159">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="6d479-160">Ad esempio, il file *_Layout. cshtml* configura gli elementi dell'interfaccia utente comuni a tutte le pagine.</span><span class="sxs-lookup"><span data-stu-id="6d479-160">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="6d479-161">Questo file imposta il menu di navigazione nella parte superiore della pagina e le informazioni sul copyright in fondo alla pagina.</span><span class="sxs-lookup"><span data-stu-id="6d479-161">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="6d479-162">Per altre informazioni, vedere <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="6d479-162">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="6d479-163">Cartella wwwroot</span><span class="sxs-lookup"><span data-stu-id="6d479-163">wwwroot folder</span></span>

<span data-ttu-id="6d479-164">Contiene i file statici, ad esempio i file HTML, i file JavaScript e i file CSS.</span><span class="sxs-lookup"><span data-stu-id="6d479-164">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="6d479-165">Per altre informazioni, vedere <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="6d479-165">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="6d479-166">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="6d479-166">appSettings.json</span></span>

<span data-ttu-id="6d479-167">Contiene i dati di configurazione, ad esempio le stringhe di connessione.</span><span class="sxs-lookup"><span data-stu-id="6d479-167">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="6d479-168">Per altre informazioni, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="6d479-168">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="6d479-169">Program.cs</span><span class="sxs-lookup"><span data-stu-id="6d479-169">Program.cs</span></span>

<span data-ttu-id="6d479-170">Contiene il punto di ingresso per il programma.</span><span class="sxs-lookup"><span data-stu-id="6d479-170">Contains the entry point for the program.</span></span> <span data-ttu-id="6d479-171">Per altre informazioni, vedere <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="6d479-171">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="6d479-172">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="6d479-172">Startup.cs</span></span>

<span data-ttu-id="6d479-173">Contiene codice che consente di configurare il comportamento dell'app.</span><span class="sxs-lookup"><span data-stu-id="6d479-173">Contains code that configures app behavior.</span></span> <span data-ttu-id="6d479-174">Per altre informazioni, vedere <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="6d479-174">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6d479-175">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="6d479-175">Next steps</span></span>

<span data-ttu-id="6d479-176">Passare all'esercitazione successiva della serie:</span><span class="sxs-lookup"><span data-stu-id="6d479-176">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="6d479-177">Aggiunta di un modello</span><span class="sxs-lookup"><span data-stu-id="6d479-177">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6d479-178">Questa è la prima esercitazione di una serie.</span><span class="sxs-lookup"><span data-stu-id="6d479-178">This is the first tutorial of a series.</span></span> <span data-ttu-id="6d479-179">[La serie](xref:tutorials/razor-pages/index) illustra le nozioni di base per la creazione di un' :::no-loc(Razor)::: app Web di ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="6d479-179">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core :::no-loc(Razor)::: Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="6d479-180">Al termine della serie si otterrà un'app che gestisce un database di film.</span><span class="sxs-lookup"><span data-stu-id="6d479-180">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="6d479-181">In questa esercitazione:</span><span class="sxs-lookup"><span data-stu-id="6d479-181">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6d479-182">Creare un' :::no-loc(Razor)::: app Web di pagine.</span><span class="sxs-lookup"><span data-stu-id="6d479-182">Create a :::no-loc(Razor)::: Pages web app.</span></span>
> * <span data-ttu-id="6d479-183">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="6d479-183">Run the app.</span></span>
> * <span data-ttu-id="6d479-184">Esaminare i file di progetto.</span><span class="sxs-lookup"><span data-stu-id="6d479-184">Examine the project files.</span></span>

<span data-ttu-id="6d479-185">Al termine di questa esercitazione, si disporrà di un' :::no-loc(Razor)::: app Web di pagine di lavoro che verrà compilata nelle esercitazioni successive.</span><span class="sxs-lookup"><span data-stu-id="6d479-185">At the end of this tutorial, you'll have a working :::no-loc(Razor)::: Pages web app that you'll build on in later tutorials.</span></span>

![Pagina Home o di indice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="6d479-187">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="6d479-187">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6d479-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6d479-188">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6d479-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6d479-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6d479-190">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6d479-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="6d479-191">Creare un' :::no-loc(Razor)::: app Web di pagine</span><span class="sxs-lookup"><span data-stu-id="6d479-191">Create a :::no-loc(Razor)::: Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6d479-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6d479-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6d479-193">Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto** .</span><span class="sxs-lookup"><span data-stu-id="6d479-193">From the Visual Studio **File** menu, select **New** > **Project** .</span></span>

* <span data-ttu-id="6d479-194">Creare una nuova applicazione Web ASP.NET Core e selezionare **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="6d479-194">Create a new ASP.NET Core Web Application and select **Next** .</span></span>

  ![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="6d479-196">Denominare il progetto **:::no-loc(Razor)::: PagesMovie** .</span><span class="sxs-lookup"><span data-stu-id="6d479-196">Name the project **:::no-loc(Razor):::PagesMovie** .</span></span> <span data-ttu-id="6d479-197">È importante denominare il progetto *:::no-loc(Razor)::: PagesMovie* in modo che gli spazi dei nomi corrispondano quando si copia e incolla il codice.</span><span class="sxs-lookup"><span data-stu-id="6d479-197">It's important to name the project *:::no-loc(Razor):::PagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="6d479-199">Selezionare **ASP.NET Core 2.2** nell'elenco a discesa **Applicazione Web** e quindi selezionare **Crea** .</span><span class="sxs-lookup"><span data-stu-id="6d479-199">Select **ASP.NET Core 2.2** in the dropdown, **Web Application** , and then select **Create** .</span></span>

![nuova applicazione Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="6d479-201">Viene creato il progetto iniziale seguente:</span><span class="sxs-lookup"><span data-stu-id="6d479-201">The following starter project is created:</span></span>

  ![Esplora soluzioni](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="6d479-203">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6d479-203">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6d479-204">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="6d479-204">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="6d479-205">Passare alla directory (`cd`) che conterrà il progetto.</span><span class="sxs-lookup"><span data-stu-id="6d479-205">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="6d479-206">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="6d479-206">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o :::no-loc(Razor):::PagesMovie
  code -r :::no-loc(Razor):::PagesMovie
  ```

  * <span data-ttu-id="6d479-207">Il `dotnet new` comando crea un nuovo :::no-loc(Razor)::: progetto di pagine nella cartella *:::no-loc(Razor)::: PagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="6d479-207">The `dotnet new` command creates a new :::no-loc(Razor)::: Pages project in the *:::no-loc(Razor):::PagesMovie* folder.</span></span>
  * <span data-ttu-id="6d479-208">Il `code` comando apre la cartella *:::no-loc(Razor)::: PagesMovie* nell'istanza corrente di Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="6d479-208">The `code` command opens the *:::no-loc(Razor):::PagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="6d479-209">Dopo che l'icona di OmniSharp Flame della barra di stato è verde, una finestra di dialogo richiede che le **risorse necessarie per la compilazione e il debug siano mancanti da' :::no-loc(Razor)::: PagesMovie '. Aggiungerli?**</span><span class="sxs-lookup"><span data-stu-id="6d479-209">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from ':::no-loc(Razor):::PagesMovie'. Add them?**</span></span> <span data-ttu-id="6d479-210">Selezionare **Sì** .</span><span class="sxs-lookup"><span data-stu-id="6d479-210">Select **Yes** .</span></span>

  <span data-ttu-id="6d479-211">Una directory *.vscode* che contiene i file *launch.json* e *tasks.json* viene aggiunta alla directory radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="6d479-211">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6d479-212">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6d479-212">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6d479-213">Selezionare **File** > **Nuova soluzione** .</span><span class="sxs-lookup"><span data-stu-id="6d479-213">Select **File** > **New Solution** .</span></span>

![Nuova soluzione macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="6d479-215">In Visual Studio per Mac precedente alla versione 8,6 selezionare **.NET Core**  >  **App**  >  **applicazione Web** app .NET Core  >  **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="6d479-215">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next** .</span></span> <span data-ttu-id="6d479-216">Nella versione 8,6 o successive selezionare applicazione Web **e**  >  **app**  >  **Web dell'** app console  >  **successiva** .</span><span class="sxs-lookup"><span data-stu-id="6d479-216">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next** .</span></span>

* <span data-ttu-id="6d479-217">Nella finestra di dialogo **Configura nuova applicazione Web** :</span><span class="sxs-lookup"><span data-stu-id="6d479-217">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="6d479-218">Verificare che **l'autenticazione** sia impostata su **Nessuna autenticazione** .</span><span class="sxs-lookup"><span data-stu-id="6d479-218">Confirm that **Authentication** is set to **No Authentication** .</span></span>
  * <span data-ttu-id="6d479-219">Se viene visualizzata un'opzione per la selezione di un **Framework di destinazione** , selezionare la versione 2. x più recente.</span><span class="sxs-lookup"><span data-stu-id="6d479-219">If presented an option to select a **Target Framework** , select the latest 2.x version.</span></span>

  <span data-ttu-id="6d479-220">Selezionare **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="6d479-220">Select **Next** .</span></span>

* <span data-ttu-id="6d479-221">Assegnare al progetto il nome **:::no-loc(Razor)::: PagesMovie** e quindi selezionare **Crea** .</span><span class="sxs-lookup"><span data-stu-id="6d479-221">Name the project **:::no-loc(Razor):::PagesMovie** , and then select **Create** .</span></span>

  ![nameproj](razor-pages-start/_static/:::no-loc(Razor):::PagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="6d479-223">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="6d479-223">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6d479-224">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6d479-224">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6d479-225">Premere CTRL+F5 per l'esecuzione senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="6d479-225">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="6d479-226">Visual Studio avvia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ed esegue l'app.</span><span class="sxs-lookup"><span data-stu-id="6d479-226">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="6d479-227">La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="6d479-227">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="6d479-228">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="6d479-228">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="6d479-229">Localhost viene usato solo per le richieste web del computer locale.</span><span class="sxs-lookup"><span data-stu-id="6d479-229">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="6d479-230">Quando Visual Studio crea un progetto Web, viene usata una porta casuale per il server Web.</span><span class="sxs-lookup"><span data-stu-id="6d479-230">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="6d479-231">Nella home page dell'app selezionare **Accetto** per autorizzare il rilevamento.</span><span class="sxs-lookup"><span data-stu-id="6d479-231">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="6d479-232">Questa app non tiene traccia delle informazioni personali, ma il modello di progetto include la funzionalità di consenso nel caso in cui risulti necessaria la conformità al [Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="6d479-232">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Pagina Home o di indice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="6d479-234">La figura seguente visualizza l'app dopo che è stato impostato il consenso al rilevamento:</span><span class="sxs-lookup"><span data-stu-id="6d479-234">The following image shows the app after you give consent to tracking:</span></span>

  ![Pagina Home o di indice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="6d479-236">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6d479-236">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="6d479-237">Premere **CTRL+F5** per l'esecuzione senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="6d479-237">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="6d479-238">Visual Studio Code avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="6d479-238">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="6d479-239">La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="6d479-239">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="6d479-240">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="6d479-240">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="6d479-241">Localhost viene usato solo per le richieste web del computer locale.</span><span class="sxs-lookup"><span data-stu-id="6d479-241">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="6d479-242">Nella home page dell'app selezionare **Accetto** per autorizzare il rilevamento.</span><span class="sxs-lookup"><span data-stu-id="6d479-242">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="6d479-243">Questa app non tiene traccia delle informazioni personali, ma il modello di progetto include la funzionalità di consenso nel caso in cui risulti necessaria la conformità al [Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="6d479-243">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Pagina Home o di indice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="6d479-245">La figura seguente visualizza l'app dopo che è stato impostato il consenso al rilevamento:</span><span class="sxs-lookup"><span data-stu-id="6d479-245">The following image shows the app after you give consent to tracking:</span></span>

  ![Pagina Home o di indice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6d479-247">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6d479-247">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="6d479-248">Premere **Cmd-Opt-F5** per l'esecuzione senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="6d479-248">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="6d479-249">Visual Studio avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="6d479-249">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="6d479-250">Nella home page dell'app selezionare **Accetto** per autorizzare il rilevamento.</span><span class="sxs-lookup"><span data-stu-id="6d479-250">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="6d479-251">Questa app non tiene traccia delle informazioni personali, ma il modello di progetto include la funzionalità di consenso nel caso in cui risulti necessaria la conformità al [Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="6d479-251">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Pagina Home o di indice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="6d479-253">La figura seguente visualizza l'app dopo che è stato impostato il consenso al rilevamento:</span><span class="sxs-lookup"><span data-stu-id="6d479-253">The following image shows the app after you give consent to tracking:</span></span>

  ![Pagina Home o di indice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="6d479-255">Esaminare i file di progetto</span><span class="sxs-lookup"><span data-stu-id="6d479-255">Examine the project files</span></span>

<span data-ttu-id="6d479-256">Di seguito viene visualizzata una panoramica delle cartelle e dei file principali del progetto, che verranno usati in esercitazioni successive.</span><span class="sxs-lookup"><span data-stu-id="6d479-256">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="6d479-257">Cartella Pages</span><span class="sxs-lookup"><span data-stu-id="6d479-257">Pages folder</span></span>

<span data-ttu-id="6d479-258">Contiene :::no-loc(Razor)::: pagine e file di supporto.</span><span class="sxs-lookup"><span data-stu-id="6d479-258">Contains :::no-loc(Razor)::: pages and supporting files.</span></span> <span data-ttu-id="6d479-259">Ogni :::no-loc(Razor)::: pagina è una coppia di file:</span><span class="sxs-lookup"><span data-stu-id="6d479-259">Each :::no-loc(Razor)::: page is a pair of files:</span></span>

* <span data-ttu-id="6d479-260">Un file con *estensione cshtml* che contiene markup HTML con codice C# che utilizza la :::no-loc(Razor)::: sintassi.</span><span class="sxs-lookup"><span data-stu-id="6d479-260">A *.cshtml* file that contains HTML markup with C# code using :::no-loc(Razor)::: syntax.</span></span>
* <span data-ttu-id="6d479-261">Un file con estensione *cshtml.cs* contenente il codice C# per la gestione degli eventi della pagina.</span><span class="sxs-lookup"><span data-stu-id="6d479-261">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="6d479-262">I nomi dei file di supporto iniziano con un carattere di sottolineatura.</span><span class="sxs-lookup"><span data-stu-id="6d479-262">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="6d479-263">Ad esempio, il file *_Layout. cshtml* configura gli elementi dell'interfaccia utente comuni a tutte le pagine.</span><span class="sxs-lookup"><span data-stu-id="6d479-263">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="6d479-264">Questo file imposta il menu di navigazione nella parte superiore della pagina e le informazioni sul copyright in fondo alla pagina.</span><span class="sxs-lookup"><span data-stu-id="6d479-264">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="6d479-265">Per altre informazioni, vedere <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="6d479-265">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="6d479-266">Cartella wwwroot</span><span class="sxs-lookup"><span data-stu-id="6d479-266">wwwroot folder</span></span>

<span data-ttu-id="6d479-267">Contiene i file statici, ad esempio i file HTML, i file JavaScript e i file CSS.</span><span class="sxs-lookup"><span data-stu-id="6d479-267">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="6d479-268">Per altre informazioni, vedere <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="6d479-268">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="6d479-269">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="6d479-269">appSettings.json</span></span>

<span data-ttu-id="6d479-270">Contiene i dati di configurazione, ad esempio le stringhe di connessione.</span><span class="sxs-lookup"><span data-stu-id="6d479-270">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="6d479-271">Per altre informazioni, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="6d479-271">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="6d479-272">Program.cs</span><span class="sxs-lookup"><span data-stu-id="6d479-272">Program.cs</span></span>

<span data-ttu-id="6d479-273">Contiene il punto di ingresso per il programma.</span><span class="sxs-lookup"><span data-stu-id="6d479-273">Contains the entry point for the program.</span></span> <span data-ttu-id="6d479-274">Per altre informazioni, vedere <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="6d479-274">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="6d479-275">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="6d479-275">Startup.cs</span></span>

<span data-ttu-id="6d479-276">Contiene il codice che configura il comportamento dell'app, ad esempio se richiede il consenso per :::no-loc(cookie)::: s.</span><span class="sxs-lookup"><span data-stu-id="6d479-276">Contains code that configures app behavior, such as whether it requires consent for :::no-loc(cookie):::s.</span></span> <span data-ttu-id="6d479-277">Per altre informazioni, vedere <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="6d479-277">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6d479-278">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="6d479-278">Additional resources</span></span>

* [<span data-ttu-id="6d479-279">Versione di YouTube di questa esercitazione</span><span class="sxs-lookup"><span data-stu-id="6d479-279">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="6d479-280">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="6d479-280">Next steps</span></span>

<span data-ttu-id="6d479-281">Passare all'esercitazione successiva della serie:</span><span class="sxs-lookup"><span data-stu-id="6d479-281">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="6d479-282">Aggiunta di un modello</span><span class="sxs-lookup"><span data-stu-id="6d479-282">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
