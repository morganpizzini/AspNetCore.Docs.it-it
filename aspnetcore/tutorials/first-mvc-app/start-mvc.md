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
ms.openlocfilehash: 9d70b292a93a5d19cc25b2fc592ec88ce8262434
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629990"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="3c425-103">Introduzione ad ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="3c425-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="3c425-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3c425-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="3c425-105">Questa esercitazione illustra le nozioni di base della creazione di un'app Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="3c425-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="3c425-106">L'app gestisce un database di titoli di film.</span><span class="sxs-lookup"><span data-stu-id="3c425-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="3c425-107">Si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="3c425-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3c425-108">Creare un'app Web.</span><span class="sxs-lookup"><span data-stu-id="3c425-108">Create a web app.</span></span>
> * <span data-ttu-id="3c425-109">Aggiungere un modello ed eseguirne lo scaffolding.</span><span class="sxs-lookup"><span data-stu-id="3c425-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="3c425-110">Usare un database.</span><span class="sxs-lookup"><span data-stu-id="3c425-110">Work with a database.</span></span>
> * <span data-ttu-id="3c425-111">Aggiungere ricerca e convalida.</span><span class="sxs-lookup"><span data-stu-id="3c425-111">Add search and validation.</span></span>

<span data-ttu-id="3c425-112">Al termine di queste operazioni si ottiene un'app che può gestire e visualizzare dati relativi ai film.</span><span class="sxs-lookup"><span data-stu-id="3c425-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="3c425-113">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="3c425-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c425-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c425-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c425-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c425-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c425-116">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="3c425-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="3c425-117">Creare un'app Web</span><span class="sxs-lookup"><span data-stu-id="3c425-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c425-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c425-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3c425-119">Dal menu di Visual Studio selezionare **Crea un nuovo progetto**.</span><span class="sxs-lookup"><span data-stu-id="3c425-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="3c425-120">Selezionare **Applicazione Web ASP.NET Core** e quindi selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="3c425-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![nuova applicazione Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="3c425-122">Assegnare al progetto il nome **MvcMovie** e selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="3c425-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="3c425-123">È importante assegnare al progetto il nome **MvcMovie**, in modo che quando si copia il codice lo spazio dei nomi corrisponda.</span><span class="sxs-lookup"><span data-stu-id="3c425-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![nuova applicazione Web ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="3c425-125">Selezionare **Applicazione Web (MVC)** e quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="3c425-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="3c425-126">Finestra di dialogo Nuovo progetto, .NET Core nel riquadro sinistro, Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3c425-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="3c425-127">Per il progetto MVC appena creato Visual Studio ha usato il modello predefinito.</span><span class="sxs-lookup"><span data-stu-id="3c425-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="3c425-128">Ora è possibile disporre di un'app funzionante immettendo un nome progetto e selezionando alcune opzioni.</span><span class="sxs-lookup"><span data-stu-id="3c425-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="3c425-129">Si tratta di un progetto iniziale di base.</span><span class="sxs-lookup"><span data-stu-id="3c425-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c425-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c425-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3c425-131">Nell'esercitazione si presuppone una familarità con Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3c425-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="3c425-132">Vedere [Introduzione a VS Code](https://code.visualstudio.com/docs) e [Guida a Visual Studio Code](#visual-studio-code-help) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="3c425-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="3c425-133">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="3c425-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="3c425-134">Cambiare directory (`cd`) e passare alla cartella che conterrà il progetto.</span><span class="sxs-lookup"><span data-stu-id="3c425-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="3c425-135">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="3c425-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="3c425-136">Viene visualizzata una finestra di dialogo con le **risorse necessarie per la compilazione e il debug non è presente in ' MvcMovie '. Aggiungerli?**</span><span class="sxs-lookup"><span data-stu-id="3c425-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="3c425-137">Selezionare **Sì**</span><span class="sxs-lookup"><span data-stu-id="3c425-137">Select **Yes**</span></span>

  * <span data-ttu-id="3c425-138">`dotnet new mvc -o MvcMovie`: crea un nuovo progetto ASP.NET Core MVC nella cartella *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="3c425-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="3c425-139">`code -r MvcMovie`: Carica il file di progetto *MvcMovie. csproj* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3c425-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c425-140">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="3c425-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3c425-141">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="3c425-141">Select **File** > **New Solution**.</span></span>

  ![Nuova soluzione macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="3c425-143">In Visual Studio per Mac precedente alla versione 8,6, selezionare applicazione Web **.NET Core**  >  **app**  >  **(Model-View-Controller)**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="3c425-143">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="3c425-144">Nella versione 8,6 o successive selezionare **Web e applicazione console**  >  **app**  >  **Web (Model-View-Controller)**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="3c425-144">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![selezione del modello di app Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="3c425-146">Nella finestra di dialogo **Configura nuova applicazione Web** :</span><span class="sxs-lookup"><span data-stu-id="3c425-146">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="3c425-147">Verificare che **l'autenticazione** sia impostata su **Nessuna autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="3c425-147">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="3c425-148">Se viene visualizzata un'opzione per la selezione di un **Framework di destinazione**, selezionare la versione 3. x più recente.</span><span class="sxs-lookup"><span data-stu-id="3c425-148">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="3c425-149">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="3c425-149">Select **Next**.</span></span>

* <span data-ttu-id="3c425-150">Denominare il progetto **MvcMovie**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="3c425-150">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS nome del progetto](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="3c425-152">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="3c425-152">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c425-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c425-153">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3c425-154">Selezionare **CTRL+F5** per eseguire l'app in modalità non di debug.</span><span class="sxs-lookup"><span data-stu-id="3c425-154">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="3c425-155">Visual Studio avvia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ed esegue l'app.</span><span class="sxs-lookup"><span data-stu-id="3c425-155">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="3c425-156">Si noti che la barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="3c425-156">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="3c425-157">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="3c425-157">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="3c425-158">Quando Visual Studio crea un progetto Web, viene usata una porta casuale per il server Web.</span><span class="sxs-lookup"><span data-stu-id="3c425-158">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="3c425-159">Se si avvia l'app con CTRL+F5 (modalità non di debug) sarà possibile apportare modifiche al codice, salvare il file, aggiornare il browser e visualizzare le modifiche del codice.</span><span class="sxs-lookup"><span data-stu-id="3c425-159">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="3c425-160">Molti sviluppatori preferiscono usare la modalità non di debug per avviare l'app rapidamente e visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="3c425-160">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="3c425-161">È possibile scegliere se avviare l'app in modalità di debug o non di debug nella voce di menu **Debug**:</span><span class="sxs-lookup"><span data-stu-id="3c425-161">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Debug](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="3c425-163">È possibile eseguire il debug dell'app toccando il pulsante **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="3c425-163">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="3c425-165">La figura seguente mostra l'app:</span><span class="sxs-lookup"><span data-stu-id="3c425-165">The following image shows the app:</span></span>

  ![Pagina Home o di indice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c425-167">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c425-167">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3c425-168">Premere CTRL+F5 per l'esecuzione senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="3c425-168">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="3c425-169">Visual Studio Code avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="3c425-169">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="3c425-170">La barra degli indirizzi visualizza `localhost:port:5001` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="3c425-170">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="3c425-171">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="3c425-171">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="3c425-172">Localhost viene usato solo per le richieste web del computer locale.</span><span class="sxs-lookup"><span data-stu-id="3c425-172">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="3c425-173">Se si avvia l'app con CTRL+F5 (modalità non di debug) sarà possibile apportare modifiche al codice, salvare il file, aggiornare il browser e visualizzare le modifiche del codice.</span><span class="sxs-lookup"><span data-stu-id="3c425-173">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="3c425-174">Molti sviluppatori preferiscono usare la modalità non di debug per aggiornare la pagina e visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="3c425-174">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Pagina Home o di indice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c425-176">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="3c425-176">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3c425-177">Selezionare **Esegui**  >  **Avvia senza eseguire debug** per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="3c425-177">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="3c425-178">Visual Studio per Mac avvia il server [Kestrel](xref:fundamentals/servers/index#kestrel), apre un browser e naviga all'indirizzo `http://localhost:port`, dove *port* è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="3c425-178">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="3c425-179">La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="3c425-179">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="3c425-180">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="3c425-180">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="3c425-181">Quando Visual Studio crea un progetto Web, viene usata una porta casuale per il server Web.</span><span class="sxs-lookup"><span data-stu-id="3c425-181">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="3c425-182">Quando si esegue l'app verrà visualizzato un numero di porta diverso.</span><span class="sxs-lookup"><span data-stu-id="3c425-182">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="3c425-183">È possibile scegliere se avviare l'app in modalità di debug o non di dal menu **Esegui**.</span><span class="sxs-lookup"><span data-stu-id="3c425-183">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="3c425-184">La figura seguente mostra l'app:</span><span class="sxs-lookup"><span data-stu-id="3c425-184">The following image shows the app:</span></span>

  ![Pagina Home o di indice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="3c425-186">Nella parte seguente di questa esercitazione vengono fornite informazioni su MVC e istruzioni per iniziare a creare codice.</span><span class="sxs-lookup"><span data-stu-id="3c425-186">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="3c425-187">Avanti</span><span class="sxs-lookup"><span data-stu-id="3c425-187">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="3c425-188">Questa esercitazione illustra le nozioni di base della creazione di un'app Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="3c425-188">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="3c425-189">L'app gestisce un database di titoli di film.</span><span class="sxs-lookup"><span data-stu-id="3c425-189">The app manages a database of movie titles.</span></span> <span data-ttu-id="3c425-190">Si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="3c425-190">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3c425-191">Creare un'app Web.</span><span class="sxs-lookup"><span data-stu-id="3c425-191">Create a web app.</span></span>
> * <span data-ttu-id="3c425-192">Aggiungere un modello ed eseguirne lo scaffolding.</span><span class="sxs-lookup"><span data-stu-id="3c425-192">Add and scaffold a model.</span></span>
> * <span data-ttu-id="3c425-193">Usare un database.</span><span class="sxs-lookup"><span data-stu-id="3c425-193">Work with a database.</span></span>
> * <span data-ttu-id="3c425-194">Aggiungere ricerca e convalida.</span><span class="sxs-lookup"><span data-stu-id="3c425-194">Add search and validation.</span></span>

<span data-ttu-id="3c425-195">Al termine di queste operazioni si ottiene un'app che può gestire e visualizzare dati relativi ai film.</span><span class="sxs-lookup"><span data-stu-id="3c425-195">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="3c425-196">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="3c425-196">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c425-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c425-197">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c425-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c425-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c425-199">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="3c425-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="3c425-200">Creare un'app Web</span><span class="sxs-lookup"><span data-stu-id="3c425-200">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c425-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c425-201">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3c425-202">Dal menu di Visual Studio selezionare **Crea un nuovo progetto**.</span><span class="sxs-lookup"><span data-stu-id="3c425-202">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="3c425-203">Selezionare **Applicazione Web ASP.NET Core** e quindi selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="3c425-203">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![nuova applicazione Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="3c425-205">Assegnare al progetto il nome **MvcMovie** e selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="3c425-205">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="3c425-206">È importante assegnare al progetto il nome **MvcMovie**, in modo che quando si copia il codice lo spazio dei nomi corrisponda.</span><span class="sxs-lookup"><span data-stu-id="3c425-206">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![nuova applicazione Web ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="3c425-208">Selezionare **Applicazione Web (MVC)** e quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="3c425-208">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="3c425-209">Finestra di dialogo Nuovo progetto, .NET Core nel riquadro sinistro, Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3c425-209">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="3c425-210">Per il progetto MVC appena creato Visual Studio ha usato il modello predefinito.</span><span class="sxs-lookup"><span data-stu-id="3c425-210">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="3c425-211">Ora è possibile disporre di un'app funzionante immettendo un nome progetto e selezionando alcune opzioni.</span><span class="sxs-lookup"><span data-stu-id="3c425-211">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="3c425-212">Si tratta di un progetto iniziale di base che rappresenta un ottimo punto di partenza.</span><span class="sxs-lookup"><span data-stu-id="3c425-212">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c425-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c425-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3c425-214">Nell'esercitazione si presuppone una familarità con Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3c425-214">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="3c425-215">Vedere [Introduzione a VS Code](https://code.visualstudio.com/docs) e [Guida a Visual Studio Code](#visual-studio-code-help) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="3c425-215">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="3c425-216">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="3c425-216">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="3c425-217">Cambiare directory (`cd`) e passare alla cartella che conterrà il progetto.</span><span class="sxs-lookup"><span data-stu-id="3c425-217">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="3c425-218">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="3c425-218">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="3c425-219">Viene visualizzata una finestra di dialogo con le **risorse necessarie per la compilazione e il debug non è presente in ' MvcMovie '. Aggiungerli?**</span><span class="sxs-lookup"><span data-stu-id="3c425-219">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="3c425-220">Selezionare **Sì**</span><span class="sxs-lookup"><span data-stu-id="3c425-220">Select **Yes**</span></span>

  * <span data-ttu-id="3c425-221">`dotnet new mvc -o MvcMovie`: crea un nuovo progetto ASP.NET Core MVC nella cartella *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="3c425-221">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="3c425-222">`code -r MvcMovie`: Carica il file di progetto *MvcMovie. csproj* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3c425-222">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c425-223">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="3c425-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3c425-224">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="3c425-224">Select **File** > **New Solution**.</span></span>

  ![Nuova soluzione macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="3c425-226">In Visual Studio per Mac precedente alla versione 8,6, selezionare applicazione Web **.NET Core**  >  **app**  >  **(Model-View-Controller)**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="3c425-226">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="3c425-227">Nella versione 8,6 o successive selezionare **Web e applicazione console**  >  **app**  >  **Web (Model-View-Controller)**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="3c425-227">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="3c425-228">Nella finestra di dialogo **Configura nuova applicazione Web** :</span><span class="sxs-lookup"><span data-stu-id="3c425-228">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="3c425-229">Verificare che **l'autenticazione** sia impostata su **Nessuna autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="3c425-229">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="3c425-230">Se viene visualizzata un'opzione per la selezione di un **Framework di destinazione**, selezionare la versione 2. x più recente.</span><span class="sxs-lookup"><span data-stu-id="3c425-230">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="3c425-231">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="3c425-231">Select **Next**.</span></span>

* <span data-ttu-id="3c425-232">Denominare il progetto **MvcMovie**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="3c425-232">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="3c425-233">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="3c425-233">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c425-234">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c425-234">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3c425-235">Selezionare **CTRL+F5** per eseguire l'app in modalità non di debug.</span><span class="sxs-lookup"><span data-stu-id="3c425-235">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="3c425-236">Visual Studio avvia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ed esegue l'app.</span><span class="sxs-lookup"><span data-stu-id="3c425-236">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="3c425-237">Si noti che la barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="3c425-237">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="3c425-238">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="3c425-238">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="3c425-239">Quando Visual Studio crea un progetto Web, viene usata una porta casuale per il server Web.</span><span class="sxs-lookup"><span data-stu-id="3c425-239">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="3c425-240">Se si avvia l'app con CTRL+F5 (modalità non di debug) sarà possibile apportare modifiche al codice, salvare il file, aggiornare il browser e visualizzare le modifiche del codice.</span><span class="sxs-lookup"><span data-stu-id="3c425-240">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="3c425-241">Molti sviluppatori preferiscono usare la modalità non di debug per avviare l'app rapidamente e visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="3c425-241">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="3c425-242">È possibile scegliere se avviare l'app in modalità di debug o non di debug nella voce di menu **Debug**:</span><span class="sxs-lookup"><span data-stu-id="3c425-242">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Debug](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="3c425-244">È possibile eseguire il debug dell'app toccando il pulsante **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="3c425-244">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="3c425-246">Selezionare **Accetto** per acconsentire al rilevamento.</span><span class="sxs-lookup"><span data-stu-id="3c425-246">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="3c425-247">Questa app non tiene traccia delle informazioni personali.</span><span class="sxs-lookup"><span data-stu-id="3c425-247">This app doesn't track personal information.</span></span> <span data-ttu-id="3c425-248">Il codice generato del modello include asset che consentono di soddisfare il [Regolamento generale sulla protezione dei dati (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="3c425-248">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Pagina Home o di indice](start-mvc/_static/privacy.png)

  <span data-ttu-id="3c425-250">La figura seguente illustra l'app dopo aver accettato il rilevamento:</span><span class="sxs-lookup"><span data-stu-id="3c425-250">The following image shows the app after accepting tracking:</span></span>

  ![Pagina Home o di indice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c425-252">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c425-252">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3c425-253">Premere CTRL+F5 per l'esecuzione senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="3c425-253">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="3c425-254">Visual Studio Code avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="3c425-254">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="3c425-255">La barra degli indirizzi visualizza `localhost:port:5001` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="3c425-255">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="3c425-256">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="3c425-256">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="3c425-257">Localhost viene usato solo per le richieste web del computer locale.</span><span class="sxs-lookup"><span data-stu-id="3c425-257">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="3c425-258">Se si avvia l'app con CTRL+F5 (modalità non di debug) sarà possibile apportare modifiche al codice, salvare il file, aggiornare il browser e visualizzare le modifiche del codice.</span><span class="sxs-lookup"><span data-stu-id="3c425-258">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="3c425-259">Molti sviluppatori preferiscono usare la modalità non di debug per aggiornare la pagina e visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="3c425-259">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="3c425-260">Selezionare **Accetto** per acconsentire al rilevamento.</span><span class="sxs-lookup"><span data-stu-id="3c425-260">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="3c425-261">Questa app non tiene traccia delle informazioni personali.</span><span class="sxs-lookup"><span data-stu-id="3c425-261">This app doesn't track personal information.</span></span> <span data-ttu-id="3c425-262">Il codice generato del modello include asset che consentono di soddisfare il [Regolamento generale sulla protezione dei dati (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="3c425-262">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Pagina Home o di indice](start-mvc/_static/privacy.png)

  <span data-ttu-id="3c425-264">La figura seguente illustra l'app dopo aver accettato il rilevamento:</span><span class="sxs-lookup"><span data-stu-id="3c425-264">The following image shows the app after accepting tracking:</span></span>

  ![Pagina Home o di indice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c425-266">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="3c425-266">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3c425-267">Selezionare **Esegui**  >  **Avvia senza eseguire debug** per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="3c425-267">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="3c425-268">Visual Studio per Mac avvia il server [Kestrel](xref:fundamentals/servers/index#kestrel), apre un browser e naviga all'indirizzo `http://localhost:port`, dove *port* è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="3c425-268">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="3c425-269">La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="3c425-269">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="3c425-270">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="3c425-270">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="3c425-271">Quando Visual Studio crea un progetto Web, viene usata una porta casuale per il server Web.</span><span class="sxs-lookup"><span data-stu-id="3c425-271">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="3c425-272">Quando si esegue l'app verrà visualizzato un numero di porta diverso.</span><span class="sxs-lookup"><span data-stu-id="3c425-272">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="3c425-273">È possibile scegliere se avviare l'app in modalità di debug o non di dal menu **Esegui**.</span><span class="sxs-lookup"><span data-stu-id="3c425-273">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="3c425-274">Selezionare **Accetto** per acconsentire al rilevamento.</span><span class="sxs-lookup"><span data-stu-id="3c425-274">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="3c425-275">Questa app non tiene traccia delle informazioni personali.</span><span class="sxs-lookup"><span data-stu-id="3c425-275">This app doesn't track personal information.</span></span> <span data-ttu-id="3c425-276">Il codice generato del modello include asset che consentono di soddisfare il [Regolamento generale sulla protezione dei dati (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="3c425-276">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Pagina Home o di indice](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="3c425-278">La figura seguente illustra l'app dopo aver accettato il rilevamento:</span><span class="sxs-lookup"><span data-stu-id="3c425-278">The following image shows the app after accepting tracking:</span></span>

  ![Pagina Home o di indice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="3c425-280">Nella parte seguente di questa esercitazione vengono fornite informazioni su MVC e istruzioni per iniziare a creare codice.</span><span class="sxs-lookup"><span data-stu-id="3c425-280">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="3c425-281">Avanti</span><span class="sxs-lookup"><span data-stu-id="3c425-281">Next</span></span>](adding-controller.md)

::: moniker-end
