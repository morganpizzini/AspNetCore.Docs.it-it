---
title: "Usare ASP.NET Core SignalR con un'app ospitata Blazor WebAssembly"
author: guardrex
description: "Creare un'app di chat che usa ASP.NET Core SignalR con Blazor WebAssembly ."
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 2975ee699a2535cdf63ef2f5af5790f178a09f93
ms.sourcegitcommit: e087b6a38e3d38625ebb567a973e75b4d79547b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637730"
---
# <a name="use-aspnet-core-no-locsignalr-with-a-hosted-no-locblazor-webassembly-app"></a><span data-ttu-id="6c20a-103">Usare ASP.NET Core SignalR con un'app ospitata Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="6c20a-103">Use ASP.NET Core SignalR with a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="6c20a-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6c20a-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6c20a-105">Questa esercitazione illustra le nozioni di base per la creazione di un'app in tempo reale usando SignalR con Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="6c20a-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="6c20a-106">Si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="6c20a-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6c20a-107">Creare un Blazor WebAssembly progetto di app ospitata</span><span class="sxs-lookup"><span data-stu-id="6c20a-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="6c20a-108">Aggiungere la SignalR libreria client</span><span class="sxs-lookup"><span data-stu-id="6c20a-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="6c20a-109">Aggiungere un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="6c20a-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="6c20a-110">Aggiungere SignalR Servizi e un endpoint per l' SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="6c20a-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="6c20a-111">Aggiungi Razor codice componente per la chat</span><span class="sxs-lookup"><span data-stu-id="6c20a-111">Add Razor component code for chat</span></span>

<span data-ttu-id="6c20a-112">Al termine di questa esercitazione, si disporrà di un'app di chat funzionante.</span><span class="sxs-lookup"><span data-stu-id="6c20a-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="6c20a-113">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6c20a-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6c20a-114">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="6c20a-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="6c20a-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c20a-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6c20a-116">[Visual Studio 2019 16,8 o versione successiva](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con il carico di lavoro **sviluppo di ASP.NET e Web**</span><span class="sxs-lookup"><span data-stu-id="6c20a-116">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6c20a-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6c20a-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6c20a-118">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6c20a-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="6c20a-119">Visual Studio per Mac versione 8,8 o successiva</span><span class="sxs-lookup"><span data-stu-id="6c20a-119">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="6c20a-120">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="6c20a-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="6c20a-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c20a-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6c20a-122">[Visual Studio 2019 16,6 o versione successiva](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con il carico di lavoro **sviluppo di ASP.NET e Web**</span><span class="sxs-lookup"><span data-stu-id="6c20a-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6c20a-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6c20a-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6c20a-124">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6c20a-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="6c20a-125">Visual Studio per Mac versione 8,6 o successiva</span><span class="sxs-lookup"><span data-stu-id="6c20a-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="6c20a-126">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="6c20a-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="6c20a-127">Creare un progetto di app ospitata Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="6c20a-127">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="6c20a-128">Seguire le istruzioni per la scelta degli strumenti:</span><span class="sxs-lookup"><span data-stu-id="6c20a-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6c20a-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c20a-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="6c20a-130">Sono necessari Visual Studio 16,8 o versioni successive e .NET Core SDK 5.0.0 o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="6c20a-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="6c20a-131">Sono necessari Visual Studio 16,6 o versioni successive e .NET Core SDK 3.1.300 o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="6c20a-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="6c20a-132">Creare un nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="6c20a-132">Create a new project.</span></span>

1. <span data-ttu-id="6c20a-133">Selezionare **Blazor app** e fare clic su **Next (avanti** ).</span><span class="sxs-lookup"><span data-stu-id="6c20a-133">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="6c20a-134">Digitare `BlazorSignalRApp` nel campo **nome progetto** .</span><span class="sxs-lookup"><span data-stu-id="6c20a-134">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="6c20a-135">Confermare che la voce relativa al **percorso** sia corretta o specificare un percorso per il progetto.</span><span class="sxs-lookup"><span data-stu-id="6c20a-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="6c20a-136">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="6c20a-136">Select **Create**.</span></span>

1. <span data-ttu-id="6c20a-137">Scegliere il modello di **Blazor WebAssembly app** .</span><span class="sxs-lookup"><span data-stu-id="6c20a-137">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="6c20a-138">In **Avanzate** selezionare la casella di controllo **ASP.NET Core Hosted** .</span><span class="sxs-lookup"><span data-stu-id="6c20a-138">Under **Advanced** , select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="6c20a-139">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="6c20a-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6c20a-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6c20a-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="6c20a-141">In una shell dei comandi eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="6c20a-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="6c20a-142">In Visual Studio Code aprire la cartella del progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="6c20a-142">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="6c20a-143">Quando viene visualizzata la finestra di dialogo per aggiungere asset per compilare ed eseguire il debug dell'app, selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="6c20a-143">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="6c20a-144">Visual Studio Code aggiunge automaticamente la `.vscode` cartella con `launch.json` `tasks.json` i file e generati.</span><span class="sxs-lookup"><span data-stu-id="6c20a-144">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6c20a-145">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6c20a-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6c20a-146">Installare la versione più recente di [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/) e seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="6c20a-146">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="6c20a-147">Selezionare **file**  >  **nuova soluzione** o creare un **nuovo** progetto dalla **finestra Start**.</span><span class="sxs-lookup"><span data-stu-id="6c20a-147">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="6c20a-148">Nella barra laterale selezionare app **Web e console**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="6c20a-148">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="6c20a-149">Scegliere il modello di **Blazor WebAssembly app** .</span><span class="sxs-lookup"><span data-stu-id="6c20a-149">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="6c20a-150">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="6c20a-150">Select **Next**.</span></span>

1. <span data-ttu-id="6c20a-151">Verificare che **l'autenticazione** sia impostata su **Nessuna autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="6c20a-151">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="6c20a-152">Selezionare la casella di controllo **ASP.NET Core Hosted** .</span><span class="sxs-lookup"><span data-stu-id="6c20a-152">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="6c20a-153">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="6c20a-153">Select **Next**.</span></span>

1. <span data-ttu-id="6c20a-154">Nel campo **nome progetto** assegnare un nome all'app `BlazorSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="6c20a-154">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="6c20a-155">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="6c20a-155">Select **Create**.</span></span>

   <span data-ttu-id="6c20a-156">Se viene visualizzato un messaggio per considerare attendibile il certificato di sviluppo, considerare attendibile il certificato e continuare.</span><span class="sxs-lookup"><span data-stu-id="6c20a-156">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="6c20a-157">Per considerare attendibile il certificato, è necessario specificare le password dell'utente e del keychain.</span><span class="sxs-lookup"><span data-stu-id="6c20a-157">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="6c20a-158">Aprire il progetto passando alla cartella del progetto e aprendo il file di soluzione del progetto ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="6c20a-158">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6c20a-159">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="6c20a-159">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="6c20a-160">In una shell dei comandi eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="6c20a-160">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="6c20a-161">Aggiungere la SignalR libreria client</span><span class="sxs-lookup"><span data-stu-id="6c20a-161">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6c20a-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c20a-162">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="6c20a-163">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul `BlazorSignalRApp.Client` progetto e scegliere **Gestisci pacchetti NuGet**.</span><span class="sxs-lookup"><span data-stu-id="6c20a-163">In **Solution Explorer** , right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="6c20a-164">Nella finestra di dialogo **Gestisci pacchetti NuGet** verificare che l' **origine del pacchetto** sia impostata su `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="6c20a-164">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="6c20a-165">Con **Sfoglia** selezionato, digitare `Microsoft.AspNetCore.SignalR.Client` nella casella di ricerca.</span><span class="sxs-lookup"><span data-stu-id="6c20a-165">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="6c20a-166">Nei risultati della ricerca selezionare il [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacchetto e selezionare **Installa**.</span><span class="sxs-lookup"><span data-stu-id="6c20a-166">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="6c20a-167">Se viene visualizzata la finestra di dialogo **Anteprima modifiche** , selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="6c20a-167">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="6c20a-168">Se viene visualizzata la finestra di dialogo **accettazione della licenza** , selezionare Accetto se **si accettano le** condizioni di licenza.</span><span class="sxs-lookup"><span data-stu-id="6c20a-168">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6c20a-169">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6c20a-169">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="6c20a-170">Nel **terminale integrato** ( **visualizzare** il  >  **terminale** dalla barra degli strumenti) eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="6c20a-170">In the **Integrated Terminal** ( **View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6c20a-171">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6c20a-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6c20a-172">Nella barra laterale **soluzione** fare clic con il pulsante destro del mouse sul `BlazorSignalRApp.Client` progetto e scegliere **Gestisci pacchetti NuGet**.</span><span class="sxs-lookup"><span data-stu-id="6c20a-172">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="6c20a-173">Nella finestra di dialogo **Gestisci pacchetti NuGet** verificare che l'elenco a discesa origine sia impostato su `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="6c20a-173">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="6c20a-174">Con **Sfoglia** selezionato, digitare `Microsoft.AspNetCore.SignalR.Client` nella casella di ricerca.</span><span class="sxs-lookup"><span data-stu-id="6c20a-174">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="6c20a-175">Nei risultati della ricerca selezionare la casella di controllo accanto al [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pacchetto e selezionare **Aggiungi pacchetto**.</span><span class="sxs-lookup"><span data-stu-id="6c20a-175">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="6c20a-176">Se viene visualizzata la finestra di dialogo **accettazione della licenza** , selezionare **Accetto** se si accettano le condizioni di licenza.</span><span class="sxs-lookup"><span data-stu-id="6c20a-176">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6c20a-177">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="6c20a-177">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="6c20a-178">In una shell dei comandi eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="6c20a-178">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="6c20a-179">Aggiungere un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="6c20a-179">Add a SignalR hub</span></span>

<span data-ttu-id="6c20a-180">Nel `BlazorSignalRApp.Server` progetto creare una `Hubs` cartella (plurale) e aggiungere la classe seguente `ChatHub` ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="6c20a-180">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="6c20a-181">Aggiungere servizi e un endpoint per l' SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="6c20a-181">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="6c20a-182">Nel progetto `BlazorSignalRApp.Server` aprire il file `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="6c20a-182">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="6c20a-183">Aggiungere lo spazio dei nomi per la `ChatHub` classe all'inizio del file:</span><span class="sxs-lookup"><span data-stu-id="6c20a-183">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="6c20a-184">Aggiungere SignalR e rispondere ai servizi middleware di compressione per `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6c20a-184">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

1. <span data-ttu-id="6c20a-185">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="6c20a-185">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="6c20a-186">Usare il middleware della compressione della risposta nella parte superiore della configurazione della pipeline di elaborazione.</span><span class="sxs-lookup"><span data-stu-id="6c20a-186">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="6c20a-187">Tra gli endpoint per i controller e il fallback sul lato client, aggiungere un endpoint per l'hub.</span><span class="sxs-lookup"><span data-stu-id="6c20a-187">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="6c20a-188">Aggiungi Razor codice componente per la chat</span><span class="sxs-lookup"><span data-stu-id="6c20a-188">Add Razor component code for chat</span></span>

1. <span data-ttu-id="6c20a-189">Nel progetto `BlazorSignalRApp.Client` aprire il file `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="6c20a-189">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="6c20a-190">Sostituire il markup con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="6c20a-190">Replace the markup with the following code:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="6c20a-191">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="6c20a-191">Run the app</span></span>

1. <span data-ttu-id="6c20a-192">Seguire le istruzioni per gli strumenti:</span><span class="sxs-lookup"><span data-stu-id="6c20a-192">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6c20a-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c20a-193">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="6c20a-194">In **Esplora soluzioni** selezionare il `BlazorSignalRApp.Server` progetto.</span><span class="sxs-lookup"><span data-stu-id="6c20a-194">In **Solution Explorer** , select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="6c20a-195">Premere <kbd>F5</kbd> per eseguire l'app con il debug o <kbd>CTRL</kbd> + <kbd>F5</kbd> per eseguire l'app senza debug.</span><span class="sxs-lookup"><span data-stu-id="6c20a-195">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="6c20a-196">Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="6c20a-196">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="6c20a-197">Scegliere browser, immettere un nome e un messaggio e selezionare il pulsante per l'invio del messaggio.</span><span class="sxs-lookup"><span data-stu-id="6c20a-197">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="6c20a-198">Il nome e il messaggio vengono visualizzati immediatamente in entrambe le pagine:</span><span class="sxs-lookup"><span data-stu-id="6c20a-198">The name and message are displayed on both pages instantly:</span></span>

   ![::: NO-LOC (SignalR):::::: NO-LOC (webassembly Blazer)::: app di esempio aperta in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="6c20a-200">Virgolette: *Star Trek VI: il paese non individuato* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="6c20a-200">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6c20a-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6c20a-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="6c20a-202">Quando VS Code offre la creazione di un profilo di avvio per l'app Server ( `.vscode/launch.json` ), la `program` voce appare simile alla seguente per puntare all'assembly dell'app ( `{APPLICATION NAME}.Server.dll` ):</span><span class="sxs-lookup"><span data-stu-id="6c20a-202">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

::: moniker range=">= aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. <span data-ttu-id="6c20a-203">Premere <kbd>F5</kbd> per eseguire l'app con il debug o <kbd>CTRL</kbd> + <kbd>F5</kbd> per eseguire l'app senza debug.</span><span class="sxs-lookup"><span data-stu-id="6c20a-203">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="6c20a-204">Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="6c20a-204">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="6c20a-205">Scegliere browser, immettere un nome e un messaggio e selezionare il pulsante per l'invio del messaggio.</span><span class="sxs-lookup"><span data-stu-id="6c20a-205">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="6c20a-206">Il nome e il messaggio vengono visualizzati immediatamente in entrambe le pagine:</span><span class="sxs-lookup"><span data-stu-id="6c20a-206">The name and message are displayed on both pages instantly:</span></span>

   ![::: NO-LOC (SignalR):::::: NO-LOC (webassembly Blazer)::: app di esempio aperta in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="6c20a-208">Virgolette: *Star Trek VI: il paese non individuato* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="6c20a-208">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6c20a-209">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6c20a-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6c20a-210">Nella barra laterale della **soluzione** selezionare il `BlazorSignalRApp.Server` progetto.</span><span class="sxs-lookup"><span data-stu-id="6c20a-210">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="6c20a-211">Premere <kbd>⌘</kbd> + <kbd>↩</kbd> per eseguire l'app con debug o <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> per eseguire l'app senza debug.</span><span class="sxs-lookup"><span data-stu-id="6c20a-211">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="6c20a-212">Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="6c20a-212">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="6c20a-213">Scegliere browser, immettere un nome e un messaggio e selezionare il pulsante per l'invio del messaggio.</span><span class="sxs-lookup"><span data-stu-id="6c20a-213">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="6c20a-214">Il nome e il messaggio vengono visualizzati immediatamente in entrambe le pagine:</span><span class="sxs-lookup"><span data-stu-id="6c20a-214">The name and message are displayed on both pages instantly:</span></span>

   ![::: NO-LOC (SignalR):::::: NO-LOC (webassembly Blazer)::: app di esempio aperta in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="6c20a-216">Virgolette: *Star Trek VI: il paese non individuato* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="6c20a-216">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6c20a-217">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="6c20a-217">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="6c20a-218">In una shell dei comandi eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="6c20a-218">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="6c20a-219">Copiare l'URL dalla barra degli indirizzi, aprire un'altra istanza o scheda del browser e incollare l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="6c20a-219">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="6c20a-220">Scegliere browser, immettere un nome e un messaggio e selezionare il pulsante per l'invio del messaggio.</span><span class="sxs-lookup"><span data-stu-id="6c20a-220">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="6c20a-221">Il nome e il messaggio vengono visualizzati immediatamente in entrambe le pagine:</span><span class="sxs-lookup"><span data-stu-id="6c20a-221">The name and message are displayed on both pages instantly:</span></span>

   ![::: NO-LOC (SignalR):::::: NO-LOC (webassembly Blazer)::: app di esempio aperta in due finestre del browser che mostrano i messaggi scambiati.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="6c20a-223">Virgolette: *Star Trek VI: il paese non individuato* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="6c20a-223">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="6c20a-224">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="6c20a-224">Next steps</span></span>

<span data-ttu-id="6c20a-225">In questa esercitazione sono state illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="6c20a-225">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6c20a-226">Creare un Blazor WebAssembly progetto di app ospitata</span><span class="sxs-lookup"><span data-stu-id="6c20a-226">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="6c20a-227">Aggiungere la SignalR libreria client</span><span class="sxs-lookup"><span data-stu-id="6c20a-227">Add the SignalR client library</span></span>
> * <span data-ttu-id="6c20a-228">Aggiungere un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="6c20a-228">Add a SignalR hub</span></span>
> * <span data-ttu-id="6c20a-229">Aggiungere SignalR Servizi e un endpoint per l' SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="6c20a-229">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="6c20a-230">Aggiungi Razor codice componente per la chat</span><span class="sxs-lookup"><span data-stu-id="6c20a-230">Add Razor component code for chat</span></span>

<span data-ttu-id="6c20a-231">Per altre informazioni sulla creazione di Blazor app, vedere la Blazor documentazione:</span><span class="sxs-lookup"><span data-stu-id="6c20a-231">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="6c20a-232"><xref:blazor/index>
> [Autenticazione del token di porta con Identity Server, WebSocket ed eventi di Server-Sent](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="6c20a-232"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6c20a-233">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="6c20a-233">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="6c20a-234">SignalR negoziazione tra le origini per l'autenticazione</span><span class="sxs-lookup"><span data-stu-id="6c20a-234">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
