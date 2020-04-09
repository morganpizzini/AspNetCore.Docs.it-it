---
title: Utilizzo di SignalR Blazor ASP.NET Core con WebAssembly
author: guardrex
description: Creare un'app di SignalR chat Blazor che usa ASP.NET Core con WebAssembly.Create a chat app that uses ASP.NET Core with WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: c4843dc282e1978b39738e206ecc79ded87fcff9
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80306576"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="2c60f-103">Utilizzo di ASP.NET Core SignalR con Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="2c60f-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="2c60f-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2c60f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="2c60f-105">Questa esercitazione illustra le nozioni di base per la creazione di un'app in tempo reale usando SignalR con Blazor WebAssembly.This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="2c60f-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="2c60f-106">Si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="2c60f-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2c60f-107">Creare un progetto di app ospitata Blazor WebAssemblyCreate a Blazor WebAssembly Hosted app project</span><span class="sxs-lookup"><span data-stu-id="2c60f-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="2c60f-108">Aggiungere la libreria client di SignalR</span><span class="sxs-lookup"><span data-stu-id="2c60f-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="2c60f-109">Aggiungere un hub SignalRAdd a SignalR hub</span><span class="sxs-lookup"><span data-stu-id="2c60f-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="2c60f-110">Aggiungere servizi SignalR e un endpoint per l'hub SignalRAdd SignalR services and an endpoint for the SignalR hub</span><span class="sxs-lookup"><span data-stu-id="2c60f-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="2c60f-111">Aggiungere il codice del componente Razor per la chat</span><span class="sxs-lookup"><span data-stu-id="2c60f-111">Add Razor component code for chat</span></span>

<span data-ttu-id="2c60f-112">Alla fine di questo tutorial, avrai un'app di chat funzionante.</span><span class="sxs-lookup"><span data-stu-id="2c60f-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="2c60f-113">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2c60f-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2c60f-114">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="2c60f-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c60f-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c60f-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c60f-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c60f-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c60f-117">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="2c60f-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="2c60f-118">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="2c60f-118">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="2c60f-119">Creare un progetto di app Blazor WebAssembly ospitato</span><span class="sxs-lookup"><span data-stu-id="2c60f-119">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="2c60f-120">Quando non si usa Visual Studio versione 16.6 Preview 2 o versione successiva, installare il modello [Blazor WebAssembly.](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="2c60f-120">When not using Visual Studio version 16.6 Preview 2 or later, install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template.</span></span> <span data-ttu-id="2c60f-121">Il pacchetto [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) ha una versione di anteprima mentre Blazor WebAssembly è in anteprima.</span><span class="sxs-lookup"><span data-stu-id="2c60f-121">The [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span> <span data-ttu-id="2c60f-122">In una shell dei comandi, eseguire il comando seguente:In a command shell, execute the following command:</span><span class="sxs-lookup"><span data-stu-id="2c60f-122">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
```

<span data-ttu-id="2c60f-123">Seguire le istruzioni per la scelta degli utensili:</span><span class="sxs-lookup"><span data-stu-id="2c60f-123">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c60f-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c60f-124">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="2c60f-125">Creare un nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="2c60f-125">Create a new project.</span></span>

1. <span data-ttu-id="2c60f-126">Selezionare **Blazor App** e **quindi Avanti**.</span><span class="sxs-lookup"><span data-stu-id="2c60f-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="2c60f-127">Digitare "BlazorSignalRApp" nel campo **Nome progetto.**</span><span class="sxs-lookup"><span data-stu-id="2c60f-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="2c60f-128">Verificare che la voce **Posizione** sia corretta o specificare una posizione per il progetto.</span><span class="sxs-lookup"><span data-stu-id="2c60f-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="2c60f-129">Selezionare **Create** (Crea).</span><span class="sxs-lookup"><span data-stu-id="2c60f-129">Select **Create**.</span></span>

1. <span data-ttu-id="2c60f-130">Scegliere il modello **Blazor WebAssembly App.**</span><span class="sxs-lookup"><span data-stu-id="2c60f-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="2c60f-131">In **Avanzate**selezionare la casella di controllo **ASP.NET Core ospitato.**</span><span class="sxs-lookup"><span data-stu-id="2c60f-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="2c60f-132">Selezionare **Create** (Crea).</span><span class="sxs-lookup"><span data-stu-id="2c60f-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="2c60f-133">Se è stata aggiornata o installata una nuova versione di Visual Studio e il modello WebAssembly `dotnet new` Blazor non viene visualizzato nell'interfaccia utente di VS, reinstallare il modello utilizzando il comando illustrato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="2c60f-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c60f-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c60f-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="2c60f-135">In una shell dei comandi, eseguire il comando seguente:In a command shell, execute the following command:</span><span class="sxs-lookup"><span data-stu-id="2c60f-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="2c60f-136">In Visual Studio Code apri la cartella del progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="2c60f-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="2c60f-137">Quando viene visualizzata la finestra di dialogo per aggiungere risorse per compilare ed eseguire il debug dell'app, selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="2c60f-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="2c60f-138">Visual Studio Code aggiunge automaticamente la cartella *.vscode* con i file *launch.json* e *tasks.json* generati.</span><span class="sxs-lookup"><span data-stu-id="2c60f-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c60f-139">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="2c60f-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="2c60f-140">In una shell dei comandi, eseguire il comando seguente:In a command shell, execute the following command:</span><span class="sxs-lookup"><span data-stu-id="2c60f-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="2c60f-141">In Visual Studio per Mac aprire il progetto passando alla cartella del progetto e aprendo il file di soluzione del progetto (*.sln*).</span><span class="sxs-lookup"><span data-stu-id="2c60f-141">In Visual Studio for Mac, open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2c60f-142">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="2c60f-142">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="2c60f-143">In una shell dei comandi, eseguire il comando seguente:In a command shell, execute the following command:</span><span class="sxs-lookup"><span data-stu-id="2c60f-143">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="2c60f-144">Aggiungere la libreria client di SignalR</span><span class="sxs-lookup"><span data-stu-id="2c60f-144">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c60f-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c60f-145">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="2c60f-146">In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto **BlazorSignalRApp.Client** e **scegliere Gestisci pacchetti NuGet**.</span><span class="sxs-lookup"><span data-stu-id="2c60f-146">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="2c60f-147">Nella finestra di dialogo **Gestisci pacchetti NuGet** verificare che l'origine **del pacchetto** sia impostata su *nuget.org*.</span><span class="sxs-lookup"><span data-stu-id="2c60f-147">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="2c60f-148">Con **l'opzione Sfoglia** selezionata, digitare "Microsoft.AspNetCore.SignalR.Client" nella casella di ricerca.</span><span class="sxs-lookup"><span data-stu-id="2c60f-148">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="2c60f-149">Nei risultati della ricerca `Microsoft.AspNetCore.SignalR.Client` selezionare il pacchetto e scegliere **Installa**.</span><span class="sxs-lookup"><span data-stu-id="2c60f-149">In the search results, select the `Microsoft.AspNetCore.SignalR.Client` package and select **Install**.</span></span>

1. <span data-ttu-id="2c60f-150">Se viene visualizzata la finestra di dialogo **Anteprima modifiche,** selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="2c60f-150">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="2c60f-151">Se viene visualizzata la finestra di dialogo **Accettazione licenza,** selezionare **Accetto** se si accettano le condizioni di licenza.</span><span class="sxs-lookup"><span data-stu-id="2c60f-151">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c60f-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c60f-152">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="2c60f-153">Nel **Terminale integrato** **(Visualizza** > **terminale** dalla barra degli strumenti), eseguire i seguenti comandi:</span><span class="sxs-lookup"><span data-stu-id="2c60f-153">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c60f-154">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="2c60f-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="2c60f-155">Nella barra **laterale Soluzione** fare clic con il pulsante destro del mouse sul progetto **BlazorSignalRApp.Client** e scegliere **Gestisci pacchetti NuGet**.</span><span class="sxs-lookup"><span data-stu-id="2c60f-155">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="2c60f-156">Nella finestra di dialogo **Gestisci pacchetti NuGet** verificare che l'elenco a discesa Origine sia impostato su *nuget.org*.</span><span class="sxs-lookup"><span data-stu-id="2c60f-156">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="2c60f-157">Con **l'opzione Sfoglia** selezionata, digitare "Microsoft.AspNetCore.SignalR.Client" nella casella di ricerca.</span><span class="sxs-lookup"><span data-stu-id="2c60f-157">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="2c60f-158">Nei risultati della ricerca selezionare la `Microsoft.AspNetCore.SignalR.Client` casella di controllo accanto al pacchetto e scegliere **Aggiungi pacchetto**.</span><span class="sxs-lookup"><span data-stu-id="2c60f-158">In the search results, select the check box next to the `Microsoft.AspNetCore.SignalR.Client` package and select **Add Package**.</span></span>

1. <span data-ttu-id="2c60f-159">Se viene visualizzata la finestra di dialogo **Accettazione licenza,** selezionare **Accetta** se si accettano le condizioni di licenza.</span><span class="sxs-lookup"><span data-stu-id="2c60f-159">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2c60f-160">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="2c60f-160">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="2c60f-161">In una shell dei comandi, eseguire i seguenti comandi:</span><span class="sxs-lookup"><span data-stu-id="2c60f-161">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="2c60f-162">Aggiungere un hub SignalRAdd a SignalR hub</span><span class="sxs-lookup"><span data-stu-id="2c60f-162">Add a SignalR hub</span></span>

<span data-ttu-id="2c60f-163">Nel progetto **BlazorSignalRApp.Server** creare una cartella *Hubs* (plural) e aggiungere la classe seguente `ChatHub` (*Hubs/ChatHub.cs*):</span><span class="sxs-lookup"><span data-stu-id="2c60f-163">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-signalr-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="2c60f-164">Aggiungere servizi SignalR e un endpoint per l'hub SignalRAdd SignalR services and an endpoint for the SignalR hub</span><span class="sxs-lookup"><span data-stu-id="2c60f-164">Add SignalR services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="2c60f-165">Nel progetto **BlazorSignalRApp.Server** aprire il file *Startup.cs.*</span><span class="sxs-lookup"><span data-stu-id="2c60f-165">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="2c60f-166">Aggiungere lo spazio `ChatHub` dei nomi per la classe all'inizio del file:</span><span class="sxs-lookup"><span data-stu-id="2c60f-166">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="2c60f-167">Aggiungere i servizi `Startup.ConfigureServices`SignalR a :</span><span class="sxs-lookup"><span data-stu-id="2c60f-167">Add the SignalR services to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddSignalR();
   ```

1. <span data-ttu-id="2c60f-168">Tra `Startup.Configure` gli endpoint per la route del controller predefinito e il fallback lato client, aggiungere un endpoint per l'hub:In between the endpoints for the default controller route and the client-side fallback, add an endpoint for the hub:</span><span class="sxs-lookup"><span data-stu-id="2c60f-168">In `Startup.Configure` between the endpoints for the default controller route and the client-side fallback, add an endpoint for the hub:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="2c60f-169">Aggiungere il codice del componente Razor per la chat</span><span class="sxs-lookup"><span data-stu-id="2c60f-169">Add Razor component code for chat</span></span>

1. <span data-ttu-id="2c60f-170">Nel progetto **BlazorSignalRApp.Client** aprire il file *Pages/Index.razor.*</span><span class="sxs-lookup"><span data-stu-id="2c60f-170">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="2c60f-171">Sostituire il markup con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="2c60f-171">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="2c60f-172">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="2c60f-172">Run the app</span></span>

1. <span data-ttu-id="2c60f-173">Seguire le istruzioni per gli strumenti:</span><span class="sxs-lookup"><span data-stu-id="2c60f-173">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c60f-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c60f-174">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="2c60f-175">In **Esplora soluzioni**selezionare il progetto **BlazorSignalRApp.Server.**</span><span class="sxs-lookup"><span data-stu-id="2c60f-175">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="2c60f-176">Premete **Ctrl e F5** per eseguire l'app senza eseguire il debug.</span><span class="sxs-lookup"><span data-stu-id="2c60f-176">Press **Ctrl+F5** to run the app without debugging.</span></span>

1. <span data-ttu-id="2c60f-177">Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="2c60f-177">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2c60f-178">Scegliere un browser, immettere un nome e un messaggio e fare clic sul pulsante **Invia**.</span><span class="sxs-lookup"><span data-stu-id="2c60f-178">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="2c60f-179">Il nome e il messaggio vengono visualizzati istantaneamente su entrambe le pagine:</span><span class="sxs-lookup"><span data-stu-id="2c60f-179">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly app di esempio aperto in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="2c60f-181">Citazioni: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="2c60f-181">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c60f-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c60f-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="2c60f-183">Selezionare **Esegui debug** > **senza eseguire debug** dalla barra degli strumenti.</span><span class="sxs-lookup"><span data-stu-id="2c60f-183">Select **Debug** > **Run Without Debugging** from the toolbar.</span></span>

1. <span data-ttu-id="2c60f-184">Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="2c60f-184">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2c60f-185">Scegliere un browser, immettere un nome e un messaggio e fare clic sul pulsante **Invia**.</span><span class="sxs-lookup"><span data-stu-id="2c60f-185">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="2c60f-186">Il nome e il messaggio vengono visualizzati istantaneamente su entrambe le pagine:</span><span class="sxs-lookup"><span data-stu-id="2c60f-186">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly app di esempio aperto in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="2c60f-188">Citazioni: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="2c60f-188">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c60f-189">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="2c60f-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="2c60f-190">Nella barra **laterale Soluzione** selezionare il progetto **BlazorSignalRApp.Server.**</span><span class="sxs-lookup"><span data-stu-id="2c60f-190">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="2c60f-191">Scegliere **Esegui** > **senza eseguire debug**dal menu .</span><span class="sxs-lookup"><span data-stu-id="2c60f-191">From the menu, select **Run** > **Start Without Debugging**.</span></span>

1. <span data-ttu-id="2c60f-192">Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="2c60f-192">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2c60f-193">Scegliere un browser, immettere un nome e un messaggio e fare clic sul pulsante **Invia**.</span><span class="sxs-lookup"><span data-stu-id="2c60f-193">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="2c60f-194">Il nome e il messaggio vengono visualizzati istantaneamente su entrambe le pagine:</span><span class="sxs-lookup"><span data-stu-id="2c60f-194">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly app di esempio aperto in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="2c60f-196">Citazioni: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="2c60f-196">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2c60f-197">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="2c60f-197">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="2c60f-198">In una shell dei comandi, eseguire i seguenti comandi:</span><span class="sxs-lookup"><span data-stu-id="2c60f-198">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="2c60f-199">Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="2c60f-199">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2c60f-200">Scegliere un browser, immettere un nome e un messaggio e fare clic sul pulsante **Invia**.</span><span class="sxs-lookup"><span data-stu-id="2c60f-200">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="2c60f-201">Il nome e il messaggio vengono visualizzati istantaneamente su entrambe le pagine:</span><span class="sxs-lookup"><span data-stu-id="2c60f-201">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly app di esempio aperto in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="2c60f-203">Citazioni: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="2c60f-203">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="2c60f-204">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="2c60f-204">Next steps</span></span>

<span data-ttu-id="2c60f-205">In questa esercitazione sono state illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="2c60f-205">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2c60f-206">Creare Blazor un progetto di app ospitata WebAssemblyCreate a WebAssembly Hosted app project</span><span class="sxs-lookup"><span data-stu-id="2c60f-206">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="2c60f-207">Aggiungere SignalR la libreria client</span><span class="sxs-lookup"><span data-stu-id="2c60f-207">Add the SignalR client library</span></span>
> * <span data-ttu-id="2c60f-208">Aggiungere SignalR un hub</span><span class="sxs-lookup"><span data-stu-id="2c60f-208">Add a SignalR hub</span></span>
> * <span data-ttu-id="2c60f-209">Aggiungere SignalR servizi e un SignalR endpoint per l'hubAdd services and an endpoint for the hub</span><span class="sxs-lookup"><span data-stu-id="2c60f-209">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="2c60f-210">Aggiungere il codice del componente Razor per la chat</span><span class="sxs-lookup"><span data-stu-id="2c60f-210">Add Razor component code for chat</span></span>

<span data-ttu-id="2c60f-211">Per altre informazioni Blazor sulla creazione Blazor di app, vedere la documentazione:To learn more about building apps, see the documentation:</span><span class="sxs-lookup"><span data-stu-id="2c60f-211">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="2c60f-212">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2c60f-212">Additional resources</span></span>

* <xref:signalr/introduction>
