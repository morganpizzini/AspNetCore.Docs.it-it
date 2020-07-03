---
title: Strumenti per ASP.NET CoreBlazor
author: guardrex
description: Informazioni sugli strumenti disponibili per la compilazione di Blazor app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/tooling
ms.openlocfilehash: 3ff610225c798624b7ead7d365924ae3d193829d
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944951"
---
<!-- zone_pivot_groups: operating-systems -->

# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="41499-103">Strumenti per ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="41499-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="41499-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="41499-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="41499-105">Selezionare gli strumenti per la piattaforma:</span><span class="sxs-lookup"><span data-stu-id="41499-105">Select the tooling for your platform:</span></span>

* <span data-ttu-id="41499-106">Windows: selezionare la scheda **Visual Studio** .</span><span class="sxs-lookup"><span data-stu-id="41499-106">Windows: Select the **Visual Studio** tab.</span></span>
* <span data-ttu-id="41499-107">Linux: selezionare la scheda **Visual Studio Code** .</span><span class="sxs-lookup"><span data-stu-id="41499-107">Linux: Select the **Visual Studio Code** tab.</span></span>
* <span data-ttu-id="41499-108">macOS: selezionare la scheda **Visual Studio per Mac** .</span><span class="sxs-lookup"><span data-stu-id="41499-108">macOS: Select the **Visual Studio for Mac** tab.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="41499-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41499-109">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="41499-110">Installare la versione più recente di [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) con il carico di lavoro **sviluppo di ASP.NET e Web** .</span><span class="sxs-lookup"><span data-stu-id="41499-110">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="41499-111">Creare un nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="41499-111">Create a new project.</span></span>

1. <span data-ttu-id="41499-112">Selezionare \*\* Blazor app\*\*.</span><span class="sxs-lookup"><span data-stu-id="41499-112">Select **Blazor App**.</span></span> <span data-ttu-id="41499-113">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="41499-113">Select **Next**.</span></span>

1. <span data-ttu-id="41499-114">Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito.</span><span class="sxs-lookup"><span data-stu-id="41499-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="41499-115">Confermare che la voce relativa al **percorso** sia corretta o specificare un percorso per il progetto.</span><span class="sxs-lookup"><span data-stu-id="41499-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="41499-116">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="41499-116">Select **Create**.</span></span>

1. <span data-ttu-id="41499-117">Per un' Blazor WebAssembly esperienza, scegliere il modello \*\* Blazor WebAssembly app\*\* .</span><span class="sxs-lookup"><span data-stu-id="41499-117">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="41499-118">Per un' Blazor Server esperienza, scegliere il modello \*\* Blazor Server app\*\* .</span><span class="sxs-lookup"><span data-stu-id="41499-118">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="41499-119">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="41499-119">Select **Create**.</span></span>

   <span data-ttu-id="41499-120">Per informazioni sui due Blazor modelli di hosting *Blazor WebAssembly* e *Blazor Server* , vedere <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="41499-120">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="41499-121">Premere <kbd>CTRL</kbd> + <kbd>F5</kbd> per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="41499-121">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="41499-122">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="41499-122">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="41499-123">Installare la versione più recente di [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="41499-123">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="41499-124">Se in precedenza è stato installato l'SDK, è possibile determinare la versione installata eseguendo il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="41499-124">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="41499-125">Installare la versione più recente di [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="41499-125">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="41499-126">Installare la versione più recente [di C# per Visual Studio Code estensione](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e l'estensione del [debugger JavaScript (notturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con `debug.javascript.usePreview` impostato su `true` .</span><span class="sxs-lookup"><span data-stu-id="41499-126">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   <span data-ttu-id="41499-127">Per impostare `debug.javascript.usePreview` `true` l'utilizzo dell'interfaccia utente di vs Code **File**, aprire  >  **Preferences**  >  **Impostazioni** preferenze file e cercare `debug javascript use preview` .</span><span class="sxs-lookup"><span data-stu-id="41499-127">To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`.</span></span> <span data-ttu-id="41499-128">Selezionare la casella di controllo **Usa il nuovo debugger JavaScript in anteprima per Node.js e Chrome**.</span><span class="sxs-lookup"><span data-stu-id="41499-128">Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.</span></span>

1. <span data-ttu-id="41499-129">Per un' Blazor WebAssembly esperienza, eseguire il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="41499-129">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="41499-130">Per un' Blazor Server esperienza, eseguire il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="41499-130">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="41499-131">Per informazioni sui due Blazor modelli di hosting *Blazor WebAssembly* e *Blazor Server* , vedere <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="41499-131">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="41499-132">Aprire la cartella `WebApplication1` in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="41499-132">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="41499-133">Le richieste dell'IDE aggiungono risorse per compilare ed eseguire il debug del progetto.</span><span class="sxs-lookup"><span data-stu-id="41499-133">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="41499-134">Selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="41499-134">Select **Yes**.</span></span>

1. <span data-ttu-id="41499-135">Premere <kbd>CTRL</kbd> + <kbd>F5</kbd> per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="41499-135">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="41499-136">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="41499-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="41499-137">Installare [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="41499-137">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="41499-138">Selezionare **file**  >  **nuova soluzione** o creare un **nuovo** progetto dalla **finestra Start**.</span><span class="sxs-lookup"><span data-stu-id="41499-138">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="41499-139">Nella barra laterale selezionare app **Web e console**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="41499-139">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="41499-140">Per un' Blazor WebAssembly esperienza, scegliere il modello \*\* Blazor WebAssembly app\*\* .</span><span class="sxs-lookup"><span data-stu-id="41499-140">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="41499-141">Per un' Blazor Server esperienza, scegliere il modello \*\* Blazor Server app\*\* .</span><span class="sxs-lookup"><span data-stu-id="41499-141">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="41499-142">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="41499-142">Select **Next**.</span></span>

   <span data-ttu-id="41499-143">Per informazioni sui due Blazor modelli di hosting *Blazor WebAssembly* e *Blazor Server* , vedere <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="41499-143">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="41499-144">Verificare le configurazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="41499-144">Confirm the following configurations:</span></span>

   * <span data-ttu-id="41499-145">Il **Framework di destinazione** è impostato su **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="41499-145">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="41499-146">**L'autenticazione** è impostata su **Nessuna autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="41499-146">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="41499-147">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="41499-147">Select **Next**.</span></span>

1. <span data-ttu-id="41499-148">Nel campo **nome progetto** assegnare un nome all'app `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="41499-148">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="41499-149">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="41499-149">Select **Create**.</span></span>

1. <span data-ttu-id="41499-150">Selezionare **Esegui**  >  **Avvia senza eseguire debug** per eseguire l'app *senza il debugger*.</span><span class="sxs-lookup"><span data-stu-id="41499-150">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="41499-151">Eseguire l'app con **Esegui**  >  **debug Avvia debug** o il pulsante Esegui (&#9654;) per eseguire l'app *con il debugger*.</span><span class="sxs-lookup"><span data-stu-id="41499-151">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="41499-152">Se viene visualizzato un messaggio per considerare attendibile il certificato di sviluppo, considerare attendibile il certificato e continuare.</span><span class="sxs-lookup"><span data-stu-id="41499-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="41499-153">Per considerare attendibile il certificato, è necessario specificare le password dell'utente e del keychain.</span><span class="sxs-lookup"><span data-stu-id="41499-153">The user and keychain passwords are required to trust the certificate.</span></span>

---

<!--

::: zone pivot="os-windows"

1. Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.

1. Create a new project.

1. Select **Blazor App**. Select **Next**.

1. Provide a project name in the **Project name** field or accept the default project name. Confirm the **Location** entry is correct or provide a location for the project. Select **Create**.

1. For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Create**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.

::: zone-end

::: zone pivot="os-linux"

1. Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:

   ```dotnetcli
   dotnet --version
   ```

1. Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).

1. Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.

   To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`. Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.

1. For a Blazor WebAssembly experience, execute the following command in a command shell:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   For a Blazor Server experience, execute the following command in a command shell:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Open the `WebApplication1` folder in Visual Studio Code.

1. The IDE requests that you add assets to build and debug the project. Select **Yes**.

1. Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.

::: zone-end

::: zone pivot="os-macos"

1. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Select **File** > **New Solution** or create a **New** project from the **Start Window**.

1. In the sidebar, select **Web and Console** > **App**.

   For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Next**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Confirm the following configurations:

   * **Target Framework** set to **.NET Core 3.1**.
   * **Authentication** set to **No Authentication**.
   
   Select **Next**.

1. In the **Project Name** field, name the app `WebApplication1`. Select **Create**.

1. Select **Run** > **Start Without Debugging** to run the app *without the debugger*. Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.

If a prompt appears to trust the development certificate, trust the certificate and continue. The user and keychain passwords are required to trust the certificate.

::: zone-end

-->
