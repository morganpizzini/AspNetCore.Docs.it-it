---
title: Introduzione ad ASP.NET Core MVC
author: rick-anderson
description: Informazioni introduttive su ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
no-loc:
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
ms.openlocfilehash: 177112106d143a6826c1f927aac807da0aa9f2b4
ms.sourcegitcommit: ecae2aa432628b9181d1fa11037c231c7dd56c9e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113829"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="fefe9-103">Introduzione ad ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="fefe9-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="fefe9-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fefe9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="fefe9-105">Questa esercitazione illustra le nozioni di base della creazione di un'app Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="fefe9-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="fefe9-106">L'app gestisce un database di titoli di film.</span><span class="sxs-lookup"><span data-stu-id="fefe9-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="fefe9-107">Si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="fefe9-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fefe9-108">Creare un'app Web.</span><span class="sxs-lookup"><span data-stu-id="fefe9-108">Create a web app.</span></span>
> * <span data-ttu-id="fefe9-109">Aggiungere un modello ed eseguirne lo scaffolding.</span><span class="sxs-lookup"><span data-stu-id="fefe9-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="fefe9-110">Usare un database.</span><span class="sxs-lookup"><span data-stu-id="fefe9-110">Work with a database.</span></span>
> * <span data-ttu-id="fefe9-111">Aggiungere ricerca e convalida.</span><span class="sxs-lookup"><span data-stu-id="fefe9-111">Add search and validation.</span></span>

<span data-ttu-id="fefe9-112">Al termine di queste operazioni si ottiene un'app che può gestire e visualizzare dati relativi ai film.</span><span class="sxs-lookup"><span data-stu-id="fefe9-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="fefe9-113">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="fefe9-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fefe9-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fefe9-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="fefe9-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fefe9-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fefe9-116">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="fefe9-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="fefe9-117">Creare un'app Web</span><span class="sxs-lookup"><span data-stu-id="fefe9-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fefe9-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fefe9-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fefe9-119">Dal menu di Visual Studio selezionare **Crea un nuovo progetto**.</span><span class="sxs-lookup"><span data-stu-id="fefe9-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="fefe9-120">Selezionare **ASP.NET Core applicazione Web** > **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="fefe9-120">Select **ASP.NET Core Web Application** > **Next**.</span></span>

![nuova applicazione Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="fefe9-122">Assegnare al progetto il nome **MvcMovie** e selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="fefe9-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="fefe9-123">È importante assegnare al progetto il nome **MvcMovie**, in modo che quando si copia il codice lo spazio dei nomi corrisponda.</span><span class="sxs-lookup"><span data-stu-id="fefe9-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![nuova applicazione Web ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="fefe9-125">Selezionare **applicazione Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="fefe9-125">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="fefe9-126">Nelle caselle a discesa selezionare **.NET Core** e **ASP.NET Core 3,1**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="fefe9-126">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

![<span data-ttu-id="fefe9-127">Finestra di dialogo Nuovo progetto, .NET Core nel riquadro sinistro, Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fefe9-127">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="fefe9-128">Per il progetto MVC appena creato Visual Studio ha usato il modello predefinito.</span><span class="sxs-lookup"><span data-stu-id="fefe9-128">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="fefe9-129">Ora è possibile disporre di un'app funzionante immettendo un nome progetto e selezionando alcune opzioni.</span><span class="sxs-lookup"><span data-stu-id="fefe9-129">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="fefe9-130">Si tratta di un progetto iniziale di base.</span><span class="sxs-lookup"><span data-stu-id="fefe9-130">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fefe9-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fefe9-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="fefe9-132">Nell'esercitazione si presuppone una familarità con Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="fefe9-132">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="fefe9-133">Vedere [Introduzione a VS Code](https://code.visualstudio.com/docs) e [Guida a Visual Studio Code](#visual-studio-code-help) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="fefe9-133">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="fefe9-134">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="fefe9-134">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="fefe9-135">Cambiare directory (`cd`) e passare alla cartella che conterrà il progetto.</span><span class="sxs-lookup"><span data-stu-id="fefe9-135">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="fefe9-136">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="fefe9-136">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="fefe9-137">Viene visualizzata una finestra di dialogo con le **risorse necessarie per la compilazione e il debug non è presente in ' MvcMovie '. Aggiungerli?**</span><span class="sxs-lookup"><span data-stu-id="fefe9-137">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="fefe9-138">Selezionare **Sì**</span><span class="sxs-lookup"><span data-stu-id="fefe9-138">Select **Yes**</span></span>

  * <span data-ttu-id="fefe9-139">`dotnet new mvc -o MvcMovie`: crea un nuovo progetto ASP.NET Core MVC nella cartella *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="fefe9-139">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="fefe9-140">`code -r MvcMovie`: Carica il file di progetto *MvcMovie. csproj* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="fefe9-140">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fefe9-141">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="fefe9-141">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fefe9-142">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="fefe9-142">Select **File** > **New Solution**.</span></span>

  ![Nuova soluzione macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="fefe9-144">In Visual Studio per Mac precedente alla versione 8,6, selezionare applicazione Web **.NET Core**  >  **app**  >  **(Model-View-Controller)**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="fefe9-144">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="fefe9-145">Nella versione 8,6 o successive selezionare **Web e applicazione console**  >  **app**  >  **Web (Model-View-Controller)**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="fefe9-145">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![selezione del modello di app Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="fefe9-147">Nella finestra di dialogo **Configura nuova applicazione Web** :</span><span class="sxs-lookup"><span data-stu-id="fefe9-147">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="fefe9-148">Verificare che **l'autenticazione** sia impostata su **Nessuna autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="fefe9-148">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="fefe9-149">Se viene visualizzata un'opzione per la selezione di un **Framework di destinazione**, selezionare la versione 3. x più recente.</span><span class="sxs-lookup"><span data-stu-id="fefe9-149">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="fefe9-150">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="fefe9-150">Select **Next**.</span></span>

* <span data-ttu-id="fefe9-151">Denominare il progetto **MvcMovie**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="fefe9-151">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS nome del progetto](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="fefe9-153">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="fefe9-153">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fefe9-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fefe9-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fefe9-155">Selezionare **CTRL+F5** per eseguire l'app in modalità non di debug.</span><span class="sxs-lookup"><span data-stu-id="fefe9-155">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="fefe9-156">Visual Studio avvia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ed esegue l'app.</span><span class="sxs-lookup"><span data-stu-id="fefe9-156">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="fefe9-157">Si noti che la barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="fefe9-157">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="fefe9-158">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="fefe9-158">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="fefe9-159">Quando Visual Studio crea un progetto Web, viene usata una porta casuale per il server Web.</span><span class="sxs-lookup"><span data-stu-id="fefe9-159">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="fefe9-160">Se si avvia l'app con CTRL+F5 (modalità non di debug) sarà possibile apportare modifiche al codice, salvare il file, aggiornare il browser e visualizzare le modifiche del codice.</span><span class="sxs-lookup"><span data-stu-id="fefe9-160">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="fefe9-161">Molti sviluppatori preferiscono usare la modalità non di debug per avviare l'app rapidamente e visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="fefe9-161">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="fefe9-162">È possibile scegliere se avviare l'app in modalità di debug o non di debug nella voce di menu **Debug**:</span><span class="sxs-lookup"><span data-stu-id="fefe9-162">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Debug](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="fefe9-164">È possibile eseguire il debug dell'app toccando il pulsante **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="fefe9-164">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="fefe9-166">La figura seguente mostra l'app:</span><span class="sxs-lookup"><span data-stu-id="fefe9-166">The following image shows the app:</span></span>

  ![Pagina Home o di indice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="fefe9-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fefe9-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="fefe9-169">Premere CTRL+F5 per l'esecuzione senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="fefe9-169">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="fefe9-170">Visual Studio Code avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="fefe9-170">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="fefe9-171">La barra degli indirizzi visualizza `localhost:port:5001` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="fefe9-171">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="fefe9-172">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="fefe9-172">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="fefe9-173">Localhost viene usato solo per le richieste web del computer locale.</span><span class="sxs-lookup"><span data-stu-id="fefe9-173">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="fefe9-174">Se si avvia l'app con CTRL+F5 (modalità non di debug) sarà possibile apportare modifiche al codice, salvare il file, aggiornare il browser e visualizzare le modifiche del codice.</span><span class="sxs-lookup"><span data-stu-id="fefe9-174">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="fefe9-175">Molti sviluppatori preferiscono usare la modalità non di debug per aggiornare la pagina e visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="fefe9-175">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Pagina Home o di indice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fefe9-177">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="fefe9-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fefe9-178">Selezionare **Esegui**  >  **Avvia senza eseguire debug** per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="fefe9-178">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="fefe9-179">Visual Studio per Mac avvia il server [Kestrel](xref:fundamentals/servers/index#kestrel), apre un browser e naviga all'indirizzo `http://localhost:port`, dove *port* è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="fefe9-179">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="fefe9-180">La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="fefe9-180">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="fefe9-181">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="fefe9-181">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="fefe9-182">Quando Visual Studio crea un progetto Web, viene usata una porta casuale per il server Web.</span><span class="sxs-lookup"><span data-stu-id="fefe9-182">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="fefe9-183">Quando si esegue l'app verrà visualizzato un numero di porta diverso.</span><span class="sxs-lookup"><span data-stu-id="fefe9-183">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="fefe9-184">È possibile scegliere se avviare l'app in modalità di debug o non di dal menu **Esegui**.</span><span class="sxs-lookup"><span data-stu-id="fefe9-184">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="fefe9-185">La figura seguente mostra l'app:</span><span class="sxs-lookup"><span data-stu-id="fefe9-185">The following image shows the app:</span></span>

  ![Pagina Home o di indice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="fefe9-187">Nella parte seguente di questa esercitazione vengono fornite informazioni su MVC e istruzioni per iniziare a creare codice.</span><span class="sxs-lookup"><span data-stu-id="fefe9-187">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="fefe9-188">Avanti</span><span class="sxs-lookup"><span data-stu-id="fefe9-188">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="fefe9-189">Questa esercitazione illustra le nozioni di base della creazione di un'app Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="fefe9-189">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="fefe9-190">L'app gestisce un database di titoli di film.</span><span class="sxs-lookup"><span data-stu-id="fefe9-190">The app manages a database of movie titles.</span></span> <span data-ttu-id="fefe9-191">Si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="fefe9-191">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fefe9-192">Creare un'app Web.</span><span class="sxs-lookup"><span data-stu-id="fefe9-192">Create a web app.</span></span>
> * <span data-ttu-id="fefe9-193">Aggiungere un modello ed eseguirne lo scaffolding.</span><span class="sxs-lookup"><span data-stu-id="fefe9-193">Add and scaffold a model.</span></span>
> * <span data-ttu-id="fefe9-194">Usare un database.</span><span class="sxs-lookup"><span data-stu-id="fefe9-194">Work with a database.</span></span>
> * <span data-ttu-id="fefe9-195">Aggiungere ricerca e convalida.</span><span class="sxs-lookup"><span data-stu-id="fefe9-195">Add search and validation.</span></span>

<span data-ttu-id="fefe9-196">Al termine di queste operazioni si ottiene un'app che può gestire e visualizzare dati relativi ai film.</span><span class="sxs-lookup"><span data-stu-id="fefe9-196">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="fefe9-197">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="fefe9-197">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fefe9-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fefe9-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="fefe9-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fefe9-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fefe9-200">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="fefe9-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="fefe9-201">Creare un'app Web</span><span class="sxs-lookup"><span data-stu-id="fefe9-201">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fefe9-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fefe9-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fefe9-203">Dal menu di Visual Studio selezionare **Crea un nuovo progetto**.</span><span class="sxs-lookup"><span data-stu-id="fefe9-203">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="fefe9-204">Selezionare **Applicazione Web ASP.NET Core** e quindi selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="fefe9-204">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![nuova applicazione Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="fefe9-206">Assegnare al progetto il nome **MvcMovie** e selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="fefe9-206">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="fefe9-207">È importante assegnare al progetto il nome **MvcMovie**, in modo che quando si copia il codice lo spazio dei nomi corrisponda.</span><span class="sxs-lookup"><span data-stu-id="fefe9-207">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![nuova applicazione Web ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="fefe9-209">Selezionare **Applicazione Web (MVC)** e quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="fefe9-209">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="fefe9-210">Finestra di dialogo Nuovo progetto, .NET Core nel riquadro sinistro, Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fefe9-210">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="fefe9-211">Per il progetto MVC appena creato Visual Studio ha usato il modello predefinito.</span><span class="sxs-lookup"><span data-stu-id="fefe9-211">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="fefe9-212">Ora è possibile disporre di un'app funzionante immettendo un nome progetto e selezionando alcune opzioni.</span><span class="sxs-lookup"><span data-stu-id="fefe9-212">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="fefe9-213">Si tratta di un progetto iniziale di base che rappresenta un ottimo punto di partenza.</span><span class="sxs-lookup"><span data-stu-id="fefe9-213">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fefe9-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fefe9-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="fefe9-215">Nell'esercitazione si presuppone una familarità con Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="fefe9-215">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="fefe9-216">Vedere [Introduzione a VS Code](https://code.visualstudio.com/docs) e [Guida a Visual Studio Code](#visual-studio-code-help) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="fefe9-216">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="fefe9-217">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="fefe9-217">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="fefe9-218">Cambiare directory (`cd`) e passare alla cartella che conterrà il progetto.</span><span class="sxs-lookup"><span data-stu-id="fefe9-218">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="fefe9-219">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="fefe9-219">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="fefe9-220">Viene visualizzata una finestra di dialogo con le **risorse necessarie per la compilazione e il debug non è presente in ' MvcMovie '. Aggiungerli?**</span><span class="sxs-lookup"><span data-stu-id="fefe9-220">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="fefe9-221">Selezionare **Sì**</span><span class="sxs-lookup"><span data-stu-id="fefe9-221">Select **Yes**</span></span>

  * <span data-ttu-id="fefe9-222">`dotnet new mvc -o MvcMovie`: crea un nuovo progetto ASP.NET Core MVC nella cartella *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="fefe9-222">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="fefe9-223">`code -r MvcMovie`: Carica il file di progetto *MvcMovie. csproj* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="fefe9-223">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fefe9-224">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="fefe9-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fefe9-225">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="fefe9-225">Select **File** > **New Solution**.</span></span>

  ![Nuova soluzione macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="fefe9-227">In Visual Studio per Mac precedente alla versione 8,6, selezionare applicazione Web **.NET Core**  >  **app**  >  **(Model-View-Controller)**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="fefe9-227">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="fefe9-228">Nella versione 8,6 o successive selezionare **Web e applicazione console**  >  **app**  >  **Web (Model-View-Controller)**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="fefe9-228">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="fefe9-229">Nella finestra di dialogo **Configura nuova applicazione Web** :</span><span class="sxs-lookup"><span data-stu-id="fefe9-229">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="fefe9-230">Verificare che **l'autenticazione** sia impostata su **Nessuna autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="fefe9-230">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="fefe9-231">Se viene visualizzata un'opzione per la selezione di un **Framework di destinazione**, selezionare la versione 2. x più recente.</span><span class="sxs-lookup"><span data-stu-id="fefe9-231">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="fefe9-232">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="fefe9-232">Select **Next**.</span></span>

* <span data-ttu-id="fefe9-233">Denominare il progetto **MvcMovie**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="fefe9-233">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="fefe9-234">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="fefe9-234">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fefe9-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fefe9-235">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fefe9-236">Selezionare **CTRL+F5** per eseguire l'app in modalità non di debug.</span><span class="sxs-lookup"><span data-stu-id="fefe9-236">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="fefe9-237">Visual Studio avvia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ed esegue l'app.</span><span class="sxs-lookup"><span data-stu-id="fefe9-237">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="fefe9-238">Si noti che la barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="fefe9-238">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="fefe9-239">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="fefe9-239">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="fefe9-240">Quando Visual Studio crea un progetto Web, viene usata una porta casuale per il server Web.</span><span class="sxs-lookup"><span data-stu-id="fefe9-240">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="fefe9-241">Se si avvia l'app con CTRL+F5 (modalità non di debug) sarà possibile apportare modifiche al codice, salvare il file, aggiornare il browser e visualizzare le modifiche del codice.</span><span class="sxs-lookup"><span data-stu-id="fefe9-241">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="fefe9-242">Molti sviluppatori preferiscono usare la modalità non di debug per avviare l'app rapidamente e visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="fefe9-242">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="fefe9-243">È possibile scegliere se avviare l'app in modalità di debug o non di debug nella voce di menu **Debug**:</span><span class="sxs-lookup"><span data-stu-id="fefe9-243">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Debug](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="fefe9-245">È possibile eseguire il debug dell'app toccando il pulsante **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="fefe9-245">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="fefe9-247">Selezionare **Accetto** per acconsentire al rilevamento.</span><span class="sxs-lookup"><span data-stu-id="fefe9-247">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="fefe9-248">Questa app non tiene traccia delle informazioni personali.</span><span class="sxs-lookup"><span data-stu-id="fefe9-248">This app doesn't track personal information.</span></span> <span data-ttu-id="fefe9-249">Il codice generato del modello include asset che consentono di soddisfare il [Regolamento generale sulla protezione dei dati (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="fefe9-249">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Pagina Home o di indice](start-mvc/_static/privacy.png)

  <span data-ttu-id="fefe9-251">La figura seguente illustra l'app dopo aver accettato il rilevamento:</span><span class="sxs-lookup"><span data-stu-id="fefe9-251">The following image shows the app after accepting tracking:</span></span>

  ![Pagina Home o di indice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="fefe9-253">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fefe9-253">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="fefe9-254">Premere CTRL+F5 per l'esecuzione senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="fefe9-254">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="fefe9-255">Visual Studio Code avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="fefe9-255">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="fefe9-256">La barra degli indirizzi visualizza `localhost:port:5001` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="fefe9-256">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="fefe9-257">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="fefe9-257">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="fefe9-258">Localhost viene usato solo per le richieste web del computer locale.</span><span class="sxs-lookup"><span data-stu-id="fefe9-258">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="fefe9-259">Se si avvia l'app con CTRL+F5 (modalità non di debug) sarà possibile apportare modifiche al codice, salvare il file, aggiornare il browser e visualizzare le modifiche del codice.</span><span class="sxs-lookup"><span data-stu-id="fefe9-259">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="fefe9-260">Molti sviluppatori preferiscono usare la modalità non di debug per aggiornare la pagina e visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="fefe9-260">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="fefe9-261">Selezionare **Accetto** per acconsentire al rilevamento.</span><span class="sxs-lookup"><span data-stu-id="fefe9-261">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="fefe9-262">Questa app non tiene traccia delle informazioni personali.</span><span class="sxs-lookup"><span data-stu-id="fefe9-262">This app doesn't track personal information.</span></span> <span data-ttu-id="fefe9-263">Il codice generato del modello include asset che consentono di soddisfare il [Regolamento generale sulla protezione dei dati (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="fefe9-263">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Pagina Home o di indice](start-mvc/_static/privacy.png)

  <span data-ttu-id="fefe9-265">La figura seguente illustra l'app dopo aver accettato il rilevamento:</span><span class="sxs-lookup"><span data-stu-id="fefe9-265">The following image shows the app after accepting tracking:</span></span>

  ![Pagina Home o di indice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fefe9-267">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="fefe9-267">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fefe9-268">Selezionare **Esegui**  >  **Avvia senza eseguire debug** per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="fefe9-268">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="fefe9-269">Visual Studio per Mac avvia il server [Kestrel](xref:fundamentals/servers/index#kestrel), apre un browser e naviga all'indirizzo `http://localhost:port`, dove *port* è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="fefe9-269">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="fefe9-270">La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="fefe9-270">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="fefe9-271">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="fefe9-271">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="fefe9-272">Quando Visual Studio crea un progetto Web, viene usata una porta casuale per il server Web.</span><span class="sxs-lookup"><span data-stu-id="fefe9-272">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="fefe9-273">Quando si esegue l'app verrà visualizzato un numero di porta diverso.</span><span class="sxs-lookup"><span data-stu-id="fefe9-273">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="fefe9-274">È possibile scegliere se avviare l'app in modalità di debug o non di dal menu **Esegui**.</span><span class="sxs-lookup"><span data-stu-id="fefe9-274">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="fefe9-275">Selezionare **Accetto** per acconsentire al rilevamento.</span><span class="sxs-lookup"><span data-stu-id="fefe9-275">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="fefe9-276">Questa app non tiene traccia delle informazioni personali.</span><span class="sxs-lookup"><span data-stu-id="fefe9-276">This app doesn't track personal information.</span></span> <span data-ttu-id="fefe9-277">Il codice generato del modello include asset che consentono di soddisfare il [Regolamento generale sulla protezione dei dati (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="fefe9-277">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Pagina Home o di indice](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="fefe9-279">La figura seguente illustra l'app dopo aver accettato il rilevamento:</span><span class="sxs-lookup"><span data-stu-id="fefe9-279">The following image shows the app after accepting tracking:</span></span>

  ![Pagina Home o di indice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="fefe9-281">Nella parte seguente di questa esercitazione vengono fornite informazioni su MVC e istruzioni per iniziare a creare codice.</span><span class="sxs-lookup"><span data-stu-id="fefe9-281">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="fefe9-282">Avanti</span><span class="sxs-lookup"><span data-stu-id="fefe9-282">Next</span></span>](adding-controller.md)

::: moniker-end
