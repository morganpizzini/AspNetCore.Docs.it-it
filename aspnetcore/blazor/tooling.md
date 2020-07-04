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
zone_pivot_groups: operating-systems-set-one
ms.openlocfilehash: 538257597ec5c6c705d8280a817c409debe22224
ms.sourcegitcommit: c6467f86c09b1f608b09d37d33c8c43de7ae8bc7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2020
ms.locfileid: "85946808"
---
# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="0258d-103">Strumenti per ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="0258d-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="0258d-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0258d-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="os-windows"

1. <span data-ttu-id="0258d-105">Installare la versione più recente di [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) con il carico di lavoro **sviluppo di ASP.NET e Web** .</span><span class="sxs-lookup"><span data-stu-id="0258d-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="0258d-106">Creare un nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="0258d-106">Create a new project.</span></span>

1. <span data-ttu-id="0258d-107">Selezionare \*\* Blazor app\*\*.</span><span class="sxs-lookup"><span data-stu-id="0258d-107">Select **Blazor App**.</span></span> <span data-ttu-id="0258d-108">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="0258d-108">Select **Next**.</span></span>

1. <span data-ttu-id="0258d-109">Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito.</span><span class="sxs-lookup"><span data-stu-id="0258d-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="0258d-110">Confermare che la voce relativa al **percorso** sia corretta o specificare un percorso per il progetto.</span><span class="sxs-lookup"><span data-stu-id="0258d-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="0258d-111">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="0258d-111">Select **Create**.</span></span>

1. <span data-ttu-id="0258d-112">Per un' Blazor WebAssembly esperienza, scegliere il modello \*\* Blazor WebAssembly app\*\* .</span><span class="sxs-lookup"><span data-stu-id="0258d-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="0258d-113">Per un' Blazor Server esperienza, scegliere il modello \*\* Blazor Server app\*\* .</span><span class="sxs-lookup"><span data-stu-id="0258d-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="0258d-114">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="0258d-114">Select **Create**.</span></span>

   <span data-ttu-id="0258d-115">Per informazioni sui due Blazor modelli di hosting *Blazor WebAssembly* e *Blazor Server* , vedere <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="0258d-115">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="0258d-116">Premere <kbd>CTRL</kbd> + <kbd>F5</kbd> per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="0258d-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

::: zone-end

::: zone pivot="os-linux"

1. <span data-ttu-id="0258d-117">Installare la versione più recente di [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="0258d-117">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="0258d-118">Se in precedenza è stato installato l'SDK, è possibile determinare la versione installata eseguendo il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="0258d-118">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="0258d-119">Installare la versione più recente di [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="0258d-119">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="0258d-120">Installare la versione più recente [di C# per Visual Studio Code estensione](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e l'estensione del [debugger JavaScript (notturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con `debug.javascript.usePreview` impostato su `true` .</span><span class="sxs-lookup"><span data-stu-id="0258d-120">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   <span data-ttu-id="0258d-121">Per impostare `debug.javascript.usePreview` `true` l'utilizzo dell'interfaccia utente di vs Code **File**, aprire  >  **Preferences**  >  **Impostazioni** preferenze file e cercare `debug javascript use preview` .</span><span class="sxs-lookup"><span data-stu-id="0258d-121">To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`.</span></span> <span data-ttu-id="0258d-122">Selezionare la casella di controllo **Usa il nuovo debugger JavaScript in anteprima per Node.js e Chrome**.</span><span class="sxs-lookup"><span data-stu-id="0258d-122">Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.</span></span>

1. <span data-ttu-id="0258d-123">Per un' Blazor WebAssembly esperienza, eseguire il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="0258d-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="0258d-124">Per un' Blazor Server esperienza, eseguire il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="0258d-124">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="0258d-125">Per informazioni sui due Blazor modelli di hosting *Blazor WebAssembly* e *Blazor Server* , vedere <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="0258d-125">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="0258d-126">Aprire la cartella `WebApplication1` in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="0258d-126">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="0258d-127">Le richieste dell'IDE aggiungono risorse per compilare ed eseguire il debug del progetto.</span><span class="sxs-lookup"><span data-stu-id="0258d-127">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="0258d-128">Selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="0258d-128">Select **Yes**.</span></span>

1. <span data-ttu-id="0258d-129">Premere <kbd>CTRL</kbd> + <kbd>F5</kbd> per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="0258d-129">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

::: zone-end

::: zone pivot="os-macos"

1. <span data-ttu-id="0258d-130">Installare [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="0258d-130">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="0258d-131">Selezionare **file**  >  **nuova soluzione** o creare un **nuovo** progetto dalla **finestra Start**.</span><span class="sxs-lookup"><span data-stu-id="0258d-131">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="0258d-132">Nella barra laterale selezionare app **Web e console**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="0258d-132">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="0258d-133">Per un' Blazor WebAssembly esperienza, scegliere il modello \*\* Blazor WebAssembly app\*\* .</span><span class="sxs-lookup"><span data-stu-id="0258d-133">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="0258d-134">Per un' Blazor Server esperienza, scegliere il modello \*\* Blazor Server app\*\* .</span><span class="sxs-lookup"><span data-stu-id="0258d-134">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="0258d-135">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="0258d-135">Select **Next**.</span></span>

   <span data-ttu-id="0258d-136">Per informazioni sui due Blazor modelli di hosting *Blazor WebAssembly* e *Blazor Server* , vedere <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="0258d-136">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="0258d-137">Verificare le configurazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="0258d-137">Confirm the following configurations:</span></span>

   * <span data-ttu-id="0258d-138">Il **Framework di destinazione** è impostato su **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="0258d-138">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="0258d-139">**L'autenticazione** è impostata su **Nessuna autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="0258d-139">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="0258d-140">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="0258d-140">Select **Next**.</span></span>

1. <span data-ttu-id="0258d-141">Nel campo **nome progetto** assegnare un nome all'app `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="0258d-141">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="0258d-142">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="0258d-142">Select **Create**.</span></span>

1. <span data-ttu-id="0258d-143">Selezionare **Esegui**  >  **Avvia senza eseguire debug** per eseguire l'app *senza il debugger*.</span><span class="sxs-lookup"><span data-stu-id="0258d-143">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="0258d-144">Eseguire l'app con **Esegui**  >  **debug Avvia debug** o il pulsante Esegui (&#9654;) per eseguire l'app *con il debugger*.</span><span class="sxs-lookup"><span data-stu-id="0258d-144">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="0258d-145">Se viene visualizzato un messaggio per considerare attendibile il certificato di sviluppo, considerare attendibile il certificato e continuare.</span><span class="sxs-lookup"><span data-stu-id="0258d-145">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="0258d-146">Per considerare attendibile il certificato, è necessario specificare le password dell'utente e del keychain.</span><span class="sxs-lookup"><span data-stu-id="0258d-146">The user and keychain passwords are required to trust the certificate.</span></span>

::: zone-end
