---
title: Usare ASP.NET Core SignalR conBlazor WebAssembly
author: guardrex
description: Creare un'app di chat che usa ASP.NET Core SignalR con Blazor WebAssembly .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 5a58e7ae28842e2e8a0f3bae8f47e252839903fe
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408877"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="d2092-103">Usare ASP.NET Core SignalR conBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="d2092-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="d2092-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d2092-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d2092-105">Questa esercitazione illustra le nozioni di base per la creazione di un'app in tempo reale usando SignalR con Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="d2092-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="d2092-106">Si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="d2092-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d2092-107">Creare un Blazor WebAssembly progetto di app ospitata</span><span class="sxs-lookup"><span data-stu-id="d2092-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="d2092-108">Aggiungere la SignalR libreria client</span><span class="sxs-lookup"><span data-stu-id="d2092-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="d2092-109">Aggiungere un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="d2092-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="d2092-110">Aggiungere SignalR Servizi e un endpoint per l' SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="d2092-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="d2092-111">Aggiungi Razor codice componente per la chat</span><span class="sxs-lookup"><span data-stu-id="d2092-111">Add Razor component code for chat</span></span>

<span data-ttu-id="d2092-112">Al termine di questa esercitazione, si disporrà di un'app di chat funzionante.</span><span class="sxs-lookup"><span data-stu-id="d2092-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="d2092-113">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d2092-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d2092-114">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="d2092-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d2092-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2092-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d2092-116">[Visual Studio 2019 16,6 o versione successiva](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con il carico di lavoro **sviluppo di ASP.NET e Web**</span><span class="sxs-lookup"><span data-stu-id="d2092-116">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d2092-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2092-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d2092-118">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="d2092-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="d2092-119">Visual Studio per Mac versione 8,6 o successiva</span><span class="sxs-lookup"><span data-stu-id="d2092-119">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="d2092-120">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="d2092-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="d2092-121">Creare un progetto di app ospitata Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="d2092-121">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="d2092-122">Seguire le istruzioni per la scelta degli strumenti:</span><span class="sxs-lookup"><span data-stu-id="d2092-122">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d2092-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2092-123">Visual Studio</span></span>](#tab/visual-studio)

> [!NOTE]
> <span data-ttu-id="d2092-124">Sono necessari Visual Studio 16,6 o versioni successive e .NET Core SDK 3.1.300 o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="d2092-124">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

1. <span data-ttu-id="d2092-125">Creare un nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="d2092-125">Create a new project.</span></span>

1. <span data-ttu-id="d2092-126">Selezionare \*\* Blazor app\*\* e fare clic su **Next (avanti**).</span><span class="sxs-lookup"><span data-stu-id="d2092-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="d2092-127">Digitare `BlazorSignalRApp` nel campo **nome progetto** .</span><span class="sxs-lookup"><span data-stu-id="d2092-127">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="d2092-128">Confermare che la voce relativa al **percorso** sia corretta o specificare un percorso per il progetto.</span><span class="sxs-lookup"><span data-stu-id="d2092-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="d2092-129">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="d2092-129">Select **Create**.</span></span>

1. <span data-ttu-id="d2092-130">Scegliere il modello di \*\* Blazor WebAssembly app\*\* .</span><span class="sxs-lookup"><span data-stu-id="d2092-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="d2092-131">In **Avanzate**selezionare la casella di controllo **ASP.NET Core Hosted** .</span><span class="sxs-lookup"><span data-stu-id="d2092-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="d2092-132">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="d2092-132">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d2092-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2092-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d2092-134">In una shell dei comandi eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="d2092-134">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="d2092-135">In Visual Studio Code aprire la cartella del progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="d2092-135">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="d2092-136">Quando viene visualizzata la finestra di dialogo per aggiungere asset per compilare ed eseguire il debug dell'app, selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="d2092-136">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="d2092-137">Visual Studio Code aggiunge automaticamente la `.vscode` cartella con `launch.json` `tasks.json` i file e generati.</span><span class="sxs-lookup"><span data-stu-id="d2092-137">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d2092-138">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="d2092-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d2092-139">Installare la versione più recente di [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/) e seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="d2092-139">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="d2092-140">Selezionare **file**  >  **nuova soluzione** o creare un **nuovo** progetto dalla **finestra Start**.</span><span class="sxs-lookup"><span data-stu-id="d2092-140">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="d2092-141">Nella barra laterale selezionare app **Web e console**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="d2092-141">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="d2092-142">Scegliere il modello di \*\* Blazor WebAssembly app\*\* .</span><span class="sxs-lookup"><span data-stu-id="d2092-142">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="d2092-143">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="d2092-143">Select **Next**.</span></span>

   <span data-ttu-id="d2092-144">Verificare le configurazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="d2092-144">Confirm the following configurations:</span></span>

   * <span data-ttu-id="d2092-145">Il **Framework di destinazione** è impostato su **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="d2092-145">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="d2092-146">**L'autenticazione** è impostata su **Nessuna autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="d2092-146">**Authentication** set to **No Authentication**.</span></span>

   <span data-ttu-id="d2092-147">Selezionare la casella di controllo **ASP.NET Core Hosted** .</span><span class="sxs-lookup"><span data-stu-id="d2092-147">Select the **ASP.NET Core Hosted** check box.</span></span>

   <span data-ttu-id="d2092-148">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="d2092-148">Select **Next**.</span></span>

1. <span data-ttu-id="d2092-149">Nel campo **nome progetto** assegnare un nome all'app `BlazorSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="d2092-149">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="d2092-150">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="d2092-150">Select **Create**.</span></span>

   <span data-ttu-id="d2092-151">Se viene visualizzato un messaggio per considerare attendibile il certificato di sviluppo, considerare attendibile il certificato e continuare.</span><span class="sxs-lookup"><span data-stu-id="d2092-151">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="d2092-152">Per considerare attendibile il certificato, è necessario specificare le password dell'utente e del keychain.</span><span class="sxs-lookup"><span data-stu-id="d2092-152">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="d2092-153">Aprire il progetto passando alla cartella del progetto e aprendo il file di soluzione del progetto ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="d2092-153">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d2092-154">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="d2092-154">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="d2092-155">In una shell dei comandi eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="d2092-155">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="d2092-156">Aggiungere la SignalR libreria client</span><span class="sxs-lookup"><span data-stu-id="d2092-156">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d2092-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2092-157">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="d2092-158">In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul `BlazorSignalRApp.Client` progetto e scegliere **Gestisci pacchetti NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d2092-158">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d2092-159">Nella finestra di dialogo **Gestisci pacchetti NuGet** verificare che l' **origine del pacchetto** sia impostata su `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="d2092-159">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d2092-160">Con **Sfoglia** selezionato, digitare `Microsoft.AspNetCore.SignalR.Client` nella casella di ricerca.</span><span class="sxs-lookup"><span data-stu-id="d2092-160">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="d2092-161">Nei risultati della ricerca selezionare il [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacchetto e selezionare **Installa**.</span><span class="sxs-lookup"><span data-stu-id="d2092-161">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="d2092-162">Se viene visualizzata la finestra di dialogo **Anteprima modifiche** , selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="d2092-162">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="d2092-163">Se viene visualizzata la finestra di dialogo **accettazione della licenza** , selezionare Accetto se **si accettano le** condizioni di licenza.</span><span class="sxs-lookup"><span data-stu-id="d2092-163">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d2092-164">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2092-164">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="d2092-165">Nel **terminale integrato** (**visualizzare**il  >  **terminale** dalla barra degli strumenti) eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="d2092-165">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d2092-166">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="d2092-166">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d2092-167">Nella barra laterale **soluzione** fare clic con il pulsante destro del mouse sul `BlazorSignalRApp.Client` progetto e scegliere **Gestisci pacchetti NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d2092-167">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d2092-168">Nella finestra di dialogo **Gestisci pacchetti NuGet** verificare che l'elenco a discesa origine sia impostato su `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="d2092-168">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d2092-169">Con **Sfoglia** selezionato, digitare `Microsoft.AspNetCore.SignalR.Client` nella casella di ricerca.</span><span class="sxs-lookup"><span data-stu-id="d2092-169">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="d2092-170">Nei risultati della ricerca selezionare la casella di controllo accanto al [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacchetto e selezionare **Aggiungi pacchetto**.</span><span class="sxs-lookup"><span data-stu-id="d2092-170">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="d2092-171">Se viene visualizzata la finestra di dialogo **accettazione della licenza** , selezionare **Accetto** se si accettano le condizioni di licenza.</span><span class="sxs-lookup"><span data-stu-id="d2092-171">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d2092-172">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="d2092-172">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="d2092-173">In una shell dei comandi eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="d2092-173">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="d2092-174">Aggiungere un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="d2092-174">Add a SignalR hub</span></span>

<span data-ttu-id="d2092-175">Nel `BlazorSignalRApp.Server` progetto creare una `Hubs` cartella (plurale) e aggiungere la classe seguente `ChatHub` ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="d2092-175">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="d2092-176">Aggiungere servizi e un endpoint per l' SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="d2092-176">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="d2092-177">Nel progetto `BlazorSignalRApp.Server` aprire il file `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="d2092-177">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="d2092-178">Aggiungere lo spazio dei nomi per la `ChatHub` classe all'inizio del file:</span><span class="sxs-lookup"><span data-stu-id="d2092-178">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="d2092-179">Aggiungere SignalR e rispondere ai servizi middleware di compressione per `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d2092-179">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="d2092-180">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d2092-180">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="d2092-181">Usare il middleware della compressione della risposta nella parte superiore della configurazione della pipeline di elaborazione.</span><span class="sxs-lookup"><span data-stu-id="d2092-181">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="d2092-182">Tra gli endpoint per i controller e il fallback sul lato client, aggiungere un endpoint per l'hub.</span><span class="sxs-lookup"><span data-stu-id="d2092-182">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="d2092-183">Aggiungi Razor codice componente per la chat</span><span class="sxs-lookup"><span data-stu-id="d2092-183">Add Razor component code for chat</span></span>

1. <span data-ttu-id="d2092-184">Nel progetto `BlazorSignalRApp.Client` aprire il file `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="d2092-184">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="d2092-185">Sostituire il markup con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="d2092-185">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="d2092-186">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="d2092-186">Run the app</span></span>

1. <span data-ttu-id="d2092-187">Seguire le istruzioni per gli strumenti:</span><span class="sxs-lookup"><span data-stu-id="d2092-187">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d2092-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2092-188">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d2092-189">In **Esplora soluzioni**selezionare il `BlazorSignalRApp.Server` progetto.</span><span class="sxs-lookup"><span data-stu-id="d2092-189">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="d2092-190">Premere <kbd>F5</kbd> per eseguire l'app con il debug o <kbd>CTRL</kbd> + <kbd>F5</kbd> per eseguire l'app senza debug.</span><span class="sxs-lookup"><span data-stu-id="d2092-190">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="d2092-191">Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="d2092-191">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d2092-192">Scegliere browser, immettere un nome e un messaggio e selezionare il pulsante per l'invio del messaggio.</span><span class="sxs-lookup"><span data-stu-id="d2092-192">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d2092-193">Il nome e il messaggio vengono visualizzati immediatamente in entrambe le pagine:</span><span class="sxs-lookup"><span data-stu-id="d2092-193">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="d2092-194">![SignalRBlazor WebAssemblyapp di esempio aperta in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="d2092-194">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="d2092-195">Virgolette: *Star Trek VI: il paese non individuato* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d2092-195">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d2092-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2092-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d2092-197">Quando VS Code offre la creazione di un profilo di avvio per l'app Server ( `.vscode/launch.json` ), la `program` voce appare simile alla seguente per puntare all'assembly dell'app ( `{APPLICATION NAME}.Server.dll` ):</span><span class="sxs-lookup"><span data-stu-id="d2092-197">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="d2092-198">Premere <kbd>F5</kbd> per eseguire l'app con il debug o <kbd>CTRL</kbd> + <kbd>F5</kbd> per eseguire l'app senza debug.</span><span class="sxs-lookup"><span data-stu-id="d2092-198">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="d2092-199">Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="d2092-199">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d2092-200">Scegliere browser, immettere un nome e un messaggio e selezionare il pulsante per l'invio del messaggio.</span><span class="sxs-lookup"><span data-stu-id="d2092-200">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d2092-201">Il nome e il messaggio vengono visualizzati immediatamente in entrambe le pagine:</span><span class="sxs-lookup"><span data-stu-id="d2092-201">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="d2092-202">![SignalRBlazor WebAssemblyapp di esempio aperta in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="d2092-202">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="d2092-203">Virgolette: *Star Trek VI: il paese non individuato* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d2092-203">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d2092-204">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="d2092-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d2092-205">Nella barra laterale della **soluzione** selezionare il `BlazorSignalRApp.Server` progetto.</span><span class="sxs-lookup"><span data-stu-id="d2092-205">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="d2092-206">Premere <kbd>⌘</kbd> + <kbd>↩</kbd> per eseguire l'app con debug o <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> per eseguire l'app senza debug.</span><span class="sxs-lookup"><span data-stu-id="d2092-206">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="d2092-207">Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="d2092-207">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d2092-208">Scegliere browser, immettere un nome e un messaggio e selezionare il pulsante per l'invio del messaggio.</span><span class="sxs-lookup"><span data-stu-id="d2092-208">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d2092-209">Il nome e il messaggio vengono visualizzati immediatamente in entrambe le pagine:</span><span class="sxs-lookup"><span data-stu-id="d2092-209">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="d2092-210">![SignalRBlazor WebAssemblyapp di esempio aperta in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="d2092-210">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="d2092-211">Virgolette: *Star Trek VI: il paese non individuato* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d2092-211">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d2092-212">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="d2092-212">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="d2092-213">In una shell dei comandi eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="d2092-213">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="d2092-214">Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="d2092-214">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d2092-215">Scegliere browser, immettere un nome e un messaggio e selezionare il pulsante per l'invio del messaggio.</span><span class="sxs-lookup"><span data-stu-id="d2092-215">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d2092-216">Il nome e il messaggio vengono visualizzati immediatamente in entrambe le pagine:</span><span class="sxs-lookup"><span data-stu-id="d2092-216">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="d2092-217">![SignalRBlazor WebAssemblyapp di esempio aperta in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="d2092-217">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="d2092-218">Virgolette: *Star Trek VI: il paese non individuato* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d2092-218">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="d2092-219">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="d2092-219">Next steps</span></span>

<span data-ttu-id="d2092-220">In questa esercitazione sono state illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="d2092-220">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d2092-221">Creare un Blazor WebAssembly progetto di app ospitata</span><span class="sxs-lookup"><span data-stu-id="d2092-221">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="d2092-222">Aggiungere la SignalR libreria client</span><span class="sxs-lookup"><span data-stu-id="d2092-222">Add the SignalR client library</span></span>
> * <span data-ttu-id="d2092-223">Aggiungere un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="d2092-223">Add a SignalR hub</span></span>
> * <span data-ttu-id="d2092-224">Aggiungere SignalR Servizi e un endpoint per l' SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="d2092-224">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="d2092-225">Aggiungi Razor codice componente per la chat</span><span class="sxs-lookup"><span data-stu-id="d2092-225">Add Razor component code for chat</span></span>

<span data-ttu-id="d2092-226">Per altre informazioni sulla creazione di Blazor app, vedere la Blazor documentazione:</span><span class="sxs-lookup"><span data-stu-id="d2092-226">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="d2092-227">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="d2092-227">Additional resources</span></span>

* <xref:signalr/introduction>
* <span data-ttu-id="d2092-228">[SignalRnegoziazione tra le origini per l'autenticazione](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span><span class="sxs-lookup"><span data-stu-id="d2092-228">[SignalR cross-origin negotiation for authentication](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span></span>
