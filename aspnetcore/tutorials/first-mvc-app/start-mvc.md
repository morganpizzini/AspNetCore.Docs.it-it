---
title: Introduzione ad ASP.NET Core MVC
author: rick-anderson
description: Informazioni introduttive su ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: c2b76b59ae775b9268fa77019bf8420e5e4108b6
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452286"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="6f727-103">Introduzione ad ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="6f727-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="6f727-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6f727-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="6f727-105">Questa esercitazione illustra le nozioni di base della creazione di un'app Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="6f727-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="6f727-106">L'app gestisce un database di titoli di film.</span><span class="sxs-lookup"><span data-stu-id="6f727-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="6f727-107">Si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="6f727-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6f727-108">Creare un'app Web.</span><span class="sxs-lookup"><span data-stu-id="6f727-108">Create a web app.</span></span>
> * <span data-ttu-id="6f727-109">Aggiungere un modello ed eseguirne lo scaffolding.</span><span class="sxs-lookup"><span data-stu-id="6f727-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="6f727-110">Usare un database.</span><span class="sxs-lookup"><span data-stu-id="6f727-110">Work with a database.</span></span>
> * <span data-ttu-id="6f727-111">Aggiungere ricerca e convalida.</span><span class="sxs-lookup"><span data-stu-id="6f727-111">Add search and validation.</span></span>

<span data-ttu-id="6f727-112">Al termine di queste operazioni si ottiene un'app che può gestire e visualizzare dati relativi ai film.</span><span class="sxs-lookup"><span data-stu-id="6f727-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="6f727-113">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="6f727-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6f727-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6f727-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6f727-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6f727-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6f727-116">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6f727-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="6f727-117">Creare un'app Web</span><span class="sxs-lookup"><span data-stu-id="6f727-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6f727-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6f727-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6f727-119">Dal menu di Visual Studio selezionare **Crea un nuovo progetto**.</span><span class="sxs-lookup"><span data-stu-id="6f727-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="6f727-120">Selezionare **Applicazione Web ASP.NET Core** e quindi selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="6f727-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![nuova applicazione Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="6f727-122">Assegnare al progetto il nome **MvcMovie** e selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="6f727-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="6f727-123">È importante assegnare al progetto il nome **MvcMovie**, in modo che quando si copia il codice lo spazio dei nomi corrisponda.</span><span class="sxs-lookup"><span data-stu-id="6f727-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![nuova applicazione Web ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="6f727-125">Selezionare **Applicazione Web (MVC)** e quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="6f727-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="6f727-126">Finestra di dialogo Nuovo progetto, .NET Core nel riquadro sinistro, Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6f727-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="6f727-127">Per il progetto MVC appena creato Visual Studio ha usato il modello predefinito.</span><span class="sxs-lookup"><span data-stu-id="6f727-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="6f727-128">Ora è possibile disporre di un'app funzionante immettendo un nome progetto e selezionando alcune opzioni.</span><span class="sxs-lookup"><span data-stu-id="6f727-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="6f727-129">Si tratta di un progetto iniziale di base.</span><span class="sxs-lookup"><span data-stu-id="6f727-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6f727-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6f727-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6f727-131">Nell'esercitazione si presuppone una familarità con Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="6f727-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="6f727-132">Vedere [Introduzione a VS Code](https://code.visualstudio.com/docs) e [Guida a Visual Studio Code](#visual-studio-code-help) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="6f727-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="6f727-133">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="6f727-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="6f727-134">Cambiare directory (`cd`) e passare alla cartella che conterrà il progetto.</span><span class="sxs-lookup"><span data-stu-id="6f727-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="6f727-135">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="6f727-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="6f727-136">Viene visualizzata una finestra di dialogo con le **risorse necessarie per la compilazione e il debug non è presente in ' MvcMovie '. Aggiungerli?**</span><span class="sxs-lookup"><span data-stu-id="6f727-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="6f727-137">Selezionare **Sì**</span><span class="sxs-lookup"><span data-stu-id="6f727-137">Select **Yes**</span></span>

  * <span data-ttu-id="6f727-138">`dotnet new mvc -o MvcMovie`: crea un nuovo progetto ASP.NET Core MVC nella cartella *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="6f727-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="6f727-139">`code -r MvcMovie`: Carica il file di progetto *MvcMovie. csproj* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="6f727-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6f727-140">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6f727-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6f727-141">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="6f727-141">Select **File** > **New Solution**.</span></span>

  ![Nuova soluzione macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="6f727-143">In Visual Studio per Mac precedente alla versione 8,6, selezionare applicazione Web **.NET Core**  >  **app**  >  **(Model-View-Controller)**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="6f727-143">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="6f727-144">Nella versione 8,6 o successive selezionare **Web e applicazione console**  >  **app**  >  **Web (Model-View-Controller)**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="6f727-144">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![selezione del modello di app Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="6f727-146">Verificare le configurazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="6f727-146">Confirm the following configurations:</span></span>

  * <span data-ttu-id="6f727-147">Il **Framework di destinazione** è impostato su **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="6f727-147">**Target Framework** set to **.NET Core 3.1**.</span></span>
  * <span data-ttu-id="6f727-148">**L'autenticazione** è impostata su **Nessuna autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="6f727-148">**Authentication** set to **No Authentication**.</span></span>
   
  <span data-ttu-id="6f727-149">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="6f727-149">Select **Next**.</span></span>

  ![selezione di macOS .NET Core 3,1](start-mvc/_static/new_project_31_vsmac.png)

* <span data-ttu-id="6f727-151">Denominare il progetto **MvcMovie**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="6f727-151">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS nome del progetto](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="6f727-153">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="6f727-153">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6f727-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6f727-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6f727-155">Selezionare **CTRL+F5** per eseguire l'app in modalità non di debug.</span><span class="sxs-lookup"><span data-stu-id="6f727-155">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="6f727-156">Visual Studio avvia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ed esegue l'app.</span><span class="sxs-lookup"><span data-stu-id="6f727-156">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="6f727-157">Si noti che la barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="6f727-157">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="6f727-158">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="6f727-158">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="6f727-159">Quando Visual Studio crea un progetto Web, per il server Web viene usata una porta casuale.</span><span class="sxs-lookup"><span data-stu-id="6f727-159">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="6f727-160">Se si avvia l'app con CTRL+F5 (modalità non di debug) sarà possibile apportare modifiche al codice, salvare il file, aggiornare il browser e visualizzare le modifiche del codice.</span><span class="sxs-lookup"><span data-stu-id="6f727-160">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="6f727-161">Molti sviluppatori preferiscono usare la modalità non di debug per avviare l'app rapidamente e visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="6f727-161">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="6f727-162">È possibile scegliere se avviare l'app in modalità di debug o non di debug nella voce di menu **Debug**:</span><span class="sxs-lookup"><span data-stu-id="6f727-162">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Debug](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="6f727-164">È possibile eseguire il debug dell'app toccando il pulsante **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="6f727-164">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="6f727-166">La figura seguente mostra l'app:</span><span class="sxs-lookup"><span data-stu-id="6f727-166">The following image shows the app:</span></span>

  ![Pagina Home o di indice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="6f727-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6f727-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6f727-169">Premere CTRL+F5 per l'esecuzione senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="6f727-169">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="6f727-170">Visual Studio Code avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="6f727-170">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="6f727-171">La barra degli indirizzi visualizza `localhost:port:5001` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="6f727-171">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="6f727-172">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="6f727-172">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="6f727-173">Localhost viene usato solo per le richieste web del computer locale.</span><span class="sxs-lookup"><span data-stu-id="6f727-173">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="6f727-174">Se si avvia l'app con CTRL+F5 (modalità non di debug) sarà possibile apportare modifiche al codice, salvare il file, aggiornare il browser e visualizzare le modifiche del codice.</span><span class="sxs-lookup"><span data-stu-id="6f727-174">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="6f727-175">Molti sviluppatori preferiscono usare la modalità non di debug per aggiornare la pagina e visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="6f727-175">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Pagina Home o di indice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6f727-177">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6f727-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6f727-178">Selezionare **Esegui**  >  **Avvia senza eseguire debug** per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="6f727-178">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="6f727-179">Visual Studio per Mac avvia il server [Kestrel](xref:fundamentals/servers/index#kestrel), apre un browser e naviga all'indirizzo `http://localhost:port`, dove *port* è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="6f727-179">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="6f727-180">La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="6f727-180">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="6f727-181">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="6f727-181">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="6f727-182">Quando Visual Studio crea un progetto Web, per il server Web viene usata una porta casuale.</span><span class="sxs-lookup"><span data-stu-id="6f727-182">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="6f727-183">Quando si esegue l'app verrà visualizzato un numero di porta diverso.</span><span class="sxs-lookup"><span data-stu-id="6f727-183">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="6f727-184">È possibile scegliere se avviare l'app in modalità di debug o non di dal menu **Esegui**.</span><span class="sxs-lookup"><span data-stu-id="6f727-184">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="6f727-185">La figura seguente mostra l'app:</span><span class="sxs-lookup"><span data-stu-id="6f727-185">The following image shows the app:</span></span>

  ![Pagina Home o di indice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="6f727-187">Nella parte seguente di questa esercitazione vengono fornite informazioni su MVC e istruzioni per iniziare a creare codice.</span><span class="sxs-lookup"><span data-stu-id="6f727-187">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="6f727-188">Avanti</span><span class="sxs-lookup"><span data-stu-id="6f727-188">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="6f727-189">Questa esercitazione illustra le nozioni di base della creazione di un'app Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="6f727-189">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="6f727-190">L'app gestisce un database di titoli di film.</span><span class="sxs-lookup"><span data-stu-id="6f727-190">The app manages a database of movie titles.</span></span> <span data-ttu-id="6f727-191">Si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="6f727-191">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6f727-192">Creare un'app Web.</span><span class="sxs-lookup"><span data-stu-id="6f727-192">Create a web app.</span></span>
> * <span data-ttu-id="6f727-193">Aggiungere un modello ed eseguirne lo scaffolding.</span><span class="sxs-lookup"><span data-stu-id="6f727-193">Add and scaffold a model.</span></span>
> * <span data-ttu-id="6f727-194">Usare un database.</span><span class="sxs-lookup"><span data-stu-id="6f727-194">Work with a database.</span></span>
> * <span data-ttu-id="6f727-195">Aggiungere ricerca e convalida.</span><span class="sxs-lookup"><span data-stu-id="6f727-195">Add search and validation.</span></span>

<span data-ttu-id="6f727-196">Al termine di queste operazioni si ottiene un'app che può gestire e visualizzare dati relativi ai film.</span><span class="sxs-lookup"><span data-stu-id="6f727-196">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="6f727-197">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="6f727-197">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6f727-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6f727-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6f727-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6f727-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6f727-200">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6f727-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="6f727-201">Creare un'app Web</span><span class="sxs-lookup"><span data-stu-id="6f727-201">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6f727-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6f727-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6f727-203">Dal menu di Visual Studio selezionare **Crea un nuovo progetto**.</span><span class="sxs-lookup"><span data-stu-id="6f727-203">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="6f727-204">Selezionare **Applicazione Web ASP.NET Core** e quindi selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="6f727-204">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![nuova applicazione Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="6f727-206">Assegnare al progetto il nome **MvcMovie** e selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="6f727-206">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="6f727-207">È importante assegnare al progetto il nome **MvcMovie**, in modo che quando si copia il codice lo spazio dei nomi corrisponda.</span><span class="sxs-lookup"><span data-stu-id="6f727-207">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![nuova applicazione Web ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="6f727-209">Selezionare **Applicazione Web (MVC)** e quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="6f727-209">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="6f727-210">Finestra di dialogo Nuovo progetto, .NET Core nel riquadro sinistro, Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6f727-210">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="6f727-211">Per il progetto MVC appena creato Visual Studio ha usato il modello predefinito.</span><span class="sxs-lookup"><span data-stu-id="6f727-211">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="6f727-212">Ora è possibile disporre di un'app funzionante immettendo un nome progetto e selezionando alcune opzioni.</span><span class="sxs-lookup"><span data-stu-id="6f727-212">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="6f727-213">Si tratta di un progetto iniziale di base che rappresenta un ottimo punto di partenza.</span><span class="sxs-lookup"><span data-stu-id="6f727-213">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6f727-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6f727-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6f727-215">Nell'esercitazione si presuppone una familarità con Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="6f727-215">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="6f727-216">Vedere [Introduzione a VS Code](https://code.visualstudio.com/docs) e [Guida a Visual Studio Code](#visual-studio-code-help) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="6f727-216">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="6f727-217">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="6f727-217">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="6f727-218">Cambiare directory (`cd`) e passare alla cartella che conterrà il progetto.</span><span class="sxs-lookup"><span data-stu-id="6f727-218">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="6f727-219">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="6f727-219">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="6f727-220">Viene visualizzata una finestra di dialogo con le **risorse necessarie per la compilazione e il debug non è presente in ' MvcMovie '. Aggiungerli?**</span><span class="sxs-lookup"><span data-stu-id="6f727-220">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="6f727-221">Selezionare **Sì**</span><span class="sxs-lookup"><span data-stu-id="6f727-221">Select **Yes**</span></span>

  * <span data-ttu-id="6f727-222">`dotnet new mvc -o MvcMovie`: crea un nuovo progetto ASP.NET Core MVC nella cartella *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="6f727-222">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="6f727-223">`code -r MvcMovie`: Carica il file di progetto *MvcMovie. csproj* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="6f727-223">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6f727-224">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6f727-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6f727-225">Selezionare **File** > **Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="6f727-225">Select **File** > **New Solution**.</span></span>

  ![Nuova soluzione macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="6f727-227">In Visual Studio per Mac precedente alla versione 8,6, selezionare applicazione Web **.NET Core**  >  **app**  >  **(Model-View-Controller)**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="6f727-227">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="6f727-228">Nella versione 8,6 o successive selezionare **Web e applicazione console**  >  **app**  >  **Web (Model-View-Controller)**  >  **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="6f727-228">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="6f727-229">Nella finestra di dialogo **configurare la nuova ASP.NET Core API Web** accettare il **Framework di destinazione** predefinito di **.NET Core 2,2**.</span><span class="sxs-lookup"><span data-stu-id="6f727-229">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![Selezione di .NET Core 2.2 per macOS](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="6f727-231">Denominare il progetto **MvcMovie**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="6f727-231">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="6f727-232">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="6f727-232">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6f727-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6f727-233">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6f727-234">Selezionare **CTRL+F5** per eseguire l'app in modalità non di debug.</span><span class="sxs-lookup"><span data-stu-id="6f727-234">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="6f727-235">Visual Studio avvia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ed esegue l'app.</span><span class="sxs-lookup"><span data-stu-id="6f727-235">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="6f727-236">Si noti che la barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="6f727-236">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="6f727-237">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="6f727-237">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="6f727-238">Quando Visual Studio crea un progetto Web, per il server Web viene usata una porta casuale.</span><span class="sxs-lookup"><span data-stu-id="6f727-238">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="6f727-239">Se si avvia l'app con CTRL+F5 (modalità non di debug) sarà possibile apportare modifiche al codice, salvare il file, aggiornare il browser e visualizzare le modifiche del codice.</span><span class="sxs-lookup"><span data-stu-id="6f727-239">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="6f727-240">Molti sviluppatori preferiscono usare la modalità non di debug per avviare l'app rapidamente e visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="6f727-240">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="6f727-241">È possibile scegliere se avviare l'app in modalità di debug o non di debug nella voce di menu **Debug**:</span><span class="sxs-lookup"><span data-stu-id="6f727-241">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Debug](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="6f727-243">È possibile eseguire il debug dell'app toccando il pulsante **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="6f727-243">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="6f727-245">Selezionare **Accept** (Accetto) per autorizzare il rilevamento.</span><span class="sxs-lookup"><span data-stu-id="6f727-245">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="6f727-246">Questa app non rileva informazioni personali.</span><span class="sxs-lookup"><span data-stu-id="6f727-246">This app doesn't track personal information.</span></span> <span data-ttu-id="6f727-247">Il codice generato del modello include asset che consentono di soddisfare il [Regolamento generale sulla protezione dei dati (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="6f727-247">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Pagina Home o di indice](start-mvc/_static/privacy.png)

  <span data-ttu-id="6f727-249">La figura seguente illustra l'app dopo aver accettato il rilevamento:</span><span class="sxs-lookup"><span data-stu-id="6f727-249">The following image shows the app after accepting tracking:</span></span>

  ![Pagina Home o di indice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="6f727-251">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6f727-251">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6f727-252">Premere CTRL+F5 per l'esecuzione senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="6f727-252">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="6f727-253">Visual Studio Code avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="6f727-253">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="6f727-254">La barra degli indirizzi visualizza `localhost:port:5001` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="6f727-254">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="6f727-255">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="6f727-255">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="6f727-256">Localhost viene usato solo per le richieste web del computer locale.</span><span class="sxs-lookup"><span data-stu-id="6f727-256">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="6f727-257">Se si avvia l'app con CTRL+F5 (modalità non di debug) sarà possibile apportare modifiche al codice, salvare il file, aggiornare il browser e visualizzare le modifiche del codice.</span><span class="sxs-lookup"><span data-stu-id="6f727-257">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="6f727-258">Molti sviluppatori preferiscono usare la modalità non di debug per aggiornare la pagina e visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="6f727-258">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="6f727-259">Selezionare **Accept** (Accetto) per autorizzare il rilevamento.</span><span class="sxs-lookup"><span data-stu-id="6f727-259">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="6f727-260">Questa app non rileva informazioni personali.</span><span class="sxs-lookup"><span data-stu-id="6f727-260">This app doesn't track personal information.</span></span> <span data-ttu-id="6f727-261">Il codice generato del modello include asset che consentono di soddisfare il [Regolamento generale sulla protezione dei dati (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="6f727-261">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Pagina Home o di indice](start-mvc/_static/privacy.png)

  <span data-ttu-id="6f727-263">La figura seguente illustra l'app dopo aver accettato il rilevamento:</span><span class="sxs-lookup"><span data-stu-id="6f727-263">The following image shows the app after accepting tracking:</span></span>

  ![Pagina Home o di indice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6f727-265">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6f727-265">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6f727-266">Selezionare **Esegui**  >  **Avvia senza eseguire debug** per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="6f727-266">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="6f727-267">Visual Studio per Mac avvia il server [Kestrel](xref:fundamentals/servers/index#kestrel), apre un browser e naviga all'indirizzo `http://localhost:port`, dove *port* è un numero di porta selezionato a caso.</span><span class="sxs-lookup"><span data-stu-id="6f727-267">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="6f727-268">La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="6f727-268">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="6f727-269">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="6f727-269">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="6f727-270">Quando Visual Studio crea un progetto Web, per il server Web viene usata una porta casuale.</span><span class="sxs-lookup"><span data-stu-id="6f727-270">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="6f727-271">Quando si esegue l'app verrà visualizzato un numero di porta diverso.</span><span class="sxs-lookup"><span data-stu-id="6f727-271">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="6f727-272">È possibile scegliere se avviare l'app in modalità di debug o non di dal menu **Esegui**.</span><span class="sxs-lookup"><span data-stu-id="6f727-272">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="6f727-273">Selezionare **Accept** (Accetto) per autorizzare il rilevamento.</span><span class="sxs-lookup"><span data-stu-id="6f727-273">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="6f727-274">Questa app non rileva informazioni personali.</span><span class="sxs-lookup"><span data-stu-id="6f727-274">This app doesn't track personal information.</span></span> <span data-ttu-id="6f727-275">Il codice generato del modello include asset che consentono di soddisfare il [Regolamento generale sulla protezione dei dati (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="6f727-275">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Pagina Home o di indice](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="6f727-277">La figura seguente illustra l'app dopo aver accettato il rilevamento:</span><span class="sxs-lookup"><span data-stu-id="6f727-277">The following image shows the app after accepting tracking:</span></span>

  ![Pagina Home o di indice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="6f727-279">Nella parte seguente di questa esercitazione vengono fornite informazioni su MVC e istruzioni per iniziare a creare codice.</span><span class="sxs-lookup"><span data-stu-id="6f727-279">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="6f727-280">Avanti</span><span class="sxs-lookup"><span data-stu-id="6f727-280">Next</span></span>](adding-controller.md)

::: moniker-end
