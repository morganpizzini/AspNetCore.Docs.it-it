---
title: Usare ASP.NET Core SignalR con Blazor webassembly
author: guardrex
description: Creare un'app di chat che usa ASP.NET Core SignalR con Blazor webassembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 720f534426cc0e2b32778e49050c7f7d75ecd60d
ms.sourcegitcommit: 6371114344a5f4fbc5d4a119b0be1ad3762e0216
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84679592"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="32c89-103">Usare ASP.NET Core SignalR con Blazor webassembly</span><span class="sxs-lookup"><span data-stu-id="32c89-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="32c89-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="32c89-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="32c89-105">Questa esercitazione illustra le nozioni di base per la creazione di un'app in tempo reale usando SignalR con Blazor webassembly.</span><span class="sxs-lookup"><span data-stu-id="32c89-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="32c89-106">Si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="32c89-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="32c89-107">Creare un Blazor progetto di app ospitata Webassembly</span><span class="sxs-lookup"><span data-stu-id="32c89-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="32c89-108">Aggiungere la SignalR libreria client</span><span class="sxs-lookup"><span data-stu-id="32c89-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="32c89-109">Aggiungere un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="32c89-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="32c89-110">Aggiungere SignalR Servizi e un endpoint per l' SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="32c89-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="32c89-111">Aggiungi Razor codice componente per la chat</span><span class="sxs-lookup"><span data-stu-id="32c89-111">Add Razor component code for chat</span></span>

<span data-ttu-id="32c89-112">Al termine di questa esercitazione, si disporrà di un'app di chat funzionante.</span><span class="sxs-lookup"><span data-stu-id="32c89-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="32c89-113">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="32c89-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="32c89-114">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="32c89-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="32c89-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="32c89-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="32c89-116">[Visual Studio 2019 16,6 o versione successiva](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con il carico di lavoro **sviluppo di ASP.NET e Web**</span><span class="sxs-lookup"><span data-stu-id="32c89-116">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="32c89-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="32c89-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="32c89-118">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="32c89-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="32c89-119">Visual Studio per Mac versione 8,6 o successiva</span><span class="sxs-lookup"><span data-stu-id="32c89-119">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="32c89-120">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="32c89-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="32c89-121">Creare un Blazor progetto di app webassembly ospitato</span><span class="sxs-lookup"><span data-stu-id="32c89-121">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="32c89-122">Seguire le istruzioni per la scelta degli strumenti:</span><span class="sxs-lookup"><span data-stu-id="32c89-122">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="32c89-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="32c89-123">Visual Studio</span></span>](#tab/visual-studio)

> [!NOTE]
> <span data-ttu-id="32c89-124">Sono necessari Visual Studio 16,6 o versioni successive e .NET Core SDK 3.1.300 o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="32c89-124">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

1. <span data-ttu-id="32c89-125">Creare un nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="32c89-125">Create a new project.</span></span>

1. <span data-ttu-id="32c89-126">Selezionare \*\* Blazor app\*\* e fare clic su **Next (avanti**).</span><span class="sxs-lookup"><span data-stu-id="32c89-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="32c89-127">Digitare "BlazorSignalRApp" nel campo **nome progetto** .</span><span class="sxs-lookup"><span data-stu-id="32c89-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="32c89-128">Confermare che la voce relativa al **percorso** sia corretta o specificare un percorso per il progetto.</span><span class="sxs-lookup"><span data-stu-id="32c89-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="32c89-129">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="32c89-129">Select **Create**.</span></span>

1. <span data-ttu-id="32c89-130">Scegliere il modello \*\* Blazor app webassembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="32c89-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="32c89-131">In **Avanzate**selezionare la casella di controllo **ASP.NET Core Hosted** .</span><span class="sxs-lookup"><span data-stu-id="32c89-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="32c89-132">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="32c89-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="32c89-133">Se è stato eseguito l'aggiornamento o l'installazione di una nuova versione di Visual Studio e il Blazor modello webassembly non viene visualizzato nell'interfaccia utente di Visual Studio, reinstallare il modello usando il `dotnet new` comando illustrato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="32c89-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="32c89-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="32c89-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="32c89-135">In una shell dei comandi eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="32c89-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="32c89-136">In Visual Studio Code aprire la cartella del progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="32c89-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="32c89-137">Quando viene visualizzata la finestra di dialogo per aggiungere asset per compilare ed eseguire il debug dell'app, selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="32c89-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="32c89-138">Visual Studio Code aggiunge automaticamente la cartella *. VSCODE* con i *launch.jsgenerati su* e *tasks.jsnei* file.</span><span class="sxs-lookup"><span data-stu-id="32c89-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="32c89-139">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="32c89-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="32c89-140">Installare la versione più recente di [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/) e seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="32c89-140">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="32c89-141">Selezionare **file**  >  **nuova soluzione** o creare un **nuovo** progetto dalla **finestra Start**.</span><span class="sxs-lookup"><span data-stu-id="32c89-141">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="32c89-142">Nella barra laterale selezionare app **Web e console**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="32c89-142">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="32c89-143">Scegliere il modello \*\* Blazor app webassembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="32c89-143">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="32c89-144">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="32c89-144">Select **Next**.</span></span>

   <span data-ttu-id="32c89-145">Verificare le configurazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="32c89-145">Confirm the following configurations:</span></span>

   * <span data-ttu-id="32c89-146">Il **Framework di destinazione** è impostato su **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="32c89-146">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="32c89-147">**L'autenticazione** è impostata su **Nessuna autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="32c89-147">**Authentication** set to **No Authentication**.</span></span>

   <span data-ttu-id="32c89-148">Selezionare la casella di controllo **ASP.NET Core Hosted** .</span><span class="sxs-lookup"><span data-stu-id="32c89-148">Select the **ASP.NET Core Hosted** check box.</span></span>

   <span data-ttu-id="32c89-149">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="32c89-149">Select **Next**.</span></span>

1. <span data-ttu-id="32c89-150">Nel campo **nome progetto** assegnare un nome all'app `BlazorSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="32c89-150">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="32c89-151">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="32c89-151">Select **Create**.</span></span>

   <span data-ttu-id="32c89-152">Se viene visualizzato un messaggio per considerare attendibile il certificato di sviluppo, considerare attendibile il certificato e continuare.</span><span class="sxs-lookup"><span data-stu-id="32c89-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="32c89-153">Per considerare attendibile il certificato, è necessario specificare le password dell'utente e del keychain.</span><span class="sxs-lookup"><span data-stu-id="32c89-153">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="32c89-154">Aprire il progetto passando alla cartella del progetto e aprendo il file di soluzione del progetto (*. sln*).</span><span class="sxs-lookup"><span data-stu-id="32c89-154">Open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="32c89-155">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="32c89-155">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="32c89-156">In una shell dei comandi eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="32c89-156">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="32c89-157">Aggiungere la SignalR libreria client</span><span class="sxs-lookup"><span data-stu-id="32c89-157">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="32c89-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="32c89-158">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="32c89-159">In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto **BlazorSignalRApp. client** e scegliere **Gestisci pacchetti NuGet**.</span><span class="sxs-lookup"><span data-stu-id="32c89-159">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="32c89-160">Nella finestra di dialogo **Gestisci pacchetti NuGet** verificare che l' **origine del pacchetto** sia impostata su *NuGet.org*.</span><span class="sxs-lookup"><span data-stu-id="32c89-160">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="32c89-161">Con **Sfoglia** selezionato digitare " SignalR Microsoft. AspNetCore. Client "nella casella di ricerca.</span><span class="sxs-lookup"><span data-stu-id="32c89-161">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="32c89-162">Nei risultati della ricerca selezionare [Microsoft. AspNetCore. SignalR Pacchetto client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) e selezionare **Installa**.</span><span class="sxs-lookup"><span data-stu-id="32c89-162">In the search results, select the [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) package and select **Install**.</span></span>

1. <span data-ttu-id="32c89-163">Se viene visualizzata la finestra di dialogo **Anteprima modifiche** , selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="32c89-163">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="32c89-164">Se viene visualizzata la finestra di dialogo **accettazione della licenza** , selezionare Accetto se **si accettano le** condizioni di licenza.</span><span class="sxs-lookup"><span data-stu-id="32c89-164">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="32c89-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="32c89-165">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="32c89-166">Nel **terminale integrato** (**visualizzare**il  >  **terminale** dalla barra degli strumenti) eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="32c89-166">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="32c89-167">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="32c89-167">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="32c89-168">Nella barra laterale **soluzione** fare clic con il pulsante destro del mouse sul progetto **BlazorSignalRApp. client** e scegliere **Gestisci pacchetti NuGet**.</span><span class="sxs-lookup"><span data-stu-id="32c89-168">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="32c89-169">Nella finestra di dialogo **Gestisci pacchetti NuGet** verificare che l'elenco a discesa origine sia impostato su *NuGet.org*.</span><span class="sxs-lookup"><span data-stu-id="32c89-169">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="32c89-170">Con **Sfoglia** selezionato digitare " SignalR Microsoft. AspNetCore. Client "nella casella di ricerca.</span><span class="sxs-lookup"><span data-stu-id="32c89-170">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="32c89-171">Nei risultati della ricerca selezionare la casella di controllo accanto a [Microsoft. AspNetCore. SignalR Pacchetto client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) e selezionare **Aggiungi pacchetto**.</span><span class="sxs-lookup"><span data-stu-id="32c89-171">In the search results, select the check box next to the [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) package and select **Add Package**.</span></span>

1. <span data-ttu-id="32c89-172">Se viene visualizzata la finestra di dialogo **accettazione della licenza** , selezionare **Accetto** se si accettano le condizioni di licenza.</span><span class="sxs-lookup"><span data-stu-id="32c89-172">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="32c89-173">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="32c89-173">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="32c89-174">In una shell dei comandi eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="32c89-174">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="32c89-175">Aggiungere un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="32c89-175">Add a SignalR hub</span></span>

<span data-ttu-id="32c89-176">Nel progetto **BlazorSignalRApp. Server** creare una cartella *Hub* (plurale) e aggiungere la classe seguente `ChatHub` (*Hub/ChatHub. cs*):</span><span class="sxs-lookup"><span data-stu-id="32c89-176">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="32c89-177">Aggiungere servizi e un endpoint per l' SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="32c89-177">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="32c89-178">Nel progetto **BlazorSignalRApp. Server** aprire il file *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="32c89-178">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="32c89-179">Aggiungere lo spazio dei nomi per la `ChatHub` classe all'inizio del file:</span><span class="sxs-lookup"><span data-stu-id="32c89-179">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="32c89-180">Aggiungere SignalR e rispondere ai servizi middleware di compressione per `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="32c89-180">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="32c89-181">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="32c89-181">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="32c89-182">Usare il middleware della compressione della risposta nella parte superiore della configurazione della pipeline di elaborazione.</span><span class="sxs-lookup"><span data-stu-id="32c89-182">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="32c89-183">Tra gli endpoint per i controller e il fallback sul lato client, aggiungere un endpoint per l'hub.</span><span class="sxs-lookup"><span data-stu-id="32c89-183">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="32c89-184">Aggiungi Razor codice componente per la chat</span><span class="sxs-lookup"><span data-stu-id="32c89-184">Add Razor component code for chat</span></span>

1. <span data-ttu-id="32c89-185">Nel progetto **BlazorSignalRApp. client** aprire il file *pages/index. Razor* .</span><span class="sxs-lookup"><span data-stu-id="32c89-185">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="32c89-186">Sostituire il markup con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="32c89-186">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="32c89-187">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="32c89-187">Run the app</span></span>

1. <span data-ttu-id="32c89-188">Seguire le istruzioni per gli strumenti:</span><span class="sxs-lookup"><span data-stu-id="32c89-188">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="32c89-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="32c89-189">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="32c89-190">In **Esplora soluzioni**selezionare il progetto **BlazorSignalRApp. Server** .</span><span class="sxs-lookup"><span data-stu-id="32c89-190">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="32c89-191">Premere <kbd>F5</kbd> per eseguire l'app con il debug o <kbd>CTRL</kbd> + <kbd>F5</kbd> per eseguire l'app senza debug.</span><span class="sxs-lookup"><span data-stu-id="32c89-191">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="32c89-192">Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="32c89-192">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="32c89-193">Scegliere un browser, immettere un nome e un messaggio e fare clic sul pulsante **Invia**.</span><span class="sxs-lookup"><span data-stu-id="32c89-193">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="32c89-194">Il nome e il messaggio vengono visualizzati immediatamente in entrambe le pagine:</span><span class="sxs-lookup"><span data-stu-id="32c89-194">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="32c89-195">![SignalRBlazorApp di esempio webassembly aperta in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="32c89-195">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="32c89-196">Virgolette: *Star Trek VI: il paese non individuato* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="32c89-196">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="32c89-197">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="32c89-197">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="32c89-198">Quando VS Code offre la creazione di un profilo di avvio per l'app Server (*con estensione VSCODE/launch.js*), la `program` voce appare simile alla seguente per puntare all'assembly dell'app ( `{APPLICATION NAME}.Server.dll` ):</span><span class="sxs-lookup"><span data-stu-id="32c89-198">When VS Code offers to create a launch profile for the Server app (*.vscode/launch.json*), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="32c89-199">Premere <kbd>F5</kbd> per eseguire l'app con il debug o <kbd>CTRL</kbd> + <kbd>F5</kbd> per eseguire l'app senza debug.</span><span class="sxs-lookup"><span data-stu-id="32c89-199">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="32c89-200">Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="32c89-200">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="32c89-201">Scegliere un browser, immettere un nome e un messaggio e fare clic sul pulsante **Invia**.</span><span class="sxs-lookup"><span data-stu-id="32c89-201">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="32c89-202">Il nome e il messaggio vengono visualizzati immediatamente in entrambe le pagine:</span><span class="sxs-lookup"><span data-stu-id="32c89-202">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="32c89-203">![SignalRBlazorApp di esempio webassembly aperta in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="32c89-203">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="32c89-204">Virgolette: *Star Trek VI: il paese non individuato* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="32c89-204">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="32c89-205">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="32c89-205">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="32c89-206">Nella barra laterale della **soluzione** selezionare il progetto **BlazorSignalRApp. Server** .</span><span class="sxs-lookup"><span data-stu-id="32c89-206">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="32c89-207">Premere <kbd>⌘</kbd> + <kbd>↩</kbd> per eseguire l'app con debug o <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> per eseguire l'app senza debug.</span><span class="sxs-lookup"><span data-stu-id="32c89-207">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="32c89-208">Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="32c89-208">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="32c89-209">Scegliere un browser, immettere un nome e un messaggio e fare clic sul pulsante **Invia**.</span><span class="sxs-lookup"><span data-stu-id="32c89-209">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="32c89-210">Il nome e il messaggio vengono visualizzati immediatamente in entrambe le pagine:</span><span class="sxs-lookup"><span data-stu-id="32c89-210">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="32c89-211">![SignalRBlazorApp di esempio webassembly aperta in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="32c89-211">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="32c89-212">Virgolette: *Star Trek VI: il paese non individuato* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="32c89-212">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="32c89-213">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="32c89-213">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="32c89-214">In una shell dei comandi eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="32c89-214">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="32c89-215">Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="32c89-215">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="32c89-216">Scegliere un browser, immettere un nome e un messaggio e fare clic sul pulsante **Invia**.</span><span class="sxs-lookup"><span data-stu-id="32c89-216">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="32c89-217">Il nome e il messaggio vengono visualizzati immediatamente in entrambe le pagine:</span><span class="sxs-lookup"><span data-stu-id="32c89-217">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="32c89-218">![SignalRBlazorApp di esempio webassembly aperta in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="32c89-218">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="32c89-219">Virgolette: *Star Trek VI: il paese non individuato* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="32c89-219">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="32c89-220">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="32c89-220">Next steps</span></span>

<span data-ttu-id="32c89-221">In questa esercitazione sono state illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="32c89-221">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="32c89-222">Creare un Blazor progetto di app ospitata Webassembly</span><span class="sxs-lookup"><span data-stu-id="32c89-222">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="32c89-223">Aggiungere la SignalR libreria client</span><span class="sxs-lookup"><span data-stu-id="32c89-223">Add the SignalR client library</span></span>
> * <span data-ttu-id="32c89-224">Aggiungere un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="32c89-224">Add a SignalR hub</span></span>
> * <span data-ttu-id="32c89-225">Aggiungere SignalR Servizi e un endpoint per l' SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="32c89-225">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="32c89-226">Aggiungi Razor codice componente per la chat</span><span class="sxs-lookup"><span data-stu-id="32c89-226">Add Razor component code for chat</span></span>

<span data-ttu-id="32c89-227">Per altre informazioni sulla creazione di Blazor app, vedere la Blazor documentazione:</span><span class="sxs-lookup"><span data-stu-id="32c89-227">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="32c89-228">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="32c89-228">Additional resources</span></span>

* <xref:signalr/introduction>
* <span data-ttu-id="32c89-229">[SignalRnegoziazione tra le origini per l'autenticazione](xref:blazor/hosting-model-configuration#signalr-cross-origin-negotiation-for-authentication)</span><span class="sxs-lookup"><span data-stu-id="32c89-229">[SignalR cross-origin negotiation for authentication](xref:blazor/hosting-model-configuration#signalr-cross-origin-negotiation-for-authentication)</span></span>
