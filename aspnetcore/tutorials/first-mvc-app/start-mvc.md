---
title: Introduzione ad ASP.NET Core MVC
author: rick-anderson
description: Informazioni introduttive su ASP.NET Core MVC.
ms.author: riande
ms.date: 01/20/2021
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
uid: tutorials/first-mvc-app/start-mvc
ms.custom: contperf-fy21q3
ms.openlocfilehash: aaf930eee351ed757be60f648bce88b182d52799
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710800"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="297d5-103">Introduzione ad ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="297d5-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="297d5-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="297d5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="297d5-105">Questa è la prima esercitazione di una serie che insegna ASP.NET Core lo sviluppo Web MVC con i controller e le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="297d5-105">This is the first tutorial of a series that teaches ASP.NET Core MVC web development with controllers and views.</span></span>

<span data-ttu-id="297d5-106">Alla fine della serie, si disporrà di un'app che gestisce e Visualizza i dati dei film.</span><span class="sxs-lookup"><span data-stu-id="297d5-106">At the end of the series, you'll have an app that manages and displays movie data.</span></span> <span data-ttu-id="297d5-107">Si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="297d5-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="297d5-108">Creare un'app Web.</span><span class="sxs-lookup"><span data-stu-id="297d5-108">Create a web app.</span></span>
> * <span data-ttu-id="297d5-109">Aggiungere un modello ed eseguirne lo scaffolding.</span><span class="sxs-lookup"><span data-stu-id="297d5-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="297d5-110">Usare un database.</span><span class="sxs-lookup"><span data-stu-id="297d5-110">Work with a database.</span></span>
> * <span data-ttu-id="297d5-111">Aggiungere ricerca e convalida.</span><span class="sxs-lookup"><span data-stu-id="297d5-111">Add search and validation.</span></span>

<span data-ttu-id="297d5-112">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="297d5-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="297d5-113">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="297d5-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="297d5-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="297d5-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="297d5-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="297d5-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="297d5-116">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="297d5-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="297d5-117">Creare un'app Web</span><span class="sxs-lookup"><span data-stu-id="297d5-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="297d5-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="297d5-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="297d5-119">Avviare Visual Studio e selezionare **Crea un nuovo progetto**.</span><span class="sxs-lookup"><span data-stu-id="297d5-119">Start Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="297d5-120">Nella finestra di dialogo **Crea un nuovo progetto** selezionare **ASP.NET Core applicazione Web** > **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="297d5-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
* <span data-ttu-id="297d5-121">Nella finestra di dialogo **Configura nuovo progetto** immettere `MvcMovie` per **nome progetto**.</span><span class="sxs-lookup"><span data-stu-id="297d5-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="297d5-122">È importante denominare il progetto *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="297d5-122">It's important to name the project *MvcMovie*.</span></span> <span data-ttu-id="297d5-123">Quando viene copiato il codice, l'utilizzo delle maiuscole deve corrispondere a ogni corrispondenza `namespace` .</span><span class="sxs-lookup"><span data-stu-id="297d5-123">Capitalization needs to match each `namespace` matches when code is copied.</span></span>
* <span data-ttu-id="297d5-124">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="297d5-124">Select **Create**.</span></span>
* <span data-ttu-id="297d5-125">Nella finestra di dialogo **Crea una nuova applicazione web ASP.NET Core** selezionare:</span><span class="sxs-lookup"><span data-stu-id="297d5-125">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
  * <span data-ttu-id="297d5-126">**.NET Core** e **ASP.NET Core 5,0** negli elenchi a discesa.</span><span class="sxs-lookup"><span data-stu-id="297d5-126">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
  * <span data-ttu-id="297d5-127">**ASP.NET Core app Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="297d5-127">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
  * <span data-ttu-id="297d5-128">**Create**.</span><span class="sxs-lookup"><span data-stu-id="297d5-128">**Create**.</span></span>

![<span data-ttu-id="297d5-129">Creare una nuova applicazione Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="297d5-129">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/mvcVS19v16.9.png)

<span data-ttu-id="297d5-130">Per approcci alternativi alla creazione del progetto, vedere [creare un nuovo progetto in Visual Studio](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="297d5-130">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="297d5-131">Visual Studio ha usato il modello di progetto predefinito per il progetto MVC creato.</span><span class="sxs-lookup"><span data-stu-id="297d5-131">Visual Studio used the default project template for the created MVC project.</span></span> <span data-ttu-id="297d5-132">Il progetto creato:</span><span class="sxs-lookup"><span data-stu-id="297d5-132">The created project:</span></span>

* <span data-ttu-id="297d5-133">È un'app funzionante.</span><span class="sxs-lookup"><span data-stu-id="297d5-133">Is a working app.</span></span>
* <span data-ttu-id="297d5-134">È un progetto iniziale di base.</span><span class="sxs-lookup"><span data-stu-id="297d5-134">Is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="297d5-135">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="297d5-135">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="297d5-136">L'esercitazione presuppone una certa familiarità con VS Code.</span><span class="sxs-lookup"><span data-stu-id="297d5-136">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="297d5-137">Per ulteriori informazioni, vedere la pagina relativa all'introduzione [a vs code](https://code.visualstudio.com/docs) e [Visual Studio Code Guida](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="297d5-137">For more information, see [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help).</span></span>

* <span data-ttu-id="297d5-138">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="297d5-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="297d5-139">Passare alla directory ( `cd` ) che conterrà il progetto.</span><span class="sxs-lookup"><span data-stu-id="297d5-139">Change to the directory (`cd`) that will contain the project.</span></span>
* <span data-ttu-id="297d5-140">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="297d5-140">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="297d5-141">Se viene visualizzata una finestra di dialogo con le **risorse necessarie per la compilazione e il debug non è presente in ' MvcMovie '. Aggiungi?**, seleziona **Sì**</span><span class="sxs-lookup"><span data-stu-id="297d5-141">If a dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**, select **Yes**</span></span>

  * <span data-ttu-id="297d5-142">`dotnet new mvc -o MvcMovie`: Crea un nuovo progetto MVC ASP.NET Core nella cartella *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="297d5-142">`dotnet new mvc -o MvcMovie`: Creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="297d5-143">`code -r MvcMovie`: Carica il file di progetto *MvcMovie. csproj* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="297d5-143">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="297d5-144">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="297d5-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="297d5-145">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="297d5-145">Select **File** > **New Solution**.</span></span>

  ![Nuova soluzione macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="297d5-147">In Visual Studio per Mac precedente alla versione 8,6, selezionare applicazione Web **.NET Core**  >  **app**  >  **(Model-View-Controller)**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="297d5-147">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="297d5-148">Nella versione 8,6 o successive selezionare **Web e applicazione console**  >  **app**  >  **Web (Model-View-Controller)**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="297d5-148">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![selezione del modello di app Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="297d5-150">Nella finestra di dialogo **Configura nuova applicazione Web** :</span><span class="sxs-lookup"><span data-stu-id="297d5-150">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="297d5-151">Verificare che **l'autenticazione** sia impostata su **Nessuna autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="297d5-151">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="297d5-152">Se viene visualizzata un'opzione per la selezione di un **Framework di destinazione** , selezionare la versione 5. x più recente.</span><span class="sxs-lookup"><span data-stu-id="297d5-152">If an option to select a **Target Framework** is presented, select the latest 5.x version.</span></span>
  * <span data-ttu-id="297d5-153">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="297d5-153">Select **Next**.</span></span>

* <span data-ttu-id="297d5-154">Denominare il progetto **MvcMovie**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="297d5-154">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS nome del progetto](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="297d5-156">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="297d5-156">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="297d5-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="297d5-157">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="297d5-158">Premere CTRL + F5 per eseguire l'app senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="297d5-158">Select Ctrl+F5 to run the app without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="297d5-159">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="297d5-159">Visual Studio:</span></span>

  * <span data-ttu-id="297d5-160">Avvia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span><span class="sxs-lookup"><span data-stu-id="297d5-160">Starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span></span>
  * <span data-ttu-id="297d5-161">Esegue l'app.</span><span class="sxs-lookup"><span data-stu-id="297d5-161">Runs the app.</span></span>

  <span data-ttu-id="297d5-162">La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="297d5-162">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="297d5-163">Il nome host standard per il computer locale è `localhost` .</span><span class="sxs-lookup"><span data-stu-id="297d5-163">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="297d5-164">Quando Visual Studio crea un progetto Web, viene usata una porta casuale per il server Web.</span><span class="sxs-lookup"><span data-stu-id="297d5-164">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="297d5-165">L'avvio dell'app senza debug selezionando CTRL + F5 consente di:</span><span class="sxs-lookup"><span data-stu-id="297d5-165">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="297d5-166">Apportare modifiche al codice.</span><span class="sxs-lookup"><span data-stu-id="297d5-166">Make code changes.</span></span>
* <span data-ttu-id="297d5-167">Salvare il file.</span><span class="sxs-lookup"><span data-stu-id="297d5-167">Save the file.</span></span>
* <span data-ttu-id="297d5-168">Aggiornare rapidamente il browser e visualizzare le modifiche al codice.</span><span class="sxs-lookup"><span data-stu-id="297d5-168">Quickly refresh the browser and see the code changes.</span></span>

<span data-ttu-id="297d5-169">È possibile scegliere se avviare l'app in modalità di debug o non di debug nella voce di menu **Debug**:</span><span class="sxs-lookup"><span data-stu-id="297d5-169">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Menu Debug](start-mvc/_static/debug_menu50.png)

<span data-ttu-id="297d5-171">È possibile eseguire il debug dell'app toccando il pulsante **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="297d5-171">You can debug the app by selecting the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express50.png)

<span data-ttu-id="297d5-173">La figura seguente mostra l'app:</span><span class="sxs-lookup"><span data-stu-id="297d5-173">The following image shows the app:</span></span>

![Pagina Home o di indice](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="297d5-175">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="297d5-175">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="297d5-176">Premere CTRL + F5 per eseguire senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="297d5-176">Select Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="297d5-177">Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="297d5-177">Visual Studio Code:</span></span>

  * <span data-ttu-id="297d5-178">Avvia [gheppio](xref:fundamentals/servers/kestrel)</span><span class="sxs-lookup"><span data-stu-id="297d5-178">Starts [Kestrel](xref:fundamentals/servers/kestrel)</span></span>
  * <span data-ttu-id="297d5-179">Avvia un browser.</span><span class="sxs-lookup"><span data-stu-id="297d5-179">Launches a browser.</span></span>
  * <span data-ttu-id="297d5-180">Passa a `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="297d5-180">Navigates to `https://localhost:5001`.</span></span>

  <span data-ttu-id="297d5-181">La barra degli indirizzi visualizza `localhost:port:5001` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="297d5-181">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="297d5-182">Il nome host standard per il computer locale è `localhost` .</span><span class="sxs-lookup"><span data-stu-id="297d5-182">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="297d5-183">Localhost viene usato solo per le richieste web del computer locale.</span><span class="sxs-lookup"><span data-stu-id="297d5-183">Localhost only serves web requests from the local computer.</span></span>

<span data-ttu-id="297d5-184">L'avvio dell'app senza debug selezionando CTRL + F5 consente di:</span><span class="sxs-lookup"><span data-stu-id="297d5-184">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="297d5-185">Apportare modifiche al codice.</span><span class="sxs-lookup"><span data-stu-id="297d5-185">Make code changes.</span></span>
* <span data-ttu-id="297d5-186">Salvare il file.</span><span class="sxs-lookup"><span data-stu-id="297d5-186">Save the file.</span></span>
* <span data-ttu-id="297d5-187">Aggiornare rapidamente il browser e visualizzare le modifiche al codice.</span><span class="sxs-lookup"><span data-stu-id="297d5-187">Quickly refresh the browser and see the code changes.</span></span>

  ![Pagina Home o di indice](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="297d5-189">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="297d5-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="297d5-190">Per avviare l'app, selezionare **Esegui** > **Avvia senza eseguire debug**.</span><span class="sxs-lookup"><span data-stu-id="297d5-190">Select **Run** > **Start Without Debugging** to launch the app.</span></span>

  <span data-ttu-id="297d5-191">Visual Studio per Mac:</span><span class="sxs-lookup"><span data-stu-id="297d5-191">Visual Studio for Mac:</span></span>

  * <span data-ttu-id="297d5-192">Avvia il server [gheppio](xref:fundamentals/servers/index#kestrel) .</span><span class="sxs-lookup"><span data-stu-id="297d5-192">Starts [Kestrel](xref:fundamentals/servers/index#kestrel) server.</span></span>
  * <span data-ttu-id="297d5-193">Avvia un browser.</span><span class="sxs-lookup"><span data-stu-id="297d5-193">Launches a browser.</span></span>
  * <span data-ttu-id="297d5-194">Passa a `http://localhost:port` , dove *porta* è un numero di porta scelto in modo casuale.</span><span class="sxs-lookup"><span data-stu-id="297d5-194">Navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

  [!INCLUDE[](~/includes/trustCertMac.md)]

  <span data-ttu-id="297d5-195">La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="297d5-195">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="297d5-196">Il nome host standard per il computer locale è `localhost` .</span><span class="sxs-lookup"><span data-stu-id="297d5-196">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="297d5-197">Quando Visual Studio crea un progetto Web, viene usata una porta casuale per il server Web.</span><span class="sxs-lookup"><span data-stu-id="297d5-197">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="297d5-198">È possibile scegliere se avviare l'app in modalità di debug o non di dal menu **Esegui**.</span><span class="sxs-lookup"><span data-stu-id="297d5-198">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="297d5-199">La figura seguente mostra l'app:</span><span class="sxs-lookup"><span data-stu-id="297d5-199">The following image shows the app:</span></span>

![Pagina Home o di indice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="297d5-201">Nella parte seguente di questa esercitazione vengono fornite informazioni su MVC e istruzioni per iniziare a creare codice.</span><span class="sxs-lookup"><span data-stu-id="297d5-201">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="297d5-202">Passaggio successivo: aggiungere un controller</span><span class="sxs-lookup"><span data-stu-id="297d5-202">Next: Add a controller</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="297d5-203">Questa è la prima esercitazione di una serie che insegna ASP.NET Core lo sviluppo Web MVC con i controller e le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="297d5-203">This is the first tutorial of a series that teaches ASP.NET Core MVC web development with controllers and views.</span></span>

<span data-ttu-id="297d5-204">Alla fine della serie, si disporrà di un'app che gestisce e Visualizza i dati dei film.</span><span class="sxs-lookup"><span data-stu-id="297d5-204">At the end of the series, you'll have an app that manages and displays movie data.</span></span> <span data-ttu-id="297d5-205">Si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="297d5-205">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="297d5-206">Creare un'app Web.</span><span class="sxs-lookup"><span data-stu-id="297d5-206">Create a web app.</span></span>
> * <span data-ttu-id="297d5-207">Aggiungere un modello ed eseguirne lo scaffolding.</span><span class="sxs-lookup"><span data-stu-id="297d5-207">Add and scaffold a model.</span></span>
> * <span data-ttu-id="297d5-208">Usare un database.</span><span class="sxs-lookup"><span data-stu-id="297d5-208">Work with a database.</span></span>
> * <span data-ttu-id="297d5-209">Aggiungere ricerca e convalida.</span><span class="sxs-lookup"><span data-stu-id="297d5-209">Add search and validation.</span></span>

<span data-ttu-id="297d5-210">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="297d5-210">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="297d5-211">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="297d5-211">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="297d5-212">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="297d5-212">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="297d5-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="297d5-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="297d5-214">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="297d5-214">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="297d5-215">Creare un'app Web</span><span class="sxs-lookup"><span data-stu-id="297d5-215">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="297d5-216">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="297d5-216">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="297d5-217">In Visual Studio selezionare **Crea un nuovo progetto**.</span><span class="sxs-lookup"><span data-stu-id="297d5-217">From the Visual Studio, select **Create a new project**.</span></span>

* <span data-ttu-id="297d5-218">Selezionare **ASP.NET Core applicazione Web** > **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="297d5-218">Select **ASP.NET Core Web Application** > **Next**.</span></span>

  ![Creare un nuovo progetto di applicazione Web di ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="297d5-220">Assegnare al progetto il nome **MvcMovie** e selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="297d5-220">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="297d5-221">È importante assegnare al progetto il nome **MvcMovie**, in modo che quando si copia il codice lo spazio dei nomi corrisponda.</span><span class="sxs-lookup"><span data-stu-id="297d5-221">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Configurare il nuovo progetto](start-mvc/_static/config.png)

* <span data-ttu-id="297d5-223">Selezionare **applicazione Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="297d5-223">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="297d5-224">Nelle caselle a discesa selezionare **.NET Core** e **ASP.NET Core 3,1**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="297d5-224">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

  ![<span data-ttu-id="297d5-225">Finestra di dialogo Nuovo progetto, .NET Core nel riquadro sinistro, Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="297d5-225">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="297d5-226">Visual Studio ha usato il modello di progetto predefinito per il progetto MVC creato.</span><span class="sxs-lookup"><span data-stu-id="297d5-226">Visual Studio used the default project template for the created MVC project.</span></span> <span data-ttu-id="297d5-227">Il progetto creato:</span><span class="sxs-lookup"><span data-stu-id="297d5-227">The created project:</span></span>

* <span data-ttu-id="297d5-228">È un'app funzionante.</span><span class="sxs-lookup"><span data-stu-id="297d5-228">Is a working app.</span></span>
* <span data-ttu-id="297d5-229">È un progetto iniziale di base.</span><span class="sxs-lookup"><span data-stu-id="297d5-229">Is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="297d5-230">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="297d5-230">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="297d5-231">L'esercitazione presuppone una certa familiarità con VS Code.</span><span class="sxs-lookup"><span data-stu-id="297d5-231">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="297d5-232">Per ulteriori informazioni, vedere la pagina relativa all'introduzione [a vs code](https://code.visualstudio.com/docs) e [Visual Studio Code Guida](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="297d5-232">For more information, see [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help).</span></span>

* <span data-ttu-id="297d5-233">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="297d5-233">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="297d5-234">Modificare le directory ( `cd` ) in una cartella che conterrà il progetto.</span><span class="sxs-lookup"><span data-stu-id="297d5-234">Change directories (`cd`) to a folder that will contain the project.</span></span>
* <span data-ttu-id="297d5-235">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="297d5-235">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="297d5-236">Viene visualizzata una finestra di dialogo con le **risorse necessarie per la compilazione e il debug non è presente in ' MvcMovie '. Aggiungerli?** selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="297d5-236">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**, select **Yes**.</span></span>

  * <span data-ttu-id="297d5-237">`dotnet new mvc -o MvcMovie`: Crea un nuovo progetto MVC ASP.NET Core nella cartella *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="297d5-237">`dotnet new mvc -o MvcMovie`: Creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="297d5-238">`code -r MvcMovie`: Carica il file di progetto *MvcMovie. csproj* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="297d5-238">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="297d5-239">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="297d5-239">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="297d5-240">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="297d5-240">Select **File** > **New Solution**.</span></span>

  ![Nuova soluzione macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="297d5-242">In Visual Studio per Mac precedente alla versione 8,6, selezionare applicazione Web **.NET Core**  >  **app**  >  **(Model-View-Controller)**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="297d5-242">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="297d5-243">Nella versione 8,6 o successive selezionare **Web e applicazione console**  >  **app**  >  **Web (Model-View-Controller)**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="297d5-243">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![selezione del modello di app Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="297d5-245">Nella finestra di dialogo **Configura nuova applicazione Web** :</span><span class="sxs-lookup"><span data-stu-id="297d5-245">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="297d5-246">Verificare che **l'autenticazione** sia impostata su **Nessuna autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="297d5-246">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="297d5-247">Se viene visualizzata un'opzione per la selezione di un **Framework di destinazione** , selezionare la versione 3. x più recente.</span><span class="sxs-lookup"><span data-stu-id="297d5-247">If an option to select a **Target Framework** is presented, select the latest 3.x version.</span></span>
  * <span data-ttu-id="297d5-248">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="297d5-248">Select **Next**.</span></span>

* <span data-ttu-id="297d5-249">Denominare il progetto **MvcMovie**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="297d5-249">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS nome del progetto](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="297d5-251">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="297d5-251">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="297d5-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="297d5-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="297d5-253">Premere CTRL + F5 per eseguire l'app senza debug.</span><span class="sxs-lookup"><span data-stu-id="297d5-253">Select Ctrl+F5 to run the app without debugging.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="297d5-254">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="297d5-254">Visual Studio:</span></span>

  * <span data-ttu-id="297d5-255">Avvia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span><span class="sxs-lookup"><span data-stu-id="297d5-255">Starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span></span>
  * <span data-ttu-id="297d5-256">Esegue l'app.</span><span class="sxs-lookup"><span data-stu-id="297d5-256">Runs the app.</span></span>

  <span data-ttu-id="297d5-257">La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="297d5-257">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="297d5-258">Il nome host standard per il computer locale è `localhost` .</span><span class="sxs-lookup"><span data-stu-id="297d5-258">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="297d5-259">Quando Visual Studio crea un progetto Web, viene usata una porta casuale per il server Web.</span><span class="sxs-lookup"><span data-stu-id="297d5-259">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="297d5-260">L'avvio dell'app senza debug selezionando CTRL + F5 consente di:</span><span class="sxs-lookup"><span data-stu-id="297d5-260">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="297d5-261">Apportare modifiche al codice.</span><span class="sxs-lookup"><span data-stu-id="297d5-261">Make code changes.</span></span>
* <span data-ttu-id="297d5-262">Salvare il file.</span><span class="sxs-lookup"><span data-stu-id="297d5-262">Save the file.</span></span>
* <span data-ttu-id="297d5-263">Aggiornare rapidamente il browser e visualizzare le modifiche al codice.</span><span class="sxs-lookup"><span data-stu-id="297d5-263">Quickly refresh the browser and see the code changes.</span></span>

<span data-ttu-id="297d5-264">È possibile scegliere se avviare l'app in modalità di debug o non di debug nella voce di menu **Debug**:</span><span class="sxs-lookup"><span data-stu-id="297d5-264">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Menu Debug](start-mvc/_static/debug_menu.png)

<span data-ttu-id="297d5-266">È possibile eseguire il debug dell'app toccando il pulsante **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="297d5-266">You can debug the app by selecting the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express.png)

<span data-ttu-id="297d5-268">La figura seguente mostra l'app:</span><span class="sxs-lookup"><span data-stu-id="297d5-268">The following image shows the app:</span></span>

![Pagina Home o di indice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="297d5-270">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="297d5-270">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="297d5-271">Premere CTRL + F5 per eseguire l'app senza debug.</span><span class="sxs-lookup"><span data-stu-id="297d5-271">Select Ctrl+F5 to run the app without debugging.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="297d5-272">Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="297d5-272">Visual Studio Code:</span></span>

  * <span data-ttu-id="297d5-273">Avvia [gheppio](xref:fundamentals/servers/kestrel)</span><span class="sxs-lookup"><span data-stu-id="297d5-273">Starts [Kestrel](xref:fundamentals/servers/kestrel)</span></span>
  * <span data-ttu-id="297d5-274">Avvia un browser.</span><span class="sxs-lookup"><span data-stu-id="297d5-274">Launches a browser.</span></span>
  * <span data-ttu-id="297d5-275">Passa a `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="297d5-275">Navigates to `https://localhost:5001`.</span></span>

  <span data-ttu-id="297d5-276">La barra degli indirizzi visualizza `localhost:port:5001` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="297d5-276">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="297d5-277">Il nome host standard per il computer locale è `localhost` .</span><span class="sxs-lookup"><span data-stu-id="297d5-277">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="297d5-278">Localhost viene usato solo per le richieste web del computer locale.</span><span class="sxs-lookup"><span data-stu-id="297d5-278">Localhost only serves web requests from the local computer.</span></span>

<span data-ttu-id="297d5-279">L'avvio dell'app senza debug selezionando CTRL + F5 consente di:</span><span class="sxs-lookup"><span data-stu-id="297d5-279">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="297d5-280">Apportare modifiche al codice.</span><span class="sxs-lookup"><span data-stu-id="297d5-280">Make code changes.</span></span>
* <span data-ttu-id="297d5-281">Salvare il file.</span><span class="sxs-lookup"><span data-stu-id="297d5-281">Save the file.</span></span>
* <span data-ttu-id="297d5-282">Aggiornare rapidamente il browser e visualizzare le modifiche al codice.</span><span class="sxs-lookup"><span data-stu-id="297d5-282">Quickly refresh the browser and see the code changes.</span></span>

  ![Pagina Home o di indice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="297d5-284">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="297d5-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="297d5-285">Per avviare l'app, selezionare **Esegui** > **Avvia senza eseguire debug**.</span><span class="sxs-lookup"><span data-stu-id="297d5-285">Select **Run** > **Start Without Debugging** to launch the app.</span></span>

  <span data-ttu-id="297d5-286">Visual Studio per Mac: avvia il server [gheppio](xref:fundamentals/servers/index#kestrel) , avvia un browser e passa a `http://localhost:port` , dove *porta* è un numero di porta scelto in modo casuale.</span><span class="sxs-lookup"><span data-stu-id="297d5-286">Visual Studio for Mac: starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

<span data-ttu-id="297d5-287">La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="297d5-287">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="297d5-288">Il nome host standard per il computer locale è `localhost` .</span><span class="sxs-lookup"><span data-stu-id="297d5-288">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="297d5-289">Quando Visual Studio crea un progetto Web, viene usata una porta casuale per il server Web.</span><span class="sxs-lookup"><span data-stu-id="297d5-289">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="297d5-290">Quando si esegue l'app verrà visualizzato un numero di porta diverso.</span><span class="sxs-lookup"><span data-stu-id="297d5-290">When you run the app, you'll see a different port number.</span></span>

<span data-ttu-id="297d5-291">È possibile scegliere se avviare l'app in modalità di debug o non di dal menu **Esegui**.</span><span class="sxs-lookup"><span data-stu-id="297d5-291">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="297d5-292">La figura seguente mostra l'app:</span><span class="sxs-lookup"><span data-stu-id="297d5-292">The following image shows the app:</span></span>

![Pagina Home o di indice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="297d5-294">Nella parte seguente di questa esercitazione vengono fornite informazioni su MVC e istruzioni per iniziare a creare codice.</span><span class="sxs-lookup"><span data-stu-id="297d5-294">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="297d5-295">Avanti</span><span class="sxs-lookup"><span data-stu-id="297d5-295">Next</span></span>](adding-controller.md)

::: moniker-end
