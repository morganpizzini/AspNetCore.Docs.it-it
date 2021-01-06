---
title: Introduzione ad ASP.NET Core MVC
author: rick-anderson
description: Informazioni introduttive su ASP.NET Core MVC.
ms.author: riande
ms.date: 11/16/2020
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
ms.openlocfilehash: c96e7107c85bf36f55f6571c71c20d09bc94ddb3
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "94688497"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="dba14-103">Introduzione ad ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="dba14-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="dba14-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="dba14-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="dba14-105">Questa esercitazione illustra le nozioni di base della creazione di un'app Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="dba14-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="dba14-106">L'app gestisce un database di titoli di film.</span><span class="sxs-lookup"><span data-stu-id="dba14-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="dba14-107">Si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="dba14-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dba14-108">Creare un'app Web.</span><span class="sxs-lookup"><span data-stu-id="dba14-108">Create a web app.</span></span>
> * <span data-ttu-id="dba14-109">Aggiungere un modello ed eseguirne lo scaffolding.</span><span class="sxs-lookup"><span data-stu-id="dba14-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="dba14-110">Usare un database.</span><span class="sxs-lookup"><span data-stu-id="dba14-110">Work with a database.</span></span>
> * <span data-ttu-id="dba14-111">Aggiungere ricerca e convalida.</span><span class="sxs-lookup"><span data-stu-id="dba14-111">Add search and validation.</span></span>

<span data-ttu-id="dba14-112">Al termine di queste operazioni si ottiene un'app che può gestire e visualizzare dati relativi ai film.</span><span class="sxs-lookup"><span data-stu-id="dba14-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="dba14-113">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="dba14-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dba14-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dba14-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="dba14-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dba14-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dba14-116">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="dba14-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="dba14-117">Creare un'app Web</span><span class="sxs-lookup"><span data-stu-id="dba14-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dba14-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dba14-118">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="dba14-119">Avviare Visual Studio e selezionare **Crea un nuovo progetto**.</span><span class="sxs-lookup"><span data-stu-id="dba14-119">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="dba14-120">Nella finestra di dialogo **Crea un nuovo progetto** selezionare **ASP.NET Core applicazione Web** > **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="dba14-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="dba14-121">Nella finestra di dialogo **Configura nuovo progetto** immettere `MvcMovie` per **nome progetto**.</span><span class="sxs-lookup"><span data-stu-id="dba14-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="dba14-122">È importante usare questo nome esatto, incluse le maiuscole, in modo che ogni `namespace` corrisponda al momento della copia del codice.</span><span class="sxs-lookup"><span data-stu-id="dba14-122">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="dba14-123">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="dba14-123">Select **Create**.</span></span>
1. <span data-ttu-id="dba14-124">Nella finestra di dialogo **Crea una nuova applicazione web ASP.NET Core** selezionare:</span><span class="sxs-lookup"><span data-stu-id="dba14-124">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="dba14-125">**.NET Core** e **ASP.NET Core 5,0** negli elenchi a discesa.</span><span class="sxs-lookup"><span data-stu-id="dba14-125">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="dba14-126">**ASP.NET Core app Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="dba14-126">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
    1. <span data-ttu-id="dba14-127">**Creare**</span><span class="sxs-lookup"><span data-stu-id="dba14-127">**Create**</span></span>

![<span data-ttu-id="dba14-128">Creare una nuova applicazione Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dba14-128">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/mvcVS19v16.9.png)

<span data-ttu-id="dba14-129">Per approcci alternativi alla creazione del progetto, vedere [creare un nuovo progetto in Visual Studio](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="dba14-129">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="dba14-130">Per il progetto MVC appena creato Visual Studio ha usato il modello predefinito.</span><span class="sxs-lookup"><span data-stu-id="dba14-130">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="dba14-131">Ora è possibile disporre di un'app funzionante immettendo un nome progetto e selezionando alcune opzioni.</span><span class="sxs-lookup"><span data-stu-id="dba14-131">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="dba14-132">Si tratta di un progetto iniziale di base.</span><span class="sxs-lookup"><span data-stu-id="dba14-132">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dba14-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dba14-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dba14-134">L'esercitazione presuppone una certa familiarità con VS Code.</span><span class="sxs-lookup"><span data-stu-id="dba14-134">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="dba14-135">Vedere [Introduzione a VS Code](https://code.visualstudio.com/docs) e [Guida a Visual Studio Code](#visual-studio-code-help) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="dba14-135">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="dba14-136">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="dba14-136">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="dba14-137">Cambiare directory (`cd`) e passare alla cartella che conterrà il progetto.</span><span class="sxs-lookup"><span data-stu-id="dba14-137">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="dba14-138">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="dba14-138">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="dba14-139">Viene visualizzata una finestra di dialogo con le **risorse necessarie per la compilazione e il debug non è presente in ' MvcMovie '. Aggiungerli?**</span><span class="sxs-lookup"><span data-stu-id="dba14-139">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="dba14-140">Selezionare **Sì**</span><span class="sxs-lookup"><span data-stu-id="dba14-140">Select **Yes**</span></span>

  * <span data-ttu-id="dba14-141">`dotnet new mvc -o MvcMovie`: crea un nuovo progetto ASP.NET Core MVC nella cartella *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="dba14-141">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="dba14-142">`code -r MvcMovie`: Carica il file di progetto *MvcMovie. csproj* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="dba14-142">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dba14-143">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="dba14-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="dba14-144">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="dba14-144">Select **File** > **New Solution**.</span></span>

  ![Nuova soluzione macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="dba14-146">In Visual Studio per Mac precedente alla versione 8,6, selezionare applicazione Web **.NET Core**  >  **app**  >  **(Model-View-Controller)**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="dba14-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="dba14-147">Nella versione 8,6 o successive selezionare **Web e applicazione console**  >  **app**  >  **Web (Model-View-Controller)**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="dba14-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![selezione del modello di app Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="dba14-149">Nella finestra di dialogo **Configura nuova applicazione Web** :</span><span class="sxs-lookup"><span data-stu-id="dba14-149">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="dba14-150">Verificare che **l'autenticazione** sia impostata su **Nessuna autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="dba14-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="dba14-151">Se viene visualizzata un'opzione per la selezione di un **Framework di destinazione**, selezionare la versione 5. x più recente.</span><span class="sxs-lookup"><span data-stu-id="dba14-151">If presented an option to select a **Target Framework**, select the latest 5.x version.</span></span>

  <span data-ttu-id="dba14-152">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="dba14-152">Select **Next**.</span></span>

* <span data-ttu-id="dba14-153">Denominare il progetto **MvcMovie**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="dba14-153">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS nome del progetto](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="dba14-155">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="dba14-155">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dba14-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dba14-156">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dba14-157">Selezionare **CTRL+F5** per eseguire l'app in modalità non di debug.</span><span class="sxs-lookup"><span data-stu-id="dba14-157">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="dba14-158">Visual Studio avvia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ed esegue l'app.</span><span class="sxs-lookup"><span data-stu-id="dba14-158">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="dba14-159">Si noti che la barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="dba14-159">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="dba14-160">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="dba14-160">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="dba14-161">Quando Visual Studio crea un progetto Web, viene usata una porta casuale per il server Web.</span><span class="sxs-lookup"><span data-stu-id="dba14-161">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="dba14-162">Se si avvia l'app con CTRL+F5 (modalità non di debug) sarà possibile apportare modifiche al codice, salvare il file, aggiornare il browser e visualizzare le modifiche del codice.</span><span class="sxs-lookup"><span data-stu-id="dba14-162">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="dba14-163">Molti sviluppatori preferiscono usare la modalità non di debug per avviare l'app rapidamente e visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="dba14-163">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="dba14-164">È possibile scegliere se avviare l'app in modalità di debug o non di debug nella voce di menu **Debug**:</span><span class="sxs-lookup"><span data-stu-id="dba14-164">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Debug](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="dba14-166">È possibile eseguire il debug dell'app toccando il pulsante **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="dba14-166">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="dba14-168">La figura seguente mostra l'app:</span><span class="sxs-lookup"><span data-stu-id="dba14-168">The following image shows the app:</span></span>

  ![Pagina Home o di indice](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="dba14-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dba14-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dba14-171">Premere CTRL+F5 per l'esecuzione senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="dba14-171">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="dba14-172">Visual Studio Code avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="dba14-172">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="dba14-173">La barra degli indirizzi visualizza `localhost:port:5001` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="dba14-173">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="dba14-174">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="dba14-174">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="dba14-175">Localhost viene usato solo per le richieste web del computer locale.</span><span class="sxs-lookup"><span data-stu-id="dba14-175">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="dba14-176">Se si avvia l'app con CTRL+F5 (modalità non di debug) sarà possibile apportare modifiche al codice, salvare il file, aggiornare il browser e visualizzare le modifiche del codice.</span><span class="sxs-lookup"><span data-stu-id="dba14-176">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="dba14-177">Molti sviluppatori preferiscono usare la modalità non di debug per aggiornare la pagina e visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="dba14-177">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Pagina Home o di indice](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dba14-179">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="dba14-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="dba14-180">Selezionare **Esegui**  >  **Avvia senza eseguire debug** per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="dba14-180">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="dba14-181">Visual Studio per Mac avvia il server [Kestrel](xref:fundamentals/servers/index#kestrel), apre un browser e naviga all'indirizzo `http://localhost:port`, dove *port* è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="dba14-181">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="dba14-182">La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="dba14-182">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="dba14-183">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="dba14-183">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="dba14-184">Quando Visual Studio crea un progetto Web, viene usata una porta casuale per il server Web.</span><span class="sxs-lookup"><span data-stu-id="dba14-184">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="dba14-185">Quando si esegue l'app verrà visualizzato un numero di porta diverso.</span><span class="sxs-lookup"><span data-stu-id="dba14-185">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="dba14-186">È possibile scegliere se avviare l'app in modalità di debug o non di dal menu **Esegui**.</span><span class="sxs-lookup"><span data-stu-id="dba14-186">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="dba14-187">La figura seguente mostra l'app:</span><span class="sxs-lookup"><span data-stu-id="dba14-187">The following image shows the app:</span></span>

  ![Pagina Home o di indice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="dba14-189">Nella parte seguente di questa esercitazione vengono fornite informazioni su MVC e istruzioni per iniziare a creare codice.</span><span class="sxs-lookup"><span data-stu-id="dba14-189">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="dba14-190">Avanti</span><span class="sxs-lookup"><span data-stu-id="dba14-190">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="dba14-191">Questa esercitazione illustra le nozioni di base della creazione di un'app Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="dba14-191">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="dba14-192">L'app gestisce un database di titoli di film.</span><span class="sxs-lookup"><span data-stu-id="dba14-192">The app manages a database of movie titles.</span></span> <span data-ttu-id="dba14-193">Si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="dba14-193">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dba14-194">Creare un'app Web.</span><span class="sxs-lookup"><span data-stu-id="dba14-194">Create a web app.</span></span>
> * <span data-ttu-id="dba14-195">Aggiungere un modello ed eseguirne lo scaffolding.</span><span class="sxs-lookup"><span data-stu-id="dba14-195">Add and scaffold a model.</span></span>
> * <span data-ttu-id="dba14-196">Usare un database.</span><span class="sxs-lookup"><span data-stu-id="dba14-196">Work with a database.</span></span>
> * <span data-ttu-id="dba14-197">Aggiungere ricerca e convalida.</span><span class="sxs-lookup"><span data-stu-id="dba14-197">Add search and validation.</span></span>

<span data-ttu-id="dba14-198">Al termine di queste operazioni si ottiene un'app che può gestire e visualizzare dati relativi ai film.</span><span class="sxs-lookup"><span data-stu-id="dba14-198">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="dba14-199">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="dba14-199">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dba14-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dba14-200">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="dba14-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dba14-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dba14-202">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="dba14-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="dba14-203">Creare un'app Web</span><span class="sxs-lookup"><span data-stu-id="dba14-203">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dba14-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dba14-204">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dba14-205">Dal menu di Visual Studio selezionare **Crea un nuovo progetto**.</span><span class="sxs-lookup"><span data-stu-id="dba14-205">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="dba14-206">Selezionare **ASP.NET Core applicazione Web** > **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="dba14-206">Select **ASP.NET Core Web Application** > **Next**.</span></span>

![nuova applicazione Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="dba14-208">Assegnare al progetto il nome **MvcMovie** e selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="dba14-208">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="dba14-209">È importante assegnare al progetto il nome **MvcMovie**, in modo che quando si copia il codice lo spazio dei nomi corrisponda.</span><span class="sxs-lookup"><span data-stu-id="dba14-209">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![nuova applicazione Web ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="dba14-211">Selezionare **applicazione Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="dba14-211">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="dba14-212">Nelle caselle a discesa selezionare **.NET Core** e **ASP.NET Core 3,1**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="dba14-212">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

![<span data-ttu-id="dba14-213">Finestra di dialogo Nuovo progetto, .NET Core nel riquadro sinistro, Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dba14-213">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="dba14-214">Per il progetto MVC appena creato Visual Studio ha usato il modello predefinito.</span><span class="sxs-lookup"><span data-stu-id="dba14-214">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="dba14-215">Ora è possibile disporre di un'app funzionante immettendo un nome progetto e selezionando alcune opzioni.</span><span class="sxs-lookup"><span data-stu-id="dba14-215">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="dba14-216">Si tratta di un progetto iniziale di base.</span><span class="sxs-lookup"><span data-stu-id="dba14-216">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dba14-217">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dba14-217">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dba14-218">Nell'esercitazione si presuppone una familarità con Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="dba14-218">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="dba14-219">Vedere [Introduzione a VS Code](https://code.visualstudio.com/docs) e [Guida a Visual Studio Code](#visual-studio-code-help) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="dba14-219">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="dba14-220">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="dba14-220">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="dba14-221">Cambiare directory (`cd`) e passare alla cartella che conterrà il progetto.</span><span class="sxs-lookup"><span data-stu-id="dba14-221">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="dba14-222">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="dba14-222">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="dba14-223">Viene visualizzata una finestra di dialogo con le **risorse necessarie per la compilazione e il debug non è presente in ' MvcMovie '. Aggiungerli?**</span><span class="sxs-lookup"><span data-stu-id="dba14-223">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="dba14-224">Selezionare **Sì**</span><span class="sxs-lookup"><span data-stu-id="dba14-224">Select **Yes**</span></span>

  * <span data-ttu-id="dba14-225">`dotnet new mvc -o MvcMovie`: crea un nuovo progetto ASP.NET Core MVC nella cartella *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="dba14-225">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="dba14-226">`code -r MvcMovie`: Carica il file di progetto *MvcMovie. csproj* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="dba14-226">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dba14-227">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="dba14-227">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="dba14-228">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="dba14-228">Select **File** > **New Solution**.</span></span>

  ![Nuova soluzione macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="dba14-230">In Visual Studio per Mac precedente alla versione 8,6, selezionare applicazione Web **.NET Core**  >  **app**  >  **(Model-View-Controller)**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="dba14-230">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="dba14-231">Nella versione 8,6 o successive selezionare **Web e applicazione console**  >  **app**  >  **Web (Model-View-Controller)**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="dba14-231">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![selezione del modello di app Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="dba14-233">Nella finestra di dialogo **Configura nuova applicazione Web** :</span><span class="sxs-lookup"><span data-stu-id="dba14-233">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="dba14-234">Verificare che **l'autenticazione** sia impostata su **Nessuna autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="dba14-234">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="dba14-235">Se viene visualizzata un'opzione per la selezione di un **Framework di destinazione**, selezionare la versione 3. x più recente.</span><span class="sxs-lookup"><span data-stu-id="dba14-235">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="dba14-236">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="dba14-236">Select **Next**.</span></span>

* <span data-ttu-id="dba14-237">Denominare il progetto **MvcMovie**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="dba14-237">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS nome del progetto](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="dba14-239">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="dba14-239">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dba14-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dba14-240">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dba14-241">Selezionare **CTRL+F5** per eseguire l'app in modalità non di debug.</span><span class="sxs-lookup"><span data-stu-id="dba14-241">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="dba14-242">Visual Studio avvia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ed esegue l'app.</span><span class="sxs-lookup"><span data-stu-id="dba14-242">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="dba14-243">Si noti che la barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="dba14-243">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="dba14-244">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="dba14-244">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="dba14-245">Quando Visual Studio crea un progetto Web, viene usata una porta casuale per il server Web.</span><span class="sxs-lookup"><span data-stu-id="dba14-245">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="dba14-246">Se si avvia l'app con CTRL+F5 (modalità non di debug) sarà possibile apportare modifiche al codice, salvare il file, aggiornare il browser e visualizzare le modifiche del codice.</span><span class="sxs-lookup"><span data-stu-id="dba14-246">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="dba14-247">Molti sviluppatori preferiscono usare la modalità non di debug per avviare l'app rapidamente e visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="dba14-247">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="dba14-248">È possibile scegliere se avviare l'app in modalità di debug o non di debug nella voce di menu **Debug**:</span><span class="sxs-lookup"><span data-stu-id="dba14-248">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Debug](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="dba14-250">È possibile eseguire il debug dell'app toccando il pulsante **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="dba14-250">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="dba14-252">La figura seguente mostra l'app:</span><span class="sxs-lookup"><span data-stu-id="dba14-252">The following image shows the app:</span></span>

  ![Pagina Home o di indice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="dba14-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dba14-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dba14-255">Premere CTRL+F5 per l'esecuzione senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="dba14-255">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="dba14-256">Visual Studio Code avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="dba14-256">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="dba14-257">La barra degli indirizzi visualizza `localhost:port:5001` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="dba14-257">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="dba14-258">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="dba14-258">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="dba14-259">Localhost viene usato solo per le richieste web del computer locale.</span><span class="sxs-lookup"><span data-stu-id="dba14-259">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="dba14-260">Se si avvia l'app con CTRL+F5 (modalità non di debug) sarà possibile apportare modifiche al codice, salvare il file, aggiornare il browser e visualizzare le modifiche del codice.</span><span class="sxs-lookup"><span data-stu-id="dba14-260">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="dba14-261">Molti sviluppatori preferiscono usare la modalità non di debug per aggiornare la pagina e visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="dba14-261">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Pagina Home o di indice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dba14-263">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="dba14-263">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="dba14-264">Selezionare **Esegui**  >  **Avvia senza eseguire debug** per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="dba14-264">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="dba14-265">Visual Studio per Mac avvia il server [Kestrel](xref:fundamentals/servers/index#kestrel), apre un browser e naviga all'indirizzo `http://localhost:port`, dove *port* è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="dba14-265">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="dba14-266">La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="dba14-266">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="dba14-267">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="dba14-267">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="dba14-268">Quando Visual Studio crea un progetto Web, viene usata una porta casuale per il server Web.</span><span class="sxs-lookup"><span data-stu-id="dba14-268">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="dba14-269">Quando si esegue l'app verrà visualizzato un numero di porta diverso.</span><span class="sxs-lookup"><span data-stu-id="dba14-269">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="dba14-270">È possibile scegliere se avviare l'app in modalità di debug o non di dal menu **Esegui**.</span><span class="sxs-lookup"><span data-stu-id="dba14-270">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="dba14-271">La figura seguente mostra l'app:</span><span class="sxs-lookup"><span data-stu-id="dba14-271">The following image shows the app:</span></span>

  ![Pagina Home o di indice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="dba14-273">Nella parte seguente di questa esercitazione vengono fornite informazioni su MVC e istruzioni per iniziare a creare codice.</span><span class="sxs-lookup"><span data-stu-id="dba14-273">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="dba14-274">Avanti</span><span class="sxs-lookup"><span data-stu-id="dba14-274">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="dba14-275">Questa esercitazione illustra le nozioni di base della creazione di un'app Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="dba14-275">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="dba14-276">L'app gestisce un database di titoli di film.</span><span class="sxs-lookup"><span data-stu-id="dba14-276">The app manages a database of movie titles.</span></span> <span data-ttu-id="dba14-277">Si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="dba14-277">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dba14-278">Creare un'app Web.</span><span class="sxs-lookup"><span data-stu-id="dba14-278">Create a web app.</span></span>
> * <span data-ttu-id="dba14-279">Aggiungere un modello ed eseguirne lo scaffolding.</span><span class="sxs-lookup"><span data-stu-id="dba14-279">Add and scaffold a model.</span></span>
> * <span data-ttu-id="dba14-280">Usare un database.</span><span class="sxs-lookup"><span data-stu-id="dba14-280">Work with a database.</span></span>
> * <span data-ttu-id="dba14-281">Aggiungere ricerca e convalida.</span><span class="sxs-lookup"><span data-stu-id="dba14-281">Add search and validation.</span></span>

<span data-ttu-id="dba14-282">Al termine di queste operazioni si ottiene un'app che può gestire e visualizzare dati relativi ai film.</span><span class="sxs-lookup"><span data-stu-id="dba14-282">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="dba14-283">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="dba14-283">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dba14-284">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dba14-284">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="dba14-285">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dba14-285">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dba14-286">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="dba14-286">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="dba14-287">Creare un'app Web</span><span class="sxs-lookup"><span data-stu-id="dba14-287">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dba14-288">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dba14-288">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dba14-289">Dal menu di Visual Studio selezionare **Crea un nuovo progetto**.</span><span class="sxs-lookup"><span data-stu-id="dba14-289">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="dba14-290">Selezionare **Applicazione Web ASP.NET Core** e quindi selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="dba14-290">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![nuova applicazione Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="dba14-292">Assegnare al progetto il nome **MvcMovie** e selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="dba14-292">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="dba14-293">È importante assegnare al progetto il nome **MvcMovie**, in modo che quando si copia il codice lo spazio dei nomi corrisponda.</span><span class="sxs-lookup"><span data-stu-id="dba14-293">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![nuova applicazione Web ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="dba14-295">Selezionare **Applicazione Web (MVC)** e quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="dba14-295">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="dba14-296">Finestra di dialogo Nuovo progetto, .NET Core nel riquadro sinistro, Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dba14-296">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="dba14-297">Per il progetto MVC appena creato Visual Studio ha usato il modello predefinito.</span><span class="sxs-lookup"><span data-stu-id="dba14-297">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="dba14-298">Ora è possibile disporre di un'app funzionante immettendo un nome progetto e selezionando alcune opzioni.</span><span class="sxs-lookup"><span data-stu-id="dba14-298">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="dba14-299">Si tratta di un progetto iniziale di base che rappresenta un ottimo punto di partenza.</span><span class="sxs-lookup"><span data-stu-id="dba14-299">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dba14-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dba14-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dba14-301">Nell'esercitazione si presuppone una familarità con Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="dba14-301">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="dba14-302">Vedere [Introduzione a VS Code](https://code.visualstudio.com/docs) e [Guida a Visual Studio Code](#visual-studio-code-help) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="dba14-302">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="dba14-303">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="dba14-303">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="dba14-304">Cambiare directory (`cd`) e passare alla cartella che conterrà il progetto.</span><span class="sxs-lookup"><span data-stu-id="dba14-304">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="dba14-305">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="dba14-305">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="dba14-306">Viene visualizzata una finestra di dialogo con le **risorse necessarie per la compilazione e il debug non è presente in ' MvcMovie '. Aggiungerli?**</span><span class="sxs-lookup"><span data-stu-id="dba14-306">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="dba14-307">Selezionare **Sì**</span><span class="sxs-lookup"><span data-stu-id="dba14-307">Select **Yes**</span></span>

  * <span data-ttu-id="dba14-308">`dotnet new mvc -o MvcMovie`: crea un nuovo progetto ASP.NET Core MVC nella cartella *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="dba14-308">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="dba14-309">`code -r MvcMovie`: Carica il file di progetto *MvcMovie. csproj* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="dba14-309">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dba14-310">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="dba14-310">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="dba14-311">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="dba14-311">Select **File** > **New Solution**.</span></span>

  ![Nuova soluzione macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="dba14-313">In Visual Studio per Mac precedente alla versione 8,6, selezionare applicazione Web **.NET Core**  >  **app**  >  **(Model-View-Controller)**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="dba14-313">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="dba14-314">Nella versione 8,6 o successive selezionare **Web e applicazione console**  >  **app**  >  **Web (Model-View-Controller)**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="dba14-314">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="dba14-315">Nella finestra di dialogo **Configura nuova applicazione Web** :</span><span class="sxs-lookup"><span data-stu-id="dba14-315">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="dba14-316">Verificare che **l'autenticazione** sia impostata su **Nessuna autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="dba14-316">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="dba14-317">Se viene visualizzata un'opzione per la selezione di un **Framework di destinazione**, selezionare la versione 2. x più recente.</span><span class="sxs-lookup"><span data-stu-id="dba14-317">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="dba14-318">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="dba14-318">Select **Next**.</span></span>

* <span data-ttu-id="dba14-319">Denominare il progetto **MvcMovie**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="dba14-319">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="dba14-320">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="dba14-320">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dba14-321">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dba14-321">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dba14-322">Selezionare **CTRL+F5** per eseguire l'app in modalità non di debug.</span><span class="sxs-lookup"><span data-stu-id="dba14-322">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="dba14-323">Visual Studio avvia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ed esegue l'app.</span><span class="sxs-lookup"><span data-stu-id="dba14-323">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="dba14-324">Si noti che la barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="dba14-324">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="dba14-325">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="dba14-325">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="dba14-326">Quando Visual Studio crea un progetto Web, viene usata una porta casuale per il server Web.</span><span class="sxs-lookup"><span data-stu-id="dba14-326">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="dba14-327">Se si avvia l'app con CTRL+F5 (modalità non di debug) sarà possibile apportare modifiche al codice, salvare il file, aggiornare il browser e visualizzare le modifiche del codice.</span><span class="sxs-lookup"><span data-stu-id="dba14-327">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="dba14-328">Molti sviluppatori preferiscono usare la modalità non di debug per avviare l'app rapidamente e visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="dba14-328">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="dba14-329">È possibile scegliere se avviare l'app in modalità di debug o non di debug nella voce di menu **Debug**:</span><span class="sxs-lookup"><span data-stu-id="dba14-329">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Debug](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="dba14-331">È possibile eseguire il debug dell'app toccando il pulsante **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="dba14-331">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="dba14-333">Selezionare **Accetto** per acconsentire al rilevamento.</span><span class="sxs-lookup"><span data-stu-id="dba14-333">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="dba14-334">Questa app non tiene traccia delle informazioni personali.</span><span class="sxs-lookup"><span data-stu-id="dba14-334">This app doesn't track personal information.</span></span> <span data-ttu-id="dba14-335">Il codice generato del modello include asset che consentono di soddisfare il [Regolamento generale sulla protezione dei dati (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="dba14-335">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Pagina Home o di indice](start-mvc/_static/privacy.png)

  <span data-ttu-id="dba14-337">La figura seguente illustra l'app dopo aver accettato il rilevamento:</span><span class="sxs-lookup"><span data-stu-id="dba14-337">The following image shows the app after accepting tracking:</span></span>

  ![Pagina Home o di indice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="dba14-339">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dba14-339">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dba14-340">Premere CTRL+F5 per l'esecuzione senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="dba14-340">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="dba14-341">Visual Studio Code avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="dba14-341">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="dba14-342">La barra degli indirizzi visualizza `localhost:port:5001` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="dba14-342">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="dba14-343">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="dba14-343">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="dba14-344">Localhost viene usato solo per le richieste web del computer locale.</span><span class="sxs-lookup"><span data-stu-id="dba14-344">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="dba14-345">Se si avvia l'app con CTRL+F5 (modalità non di debug) sarà possibile apportare modifiche al codice, salvare il file, aggiornare il browser e visualizzare le modifiche del codice.</span><span class="sxs-lookup"><span data-stu-id="dba14-345">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="dba14-346">Molti sviluppatori preferiscono usare la modalità non di debug per aggiornare la pagina e visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="dba14-346">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="dba14-347">Selezionare **Accetto** per acconsentire al rilevamento.</span><span class="sxs-lookup"><span data-stu-id="dba14-347">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="dba14-348">Questa app non tiene traccia delle informazioni personali.</span><span class="sxs-lookup"><span data-stu-id="dba14-348">This app doesn't track personal information.</span></span> <span data-ttu-id="dba14-349">Il codice generato del modello include asset che consentono di soddisfare il [Regolamento generale sulla protezione dei dati (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="dba14-349">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Pagina Home o di indice](start-mvc/_static/privacy.png)

  <span data-ttu-id="dba14-351">La figura seguente illustra l'app dopo aver accettato il rilevamento:</span><span class="sxs-lookup"><span data-stu-id="dba14-351">The following image shows the app after accepting tracking:</span></span>

  ![Pagina Home o di indice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dba14-353">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="dba14-353">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="dba14-354">Selezionare **Esegui**  >  **Avvia senza eseguire debug** per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="dba14-354">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="dba14-355">Visual Studio per Mac avvia il server [Kestrel](xref:fundamentals/servers/index#kestrel), apre un browser e naviga all'indirizzo `http://localhost:port`, dove *port* è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="dba14-355">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="dba14-356">La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="dba14-356">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="dba14-357">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="dba14-357">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="dba14-358">Quando Visual Studio crea un progetto Web, viene usata una porta casuale per il server Web.</span><span class="sxs-lookup"><span data-stu-id="dba14-358">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="dba14-359">Quando si esegue l'app verrà visualizzato un numero di porta diverso.</span><span class="sxs-lookup"><span data-stu-id="dba14-359">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="dba14-360">È possibile scegliere se avviare l'app in modalità di debug o non di dal menu **Esegui**.</span><span class="sxs-lookup"><span data-stu-id="dba14-360">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="dba14-361">Selezionare **Accetto** per acconsentire al rilevamento.</span><span class="sxs-lookup"><span data-stu-id="dba14-361">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="dba14-362">Questa app non tiene traccia delle informazioni personali.</span><span class="sxs-lookup"><span data-stu-id="dba14-362">This app doesn't track personal information.</span></span> <span data-ttu-id="dba14-363">Il codice generato del modello include asset che consentono di soddisfare il [Regolamento generale sulla protezione dei dati (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="dba14-363">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Pagina Home o di indice](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="dba14-365">La figura seguente illustra l'app dopo aver accettato il rilevamento:</span><span class="sxs-lookup"><span data-stu-id="dba14-365">The following image shows the app after accepting tracking:</span></span>

  ![Pagina Home o di indice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="dba14-367">Nella parte seguente di questa esercitazione vengono fornite informazioni su MVC e istruzioni per iniziare a creare codice.</span><span class="sxs-lookup"><span data-stu-id="dba14-367">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="dba14-368">Avanti</span><span class="sxs-lookup"><span data-stu-id="dba14-368">Next</span></span>](adding-controller.md)

::: moniker-end
