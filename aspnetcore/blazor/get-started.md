---
title: Inizia a usare ASP.NET CoreBlazor
author: guardrex
description: Inizia Blazor a creare un' Blazor app con gli strumenti che preferisci.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 6cf1de6e68d04505ce4ba5d18f2a7d0bbe5be333
ms.sourcegitcommit: c19e388c83c981232e6f128d97440262adfe06e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2020
ms.locfileid: "82727651"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="ce517-103">Introduzione a ASP.NET Core Blazer</span><span class="sxs-lookup"><span data-stu-id="ce517-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="ce517-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ce517-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="ce517-105">Per iniziare a usare blazer, seguire le istruzioni per la scelta degli strumenti:</span><span class="sxs-lookup"><span data-stu-id="ce517-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ce517-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ce517-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ce517-107">Per creare app del server blazer, installare la versione più recente di [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) con il carico di lavoro **sviluppo di ASP.NET e Web** .</span><span class="sxs-lookup"><span data-stu-id="ce517-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="ce517-108">Per creare app di webassembly con server blazer e blazer, installare l'anteprima più recente di [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) con il carico di lavoro **sviluppo di ASP.NET e Web** .</span><span class="sxs-lookup"><span data-stu-id="ce517-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="ce517-109">Per informazioni sui due modelli di hosting di Blazer, *Blazer webassembly* e il *Server Blazer*, vedere <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="ce517-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="ce517-110">Installare il modello di anteprima webassembly Blazer eseguendo il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="ce517-110">Install the Blazor WebAssembly Preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```

1. <span data-ttu-id="ce517-111">Creare un nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="ce517-111">Create a new project.</span></span>

1. <span data-ttu-id="ce517-112">Selezionare **app Blazer**.</span><span class="sxs-lookup"><span data-stu-id="ce517-112">Select **Blazor App**.</span></span> <span data-ttu-id="ce517-113">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="ce517-113">Select **Next**.</span></span>

1. <span data-ttu-id="ce517-114">Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito.</span><span class="sxs-lookup"><span data-stu-id="ce517-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="ce517-115">Confermare che la voce relativa al **percorso** sia corretta o specificare un percorso per il progetto.</span><span class="sxs-lookup"><span data-stu-id="ce517-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="ce517-116">Selezionare **Create** (Crea).</span><span class="sxs-lookup"><span data-stu-id="ce517-116">Select **Create**.</span></span>

1. <span data-ttu-id="ce517-117">Per un'esperienza di webassembly blazer (Visual Studio 16,6 Preview 2 o versione successiva), scegliere il modello di **app Webassembly Blazer** .</span><span class="sxs-lookup"><span data-stu-id="ce517-117">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="ce517-118">Per un'esperienza del server blazer (Visual Studio 16,4 o versione successiva), scegliere il modello di **app del server Blazer** .</span><span class="sxs-lookup"><span data-stu-id="ce517-118">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="ce517-119">Selezionare **Create** (Crea).</span><span class="sxs-lookup"><span data-stu-id="ce517-119">Select **Create**.</span></span>

1. <span data-ttu-id="ce517-120">Premere <kbd>CTRL</kbd>+<kbd>F5</kbd> per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="ce517-120">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ce517-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ce517-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="ce517-122">Installare [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="ce517-122">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="ce517-123">Facoltativamente, installare il modello di anteprima di [Webassembly Blazer](xref:blazor/hosting-models#blazor-webassembly) eseguendo il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="ce517-123">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```
   
   <span data-ttu-id="ce517-124">Per informazioni sui due modelli di hosting di Blazer, *Blazer webassembly* e il *Server Blazer*, vedere <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="ce517-124">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

   > [!NOTE]
   > <span data-ttu-id="ce517-125">Il [.NET Core SDK versione 3.1.201 o successiva](https://dotnet.microsoft.com/download/dotnet-core/3.1) è **necessario** per usare il modello di assembly webassembly 3,2 Preview.</span><span class="sxs-lookup"><span data-stu-id="ce517-125">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview Blazor WebAssembly template.</span></span> <span data-ttu-id="ce517-126">Verificare la versione di .NET Core SDK installata eseguendo `dotnet --version` in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="ce517-126">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="ce517-127">Installare [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="ce517-127">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="ce517-128">Installare la versione più recente [di C# per Visual Studio Code estensione](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e l'estensione del [debugger JavaScript (notturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con `debug.javascript.usePreview` impostato su `true`.</span><span class="sxs-lookup"><span data-stu-id="ce517-128">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="ce517-129">Per un'esperienza del server blazer, eseguire il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="ce517-129">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="ce517-130">Per un'esperienza di webassembly blazer, eseguire il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="ce517-130">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

1. <span data-ttu-id="ce517-131">Aprire la cartella *WebApplication1* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="ce517-131">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="ce517-132">Le richieste dell'IDE aggiungono risorse per compilare ed eseguire il debug del progetto.</span><span class="sxs-lookup"><span data-stu-id="ce517-132">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="ce517-133">Selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="ce517-133">Select **Yes**.</span></span>

1. <span data-ttu-id="ce517-134">Con il server blazer, eseguire l'app usando il debugger Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="ce517-134">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="ce517-135">Con l'assembly Web Blazer, avviare l'app usando la configurazione di avvio di **.NET Core (autonomia di Blaze)** e quindi avviare il browser usando l' **assembly Web di debug di .NET Core in Chrome** Launch Configuration (richiede Chrome).</span><span class="sxs-lookup"><span data-stu-id="ce517-135">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="ce517-136">Per altre informazioni, vedere <xref:blazor/debug#visual-studio-code>.</span><span class="sxs-lookup"><span data-stu-id="ce517-136">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="ce517-137">In un browser passare a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="ce517-137">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ce517-138">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="ce517-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ce517-139">Il server blazer è supportato in Visual Studio per Mac.</span><span class="sxs-lookup"><span data-stu-id="ce517-139">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="ce517-140">Il webassembly Blazer non è supportato in questo momento.</span><span class="sxs-lookup"><span data-stu-id="ce517-140">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="ce517-141">Per creare app webassembly Blazer in macOS, seguire le istruzioni nella scheda **interfaccia della riga di comando di .NET Core** . Per informazioni sui due modelli di hosting di Blazer, *Blazer webassembly* e il *Server Blazer*, vedere <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="ce517-141">To create Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab. For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="ce517-142">Installare [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="ce517-142">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="ce517-143">Selezionare **file** > **nuova soluzione** o creare un **nuovo** progetto dalla **finestra Start**.</span><span class="sxs-lookup"><span data-stu-id="ce517-143">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="ce517-144">Nella barra laterale selezionare app **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="ce517-144">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="ce517-145">Selezionare il modello **applicazione server Blazer** .</span><span class="sxs-lookup"><span data-stu-id="ce517-145">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="ce517-146">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="ce517-146">Select **Next**.</span></span>

1. <span data-ttu-id="ce517-147">Verificare le configurazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="ce517-147">Confirm the following configurations:</span></span>

   * <span data-ttu-id="ce517-148">Il **Framework di destinazione** è impostato su **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="ce517-148">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="ce517-149">**L'autenticazione** è impostata su **Nessuna autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="ce517-149">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="ce517-150">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="ce517-150">Select **Next**.</span></span>

1. <span data-ttu-id="ce517-151">Nel campo **nome progetto** assegnare un nome all'app `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="ce517-151">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="ce517-152">Selezionare **Create** (Crea).</span><span class="sxs-lookup"><span data-stu-id="ce517-152">Select **Create**.</span></span>

1. <span data-ttu-id="ce517-153">Selezionare **Esegui** > **Avvia senza eseguire debug** per eseguire l'app *senza il debugger*.</span><span class="sxs-lookup"><span data-stu-id="ce517-153">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="ce517-154">Il debug non è al momento supportato.</span><span class="sxs-lookup"><span data-stu-id="ce517-154">Debugging isn't supported at this time.</span></span>

<!-- HOLD FOR 8.6 GA

1. Select **File** > **New Solution** or create a **New** project from the **Start Window**.

1. In the sidebar, select **Web and Console** > **App**.

1. For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Next**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Confirm the following configurations:

   * **Target Framework** set to **.NET Core 3.1**.
   * **Authentication** set to **No Authentication**.
   
   Select **Next**.

1. In the **Project Name** field, name the app `WebApplication1`. Select **Create**.

1. Select **Run** > **Start Without Debugging** to run the app *without the debugger*. Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.

-->

<span data-ttu-id="ce517-155">Se viene visualizzato un messaggio per considerare attendibile il certificato di sviluppo, considerare attendibile il certificato e continuare.</span><span class="sxs-lookup"><span data-stu-id="ce517-155">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="ce517-156">Per considerare attendibile il certificato, è necessario specificare le password dell'utente e del keychain.</span><span class="sxs-lookup"><span data-stu-id="ce517-156">The user and keychain passwords are required to trust the certificate.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ce517-157">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="ce517-157">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="ce517-158">Installare [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="ce517-158">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="ce517-159">Facoltativamente, installare il modello di anteprima di webassembly Blazer eseguendo il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="ce517-159">Optionally install the Blazor WebAssembly preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```
   
   <span data-ttu-id="ce517-160">Per informazioni sui due modelli di hosting di Blazer, *Blazer webassembly* e il *Server Blazer*, vedere <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="ce517-160">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

   > [!NOTE]
   > <span data-ttu-id="ce517-161">Il [.NET Core SDK versione 3.1.201 o successiva](https://dotnet.microsoft.com/download/dotnet-core/3.1) è **necessario** per usare il modello di assembly webassembly 3,2 Preview.</span><span class="sxs-lookup"><span data-stu-id="ce517-161">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview Blazor WebAssembly template.</span></span> <span data-ttu-id="ce517-162">Verificare la versione di .NET Core SDK installata eseguendo `dotnet --version` in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="ce517-162">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="ce517-163">Per un'esperienza del server blazer, eseguire i comandi seguenti in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="ce517-163">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="ce517-164">Per un'esperienza di webassembly blazer, eseguire i comandi seguenti in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="ce517-164">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. <span data-ttu-id="ce517-165">In un browser passare a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="ce517-165">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="ce517-166">Nelle schede della barra laterale sono disponibili più pagine:</span><span class="sxs-lookup"><span data-stu-id="ce517-166">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="ce517-167">Home</span><span class="sxs-lookup"><span data-stu-id="ce517-167">Home</span></span>
* <span data-ttu-id="ce517-168">Contatore</span><span class="sxs-lookup"><span data-stu-id="ce517-168">Counter</span></span>
* <span data-ttu-id="ce517-169">Recuperare i dati</span><span class="sxs-lookup"><span data-stu-id="ce517-169">Fetch data</span></span>

<span data-ttu-id="ce517-170">Nella pagina Counter selezionare il pulsante **Click me** per incrementare il contatore senza un aggiornamento della pagina.</span><span class="sxs-lookup"><span data-stu-id="ce517-170">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="ce517-171">Per incrementare un contatore in una pagina Web è in genere necessario scrivere JavaScript Blazor , ma con è possibile usare C#.</span><span class="sxs-lookup"><span data-stu-id="ce517-171">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="ce517-172">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="ce517-172">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="ce517-173">Una richiesta di `/counter` nel browser, come specificato dalla `@page` direttiva nella parte superiore, determina il rendering del `Counter` contenuto da parte del componente.</span><span class="sxs-lookup"><span data-stu-id="ce517-173">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="ce517-174">I componenti eseguono il rendering in una rappresentazione in memoria della struttura di rendering che può quindi essere utilizzata per aggiornare l'interfaccia utente in modo flessibile ed efficiente.</span><span class="sxs-lookup"><span data-stu-id="ce517-174">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="ce517-175">Ogni volta che viene selezionato il pulsante **Click me** :</span><span class="sxs-lookup"><span data-stu-id="ce517-175">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="ce517-176">L' `onclick` evento viene generato.</span><span class="sxs-lookup"><span data-stu-id="ce517-176">The `onclick` event is fired.</span></span>
* <span data-ttu-id="ce517-177">Viene chiamato il metodo `IncrementCount` .</span><span class="sxs-lookup"><span data-stu-id="ce517-177">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="ce517-178">`currentCount` Viene incrementato.</span><span class="sxs-lookup"><span data-stu-id="ce517-178">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="ce517-179">Il rendering del componente viene eseguito nuovamente.</span><span class="sxs-lookup"><span data-stu-id="ce517-179">The component is rendered again.</span></span>

<span data-ttu-id="ce517-180">Il runtime confronta il nuovo contenuto con il contenuto precedente e applica solo il contenuto modificato al Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="ce517-180">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="ce517-181">Aggiungere un componente a un altro componente usando la sintassi HTML.</span><span class="sxs-lookup"><span data-stu-id="ce517-181">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="ce517-182">Ad esempio, aggiungere il `Counter` componente alla Home page dell'app aggiungendo un `<Counter />` elemento al `Index` componente.</span><span class="sxs-lookup"><span data-stu-id="ce517-182">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="ce517-183">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="ce517-183">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="ce517-184">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="ce517-184">Run the app.</span></span> <span data-ttu-id="ce517-185">La Home page presenta il proprio contatore fornito dal `Counter` componente.</span><span class="sxs-lookup"><span data-stu-id="ce517-185">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="ce517-186">I parametri del componente vengono specificati utilizzando attributi o [contenuto figlio](xref:blazor/components#child-content), che consentono di impostare le proprietà per il componente figlio.</span><span class="sxs-lookup"><span data-stu-id="ce517-186">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="ce517-187">Per aggiungere un parametro al `Counter` componente, aggiornare il `@code` blocco del componente:</span><span class="sxs-lookup"><span data-stu-id="ce517-187">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="ce517-188">Aggiungere una proprietà pubblica per `IncrementAmount` con un `[Parameter]` attributo.</span><span class="sxs-lookup"><span data-stu-id="ce517-188">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="ce517-189">Modificare il metodo `IncrementCount` per usare `IncrementAmount` quando si aumenta il valore di `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="ce517-189">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="ce517-190">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="ce517-190">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="ce517-191">Specificare `IncrementAmount` nell' `<Counter>` elemento del `Index` componente usando un attributo.</span><span class="sxs-lookup"><span data-stu-id="ce517-191">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="ce517-192">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="ce517-192">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="ce517-193">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="ce517-193">Run the app.</span></span> <span data-ttu-id="ce517-194">Il `Index` componente dispone di un contatore specifico che aumenta di dieci ogni volta che viene selezionato il pulsante **Click me** .</span><span class="sxs-lookup"><span data-stu-id="ce517-194">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="ce517-195">Il `Counter` componente (*Counter. Razor*) a `/counter` continua a incrementare di uno.</span><span class="sxs-lookup"><span data-stu-id="ce517-195">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ce517-196">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="ce517-196">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="ce517-197">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="ce517-197">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
