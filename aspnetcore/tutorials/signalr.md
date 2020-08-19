---
title: Inizia a usare ASP.NET Core SignalR
author: bradygaster
description: In questa esercitazione si creerà un'app di chat che usa ASP.NET Core SignalR .
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
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
uid: tutorials/signalr
ms.openlocfilehash: e98cfc5e95233ce4d1001ab1225fc15e5fd23733
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634501"
---
# <a name="tutorial-get-started-with-aspnet-core-no-locsignalr"></a><span data-ttu-id="1d2bc-103">Esercitazione: Introduzione a ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="1d2bc-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1d2bc-104">Questa esercitazione illustra le nozioni di base per la creazione di un'app in tempo reale usando SignalR .</span><span class="sxs-lookup"><span data-stu-id="1d2bc-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="1d2bc-105">Si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1d2bc-106">Creare un progetto Web.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-106">Create a web project.</span></span>
> * <span data-ttu-id="1d2bc-107">Aggiungere la SignalR libreria client.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="1d2bc-108">Creare un SignalR Hub.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="1d2bc-109">Configurare il progetto da usare SignalR .</span><span class="sxs-lookup"><span data-stu-id="1d2bc-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="1d2bc-110">Aggiungere codice che invia messaggi da qualsiasi client a tutti i client connessi.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="1d2bc-111">Al termine, si disporrà di un'app di chat funzionante:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-111">At the end, you'll have a working chat app:</span></span>

![App::: NO-LOC (SignalR)::: Sample](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="1d2bc-113">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="1d2bc-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1d2bc-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1d2bc-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1d2bc-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1d2bc-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1d2bc-116">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="1d2bc-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="1d2bc-117">Creare un progetto di app Web</span><span class="sxs-lookup"><span data-stu-id="1d2bc-117">Create a web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1d2bc-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1d2bc-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="1d2bc-119">Nel menu selezionare **File > Nuovo progetto**.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="1d2bc-120">Nella finestra di dialogo **Crea un nuovo progetto** selezionare **Applicazione Web ASP.NET Core**, quindi selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="1d2bc-121">Nella finestra di dialogo **Configura il nuovo progetto** denominare il progetto \* SignalR chat\*, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="1d2bc-122">Nella finestra di dialogo **Crea una nuova applicazione web ASP.NET Core** selezionare **.net Core** e **ASP.NET Core 3,0**.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-122">In the **Create a new ASP.NET Core web Application** dialog, select **.NET Core** and **ASP.NET Core 3.0**.</span></span> 

* <span data-ttu-id="1d2bc-123">Selezionare **applicazione Web** per creare un progetto che utilizza Razor pagine, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Finestra di dialogo Nuovo progetto di Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="1d2bc-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1d2bc-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="1d2bc-126">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal) alla cartella in cui verrà creata la nuova cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="1d2bc-127">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1d2bc-128">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="1d2bc-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1d2bc-129">Nel menu selezionare **File > Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="1d2bc-130">Selezionare **.NET Core > App > Applicazione Web** (Non selezionare **Applicazione Web (Model-View-Controller)**), quindi selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="1d2bc-131">Assicurarsi che il **framework di destinazione** sia impostato su **.NET Core 3.0**, quindi selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-131">Make sure the **Target Framework** is set to **.NET Core 3.0**, and then select **Next**.</span></span>

* <span data-ttu-id="1d2bc-132">Denominare il progetto \* SignalR chat\*, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="1d2bc-133">Aggiungere la SignalR libreria client</span><span class="sxs-lookup"><span data-stu-id="1d2bc-133">Add the SignalR client library</span></span>

<span data-ttu-id="1d2bc-134">La SignalR libreria server è inclusa nel Framework condiviso ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-134">The SignalR server library is included in the ASP.NET Core 3.0 shared framework.</span></span> <span data-ttu-id="1d2bc-135">La libreria client JavaScript non viene inclusa automaticamente nel progetto.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="1d2bc-136">Per questa esercitazione, usare Gestione librerie (LibMan) per ottenere la libreria client da *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="1d2bc-137">unpkg è una rete per la distribuzione di contenuti (CDN) che può fornire qualsiasi elemento disponibile in NPM, il Node.js gestione pacchetti.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-137">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1d2bc-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1d2bc-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="1d2bc-139">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi** > **Client-Side Library** (Libreria lato client).</span><span class="sxs-lookup"><span data-stu-id="1d2bc-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="1d2bc-140">Nella finestra di dialogo **Add Client-Side Library** (Aggiungi libreria lato client) selezionare **unpkg** in **Provider**.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="1d2bc-141">Per la **Library**, immettere `@microsoft/signalr@latest`.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-141">For **Library**, enter `@microsoft/signalr@latest`.</span></span>

* <span data-ttu-id="1d2bc-142">Selezionare **Choose specific files** (Scegli file specifici), espandere la cartella *dist/browser* e selezionare *signalr.js* e *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="1d2bc-143">Impostare **percorso di destinazione** su *wwwroot/JS/SignalR/* e selezionare **Installa**.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-143">Set **Target Location** to *wwwroot/js/signalr/*, and select **Install**.</span></span>

  ![Finestra di dialogo Add Client-Side Library (Aggiungi libreria lato client) - selezione della libreria](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  <span data-ttu-id="1d2bc-145">LibMan crea una cartella *wwwroot/JS/SignalR* e ne copia i file selezionati.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-145">LibMan creates a *wwwroot/js/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1d2bc-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1d2bc-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="1d2bc-147">Eseguire il comando seguente nel terminale integrato per installare LibMan.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="1d2bc-148">Eseguire il comando seguente per ottenere la SignalR libreria client usando LibMan.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="1d2bc-149">Potrebbe essere necessario attendere alcuni secondi prima di visualizzare l'output.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="1d2bc-150">I parametri specificano le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="1d2bc-151">Usare il provider unpkg.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="1d2bc-152">Copiare i file nella destinazione *wwwroot/JS/SignalR* .</span><span class="sxs-lookup"><span data-stu-id="1d2bc-152">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="1d2bc-153">Copiare solo i file specificati.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-153">Copy only the specified files.</span></span>

  <span data-ttu-id="1d2bc-154">L'output ha un aspetto simile all'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1d2bc-155">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="1d2bc-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1d2bc-156">Nel **Terminale** eseguire il comando seguente per installare LibMan.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="1d2bc-157">Passare alla cartella del progetto (quella che contiene il file \* SignalR chat. csproj\* ).</span><span class="sxs-lookup"><span data-stu-id="1d2bc-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="1d2bc-158">Eseguire il comando seguente per ottenere la SignalR libreria client usando LibMan.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="1d2bc-159">I parametri specificano le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="1d2bc-160">Usare il provider unpkg.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="1d2bc-161">Copiare i file nella destinazione *wwwroot/JS/SignalR* .</span><span class="sxs-lookup"><span data-stu-id="1d2bc-161">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="1d2bc-162">Copiare solo i file specificati.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-162">Copy only the specified files.</span></span>

  <span data-ttu-id="1d2bc-163">L'output ha un aspetto simile all'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-no-locsignalr-hub"></a><span data-ttu-id="1d2bc-164">Creare un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="1d2bc-164">Create a SignalR hub</span></span>

<span data-ttu-id="1d2bc-165">Un *hub* è una classe usata come pipeline di alto livello che gestisce le comunicazioni client-server.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="1d2bc-166">Nella SignalR cartella del progetto chat creare una cartella *Hub* .</span><span class="sxs-lookup"><span data-stu-id="1d2bc-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="1d2bc-167">Nella cartella *Hubs* creare un file *ChatHub.cs* contenente il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="1d2bc-168">La classe `ChatHub` eredita dalla classe SignalR `Hub`.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="1d2bc-169">La classe `Hub` gestisce connessioni, gruppi e messaggistica.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="1d2bc-170">Il metodo `SendMessage` può essere chiamato da un client connesso per inviare un messaggio a tutti i client.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="1d2bc-171">Il codice client JavaScript che chiama il metodo è illustrato più avanti nell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="1d2bc-172">SignalR il codice è asincrono per garantire la massima scalabilità.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-no-locsignalr"></a><span data-ttu-id="1d2bc-173">ConfigurareSignalR</span><span class="sxs-lookup"><span data-stu-id="1d2bc-173">Configure SignalR</span></span>

<span data-ttu-id="1d2bc-174">Il SignalR Server deve essere configurato per il passaggio delle SignalR richieste a SignalR .</span><span class="sxs-lookup"><span data-stu-id="1d2bc-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="1d2bc-175">Aggiungere il codice evidenziato seguente al file *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  <span data-ttu-id="1d2bc-176">Queste modifiche aggiungono SignalR ai sistemi di routing e inserimento delle dipendenze ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-no-locsignalr-client-code"></a><span data-ttu-id="1d2bc-177">Aggiungi SignalR codice client</span><span class="sxs-lookup"><span data-stu-id="1d2bc-177">Add SignalR client code</span></span>

* <span data-ttu-id="1d2bc-178">Sostituire il codice in *Pages\Index.cshtml* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="1d2bc-179">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-179">The preceding code:</span></span>

  * <span data-ttu-id="1d2bc-180">Crea le caselle di testo per il nome e il messaggio di testo, nonché un pulsante di invio.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="1d2bc-181">Crea un elenco con `id="messagesList"` per visualizzare i messaggi ricevuti dall' SignalR Hub.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="1d2bc-182">Include i riferimenti agli script SignalR e il codice dell'applicazione *chat.js* creato nel passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="1d2bc-183">Nella cartella *wwwroot/js* creare un file *chat.js* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="1d2bc-184">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-184">The preceding code:</span></span>

  * <span data-ttu-id="1d2bc-185">Crea e avvia una connessione.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="1d2bc-186">Aggiunge al pulsante di invio un gestore che invia messaggi all'hub.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="1d2bc-187">Aggiunge all'oggetto connessione un gestore che riceve i messaggi dall'hub e li aggiunge all'elenco.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="1d2bc-188">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="1d2bc-188">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1d2bc-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1d2bc-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1d2bc-190">Premere **CTRL+F5** per eseguire l'app senza debug.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1d2bc-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1d2bc-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1d2bc-192">Eseguire il comando seguente nel terminale integrato:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1d2bc-193">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="1d2bc-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1d2bc-194">Nel menu selezionare **Esegui > Avvia senza eseguire debug**.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="1d2bc-195">Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="1d2bc-196">Scegliere un browser, immettere un nome e un messaggio e fare clic sul pulsante **Invia messaggio**.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="1d2bc-197">Nome e messaggio vengono visualizzati immediatamente in entrambe le pagine.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-197">The name and message are displayed on both pages instantly.</span></span>

  ![App::: NO-LOC (SignalR)::: Sample](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="1d2bc-199">Se l'app non funziona, aprire gli strumenti di sviluppo (F12) del browser e passare alla console.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="1d2bc-200">È possibile che vengano visualizzati errori correlati al codice HTML e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="1d2bc-201">Ad esempio, si supponga di aver inserito *signalr.js* in una cartella diversa da quella indicata nelle istruzioni.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="1d2bc-202">In questo caso il riferimento a tale file non funzionerà e verrà visualizzato un errore 404 nella console.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="1d2bc-203">![Errore di signalr.js non trovato](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="1d2bc-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="1d2bc-204">Se si riceve l'errore ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, eseguire questi comandi per aggiornare il certificato di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1d2bc-205">Questa esercitazione illustra le nozioni di base per la creazione di un'app in tempo reale usando SignalR .</span><span class="sxs-lookup"><span data-stu-id="1d2bc-205">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="1d2bc-206">Si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-206">You learn how to:</span></span> 

> [!div class="checklist"]  
> * <span data-ttu-id="1d2bc-207">Creare un progetto Web.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-207">Create a web project.</span></span>   
> * <span data-ttu-id="1d2bc-208">Aggiungere la SignalR libreria client.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-208">Add the SignalR client library.</span></span>   
> * <span data-ttu-id="1d2bc-209">Creare un SignalR Hub.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-209">Create a SignalR hub.</span></span> 
> * <span data-ttu-id="1d2bc-210">Configurare il progetto da usare SignalR .</span><span class="sxs-lookup"><span data-stu-id="1d2bc-210">Configure the project to use SignalR.</span></span> 
> * <span data-ttu-id="1d2bc-211">Aggiungere codice che invia messaggi da qualsiasi client a tutti i client connessi.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-211">Add code that sends messages from any client to all connected clients.</span></span>  
<span data-ttu-id="1d2bc-212">Alla fine, si avrà un'app di chat funzionante:::: ![ No-loc (SignalR)::: Sample App](signalr/_static/2.x/signalr-get-started-finished.png)</span><span class="sxs-lookup"><span data-stu-id="1d2bc-212">At the end, you'll have a working chat app: ![SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span>   

## <a name="prerequisites"></a><span data-ttu-id="1d2bc-213">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="1d2bc-213">Prerequisites</span></span>    

# <a name="visual-studio"></a>[<span data-ttu-id="1d2bc-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1d2bc-214">Visual Studio</span></span>](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[<span data-ttu-id="1d2bc-215">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1d2bc-215">Visual Studio Code</span></span>](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1d2bc-216">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="1d2bc-216">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a><span data-ttu-id="1d2bc-217">Creare un progetto Web</span><span class="sxs-lookup"><span data-stu-id="1d2bc-217">Create a web project</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="1d2bc-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1d2bc-218">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="1d2bc-219">Nel menu selezionare **File > Nuovo progetto**.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-219">From the menu, select **File > New Project**.</span></span> 

* <span data-ttu-id="1d2bc-220">Nella finestra di dialogo **Nuovo progetto** selezionare **Installate > Visual C# > Web > Applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-220">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="1d2bc-221">Denominare il progetto \* SignalR chat\*.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-221">Name the project *SignalRChat*.</span></span>   

  ![Finestra di dialogo Nuovo progetto di Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)    

* <span data-ttu-id="1d2bc-223">Selezionare **applicazione Web** per creare un progetto che utilizza Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-223">Select **Web Application** to create a project that uses Razor Pages.</span></span>   

* <span data-ttu-id="1d2bc-224">Selezionare un framework di destinazione di **.NET Core**, selezionare **ASP.NET Core 2.2** e fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-224">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>    

  ![Finestra di dialogo Nuovo progetto di Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[<span data-ttu-id="1d2bc-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1d2bc-226">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="1d2bc-227">Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal) alla cartella in cui verrà creata la nuova cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-227">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>  

* <span data-ttu-id="1d2bc-228">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-228">Run the following commands:</span></span>   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat   
   code -r SignalRChat    
   ```  

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1d2bc-229">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="1d2bc-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="1d2bc-230">Nel menu selezionare **File > Nuova soluzione**.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-230">From the menu, select **File > New Solution**.</span></span>    

* <span data-ttu-id="1d2bc-231">Selezionare **.NET Core > App > App Web ASP.NET Core** (non selezionare **App Web ASP.NET Core (MVC)**).</span><span class="sxs-lookup"><span data-stu-id="1d2bc-231">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>  

* <span data-ttu-id="1d2bc-232">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-232">Select **Next**.</span></span>  

* <span data-ttu-id="1d2bc-233">Denominare il progetto \* SignalR chat\*, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-233">Name the project *SignalRChat*, and then select **Create**.</span></span> 

--- 

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="1d2bc-234">Aggiungere la SignalR libreria client</span><span class="sxs-lookup"><span data-stu-id="1d2bc-234">Add the SignalR client library</span></span> 

<span data-ttu-id="1d2bc-235">La SignalR libreria del server è inclusa nel `Microsoft.AspNetCore.App` metapacchetto.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-235">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="1d2bc-236">La libreria client JavaScript non viene inclusa automaticamente nel progetto.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-236">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="1d2bc-237">Per questa esercitazione, usare Gestione librerie (LibMan) per ottenere la libreria client da *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-237">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="1d2bc-238">unpkg è una rete per la distribuzione di contenuti (CDN) che può fornire qualsiasi elemento disponibile in NPM, il Node.js gestione pacchetti.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-238">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>   

# <a name="visual-studio"></a>[<span data-ttu-id="1d2bc-239">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1d2bc-239">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="1d2bc-240">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi** > **Client-Side Library** (Libreria lato client).</span><span class="sxs-lookup"><span data-stu-id="1d2bc-240">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>  

* <span data-ttu-id="1d2bc-241">Nella finestra di dialogo **Add Client-Side Library** (Aggiungi libreria lato client) selezionare **unpkg** in **Provider**.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-241">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span> 

* <span data-ttu-id="1d2bc-242">In **Libreria** immettere `@microsoft/signalr@3`e selezionare la versione più recente che non sia di anteprima.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-242">For **Library**, enter `@microsoft/signalr@3`, and select the latest version that isn't preview.</span></span>  

  ![Finestra di dialogo Add Client-Side Library (Aggiungi libreria lato client) - selezione della libreria](signalr/_static/2.x/libman1.png)   

* <span data-ttu-id="1d2bc-244">Selezionare **Choose specific files** (Scegli file specifici), espandere la cartella *dist/browser* e selezionare *signalr.js* e *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-244">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span> 

* <span data-ttu-id="1d2bc-245">Impostare **Percorso di destinazione** su *wwwroot/lib/signalr/* e selezionare **Installa**.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-245">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>    

  ![Finestra di dialogo Add Client-Side Library (Aggiungi libreria lato client) - selezione di file e destinazione](signalr/_static/2.x/libman2.png) 

  <span data-ttu-id="1d2bc-247">LibMan crea una cartella *wwwroot/lib/signalr* e copia i file selezionati in tale cartella.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-247">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>    

# <a name="visual-studio-code"></a>[<span data-ttu-id="1d2bc-248">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1d2bc-248">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="1d2bc-249">Eseguire il comando seguente nel terminale integrato per installare LibMan.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-249">In the integrated terminal, run the following command to install LibMan.</span></span>  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="1d2bc-250">Eseguire il comando seguente per ottenere la SignalR libreria client usando LibMan.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-250">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="1d2bc-251">Potrebbe essere necessario attendere alcuni secondi prima di visualizzare l'output.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-251">You might have to wait a few seconds before seeing output.</span></span> 

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="1d2bc-252">I parametri specificano le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-252">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="1d2bc-253">Usare il provider unpkg.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-253">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="1d2bc-254">Copiare i file nella destinazione *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-254">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="1d2bc-255">Copiare solo i file specificati.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-255">Copy only the specified files.</span></span>  

  <span data-ttu-id="1d2bc-256">L'output ha un aspetto simile all'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-256">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1d2bc-257">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="1d2bc-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="1d2bc-258">Nel **Terminale** eseguire il comando seguente per installare LibMan.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-258">In the **Terminal**, run the following command to install LibMan.</span></span> 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="1d2bc-259">Passare alla cartella del progetto (quella che contiene il file \* SignalR chat. csproj\* ).</span><span class="sxs-lookup"><span data-stu-id="1d2bc-259">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>   

* <span data-ttu-id="1d2bc-260">Eseguire il comando seguente per ottenere la SignalR libreria client usando LibMan.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-260">Run the following command to get the SignalR client library by using LibMan.</span></span>    

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="1d2bc-261">I parametri specificano le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-261">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="1d2bc-262">Usare il provider unpkg.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-262">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="1d2bc-263">Copiare i file nella destinazione *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-263">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="1d2bc-264">Copiare solo i file specificati.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-264">Copy only the specified files.</span></span>  

  <span data-ttu-id="1d2bc-265">L'output ha un aspetto simile all'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-265">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-no-locsignalr-hub"></a><span data-ttu-id="1d2bc-266">Creare un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="1d2bc-266">Create a SignalR hub</span></span>   

<span data-ttu-id="1d2bc-267">Un *hub* è una classe usata come pipeline di alto livello che gestisce le comunicazioni client-server.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-267">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>   

* <span data-ttu-id="1d2bc-268">Nella SignalR cartella del progetto chat creare una cartella *Hub* .</span><span class="sxs-lookup"><span data-stu-id="1d2bc-268">In the SignalRChat project folder, create a *Hubs* folder.</span></span>  

* <span data-ttu-id="1d2bc-269">Nella cartella *Hubs* creare un file *ChatHub.cs* contenente il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-269">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span> 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  <span data-ttu-id="1d2bc-270">La classe `ChatHub` eredita dalla classe SignalR `Hub`.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-270">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="1d2bc-271">La classe `Hub` gestisce connessioni, gruppi e messaggistica.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-271">The `Hub` class manages connections, groups, and messaging.</span></span>  

  <span data-ttu-id="1d2bc-272">Il metodo `SendMessage` può essere chiamato da un client connesso per inviare un messaggio a tutti i client.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-272">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="1d2bc-273">Il codice client JavaScript che chiama il metodo è illustrato più avanti nell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-273">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="1d2bc-274">SignalR il codice è asincrono per garantire la massima scalabilità.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-274">SignalR code is asynchronous to provide maximum scalability.</span></span>    

## <a name="configure-no-locsignalr"></a><span data-ttu-id="1d2bc-275">ConfigurareSignalR</span><span class="sxs-lookup"><span data-stu-id="1d2bc-275">Configure SignalR</span></span>  

<span data-ttu-id="1d2bc-276">Il SignalR Server deve essere configurato per il passaggio delle SignalR richieste a SignalR .</span><span class="sxs-lookup"><span data-stu-id="1d2bc-276">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>    

* <span data-ttu-id="1d2bc-277">Aggiungere il codice evidenziato seguente al file *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-277">Add the following highlighted code to the *Startup.cs* file.</span></span>  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  <span data-ttu-id="1d2bc-278">Queste modifiche vengono aggiunte SignalR al sistema di inserimento delle dipendenze ASP.NET Core e alla pipeline middleware.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-278">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>  

## <a name="add-no-locsignalr-client-code"></a><span data-ttu-id="1d2bc-279">Aggiungi SignalR codice client</span><span class="sxs-lookup"><span data-stu-id="1d2bc-279">Add SignalR client code</span></span>    

* <span data-ttu-id="1d2bc-280">Sostituire il codice in *Pages\Index.cshtml* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-280">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  <span data-ttu-id="1d2bc-281">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-281">The preceding code:</span></span>   

  * <span data-ttu-id="1d2bc-282">Crea le caselle di testo per il nome e il messaggio di testo, nonché un pulsante di invio.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-282">Creates text boxes for name and message text, and a submit button.</span></span>  
  * <span data-ttu-id="1d2bc-283">Crea un elenco con `id="messagesList"` per visualizzare i messaggi ricevuti dall' SignalR Hub.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-283">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>   
  * <span data-ttu-id="1d2bc-284">Include i riferimenti agli script SignalR e il codice dell'applicazione *chat.js* creato nel passaggio successivo.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-284">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>    

* <span data-ttu-id="1d2bc-285">Nella cartella *wwwroot/js* creare un file *chat.js* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-285">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  <span data-ttu-id="1d2bc-286">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-286">The preceding code:</span></span>   

  * <span data-ttu-id="1d2bc-287">Crea e avvia una connessione.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-287">Creates and starts a connection.</span></span>    
  * <span data-ttu-id="1d2bc-288">Aggiunge al pulsante di invio un gestore che invia messaggi all'hub.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-288">Adds to the submit button a handler that sends messages to the hub.</span></span> 
  * <span data-ttu-id="1d2bc-289">Aggiunge all'oggetto connessione un gestore che riceve i messaggi dall'hub e li aggiunge all'elenco.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-289">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>  

## <a name="run-the-app"></a><span data-ttu-id="1d2bc-290">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="1d2bc-290">Run the app</span></span>  

# <a name="visual-studio"></a>[<span data-ttu-id="1d2bc-291">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1d2bc-291">Visual Studio</span></span>](#tab/visual-studio)   

* <span data-ttu-id="1d2bc-292">Premere **CTRL+F5** per eseguire l'app senza debug.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-292">Press **CTRL+F5** to run the app without debugging.</span></span>   

# <a name="visual-studio-code"></a>[<span data-ttu-id="1d2bc-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1d2bc-293">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="1d2bc-294">Eseguire il comando seguente nel terminale integrato:</span><span class="sxs-lookup"><span data-stu-id="1d2bc-294">In the integrated terminal, run the following command:</span></span>    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1d2bc-295">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="1d2bc-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1d2bc-296">Nel menu selezionare **Esegui > Avvia senza eseguire debug**.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-296">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="1d2bc-297">Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-297">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="1d2bc-298">Scegliere un browser, immettere un nome e un messaggio e fare clic sul pulsante **Invia messaggio**.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-298">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>  

  <span data-ttu-id="1d2bc-299">Nome e messaggio vengono visualizzati immediatamente in entrambe le pagine.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-299">The name and message are displayed on both pages instantly.</span></span>   

  ![App::: NO-LOC (SignalR)::: Sample](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> <span data-ttu-id="1d2bc-301">Se l'app non funziona, aprire gli strumenti di sviluppo (F12) del browser e passare alla console.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-301">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="1d2bc-302">È possibile che vengano visualizzati errori correlati al codice HTML e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-302">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="1d2bc-303">Ad esempio, si supponga di aver inserito *signalr.js* in una cartella diversa da quella indicata nelle istruzioni.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-303">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="1d2bc-304">In questo caso il riferimento a tale file non funzionerà e verrà visualizzato un errore 404 nella console.</span><span class="sxs-lookup"><span data-stu-id="1d2bc-304">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>   
> <span data-ttu-id="1d2bc-305">![Errore di signalr.js non trovato](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="1d2bc-305">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>    
## <a name="additional-resources"></a><span data-ttu-id="1d2bc-306">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="1d2bc-306">Additional resources</span></span> 
* [<span data-ttu-id="1d2bc-307">Versione di YouTube di questa esercitazione</span><span class="sxs-lookup"><span data-stu-id="1d2bc-307">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
