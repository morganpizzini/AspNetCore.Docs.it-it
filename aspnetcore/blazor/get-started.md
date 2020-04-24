---
title: Inizia a usare ASP.NET CoreBlazor
author: guardrex
description: Inizia Blazor a creare un' Blazor app con gli strumenti che preferisci.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 2f10b00adce31c020d46d107c087159c17341beb
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111071"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="06456-103">Introduzione a ASP.NET Core Blazer</span><span class="sxs-lookup"><span data-stu-id="06456-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="06456-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="06456-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="06456-105">Per iniziare a usare blazer, seguire le istruzioni per la scelta degli strumenti:</span><span class="sxs-lookup"><span data-stu-id="06456-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="06456-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06456-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="06456-107">Per creare app del server blazer, installare la versione più recente di [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) con il carico di lavoro **sviluppo di ASP.NET e Web** .</span><span class="sxs-lookup"><span data-stu-id="06456-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="06456-108">Per creare app di webassembly con server blazer e blazer, installare l'anteprima più recente di [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) con il carico di lavoro **sviluppo di ASP.NET e Web** .</span><span class="sxs-lookup"><span data-stu-id="06456-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="06456-109">Per informazioni sui due modelli di hosting di Blazer, *Blazer webassembly* e il *Server Blazer*, vedere <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="06456-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="06456-110">Installare il modello di anteprima [Webassembly Blazer](xref:blazor/hosting-models#blazor-webassembly) eseguendo il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="06456-110">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

1. <span data-ttu-id="06456-111">Creare un nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="06456-111">Create a new project.</span></span>

1. <span data-ttu-id="06456-112">Selezionare **app Blazer**.</span><span class="sxs-lookup"><span data-stu-id="06456-112">Select **Blazor App**.</span></span> <span data-ttu-id="06456-113">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="06456-113">Select **Next**.</span></span>

1. <span data-ttu-id="06456-114">Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito.</span><span class="sxs-lookup"><span data-stu-id="06456-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="06456-115">Confermare che la voce relativa al **percorso** sia corretta o specificare un percorso per il progetto.</span><span class="sxs-lookup"><span data-stu-id="06456-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="06456-116">Seleziona **Crea**.</span><span class="sxs-lookup"><span data-stu-id="06456-116">Select **Create**.</span></span>

1. <span data-ttu-id="06456-117">Per un'esperienza di webassembly blazer (Visual Studio 16,6 Preview 2 o versione successiva), scegliere il modello di **app Webassembly Blazer** .</span><span class="sxs-lookup"><span data-stu-id="06456-117">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="06456-118">Per un'esperienza del server blazer (Visual Studio 16,4 o versione successiva), scegliere il modello di **app del server Blazer** .</span><span class="sxs-lookup"><span data-stu-id="06456-118">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="06456-119">Seleziona **Crea**.</span><span class="sxs-lookup"><span data-stu-id="06456-119">Select **Create**.</span></span>

1. <span data-ttu-id="06456-120">Premere <kbd>CTRL</kbd>+<kbd>F5</kbd> per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="06456-120">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="06456-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="06456-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="06456-122">Installare [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="06456-122">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="06456-123">Facoltativamente, installare il modello di anteprima di [Webassembly Blazer](xref:blazor/hosting-models#blazor-webassembly) eseguendo il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="06456-123">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > <span data-ttu-id="06456-124">Per usare il modello di assembly webassembly 3,2 Preview 4 Blazer è **necessario** il [.NET Core SDK versione 3.1.201 o successiva](https://dotnet.microsoft.com/download/dotnet-core/3.1) .</span><span class="sxs-lookup"><span data-stu-id="06456-124">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="06456-125">Verificare la versione di .NET Core SDK installata eseguendo `dotnet --version` in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="06456-125">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="06456-126">Installare [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="06456-126">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="06456-127">Installare la versione più recente [di C# per Visual Studio Code estensione](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e l'estensione del [debugger JavaScript (notturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con `debug.javascript.usePreview` impostato su `true`.</span><span class="sxs-lookup"><span data-stu-id="06456-127">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="06456-128">Per un'esperienza del server blazer, eseguire il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="06456-128">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="06456-129">Per un'esperienza di webassembly blazer, eseguire il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="06456-129">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="06456-130">Per informazioni sui due modelli di hosting blazer, *Server Blazer* e *webassembly Blazer*, vedere <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="06456-130">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="06456-131">Aprire la cartella *WebApplication1* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="06456-131">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="06456-132">Le richieste dell'IDE aggiungono risorse per compilare ed eseguire il debug del progetto.</span><span class="sxs-lookup"><span data-stu-id="06456-132">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="06456-133">Selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="06456-133">Select **Yes**.</span></span>

1. <span data-ttu-id="06456-134">Con il server blazer, eseguire l'app usando il debugger Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="06456-134">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="06456-135">Con l'assembly Web Blazer, avviare l'app usando la configurazione di avvio di **.NET Core (autonomia di Blaze)** e quindi avviare il browser usando l' **assembly Web di debug di .NET Core in Chrome** Launch Configuration (richiede Chrome).</span><span class="sxs-lookup"><span data-stu-id="06456-135">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="06456-136">Per altre informazioni, vedere <xref:blazor/debug#visual-studio-code>.</span><span class="sxs-lookup"><span data-stu-id="06456-136">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="06456-137">In un browser passare a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="06456-137">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="06456-138">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="06456-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="06456-139">Il server blazer è supportato in Visual Studio per Mac.</span><span class="sxs-lookup"><span data-stu-id="06456-139">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="06456-140">Il webassembly Blazer non è supportato in questo momento.</span><span class="sxs-lookup"><span data-stu-id="06456-140">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="06456-141">Per compilare app webassembly di Blazer in macOS, seguire le istruzioni nella scheda **interfaccia della riga di comando di .NET Core** .</span><span class="sxs-lookup"><span data-stu-id="06456-141">To build Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab.</span></span>

1. <span data-ttu-id="06456-142">Installare [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="06456-142">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="06456-143">Selezionare **file** > **nuova soluzione** o creare un **nuovo progetto**.</span><span class="sxs-lookup"><span data-stu-id="06456-143">Select **File** > **New Solution** or create a **New Project**.</span></span>

1. <span data-ttu-id="06456-144">Nella barra laterale selezionare app **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="06456-144">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="06456-145">Selezionare il modello **applicazione server Blazer** .</span><span class="sxs-lookup"><span data-stu-id="06456-145">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="06456-146">Seleziona **Crea**.</span><span class="sxs-lookup"><span data-stu-id="06456-146">Select **Create**.</span></span>

   <span data-ttu-id="06456-147">Per informazioni sul modello di hosting del server blazer, <xref:blazor/hosting-models>vedere.</span><span class="sxs-lookup"><span data-stu-id="06456-147">For information on the Blazor Server hosting model, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="06456-148">Impostare il **Framework di destinazione** su **.NET Core 3,1** e selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="06456-148">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

1. <span data-ttu-id="06456-149">Nel campo **nome progetto** assegnare un nome all'app `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="06456-149">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="06456-150">Seleziona **Crea**.</span><span class="sxs-lookup"><span data-stu-id="06456-150">Select **Create**.</span></span>

1. <span data-ttu-id="06456-151">Selezionare **Esegui** > **esecuzione senza debug** per eseguire l'app *senza il debugger*.</span><span class="sxs-lookup"><span data-stu-id="06456-151">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="06456-152">Eseguire l'app con **Avvia debug** per eseguire l'app *con il debugger*.</span><span class="sxs-lookup"><span data-stu-id="06456-152">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

<span data-ttu-id="06456-153">Se viene visualizzato un messaggio per considerare attendibile il certificato di sviluppo, considerare attendibile il certificato e continuare.</span><span class="sxs-lookup"><span data-stu-id="06456-153">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="06456-154">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="06456-154">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="06456-155">Installare [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="06456-155">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="06456-156">Facoltativamente, installare il modello di anteprima di [Webassembly Blazer](xref:blazor/hosting-models#blazor-webassembly) eseguendo il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="06456-156">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > <span data-ttu-id="06456-157">Per usare il modello di assembly webassembly 3,2 Preview 4 Blazer è **necessario** il [.NET Core SDK versione 3.1.201 o successiva](https://dotnet.microsoft.com/download/dotnet-core/3.1) .</span><span class="sxs-lookup"><span data-stu-id="06456-157">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="06456-158">Verificare la versione di .NET Core SDK installata eseguendo `dotnet --version` in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="06456-158">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="06456-159">Per un'esperienza del server blazer, eseguire i comandi seguenti in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="06456-159">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="06456-160">Per un'esperienza di webassembly blazer, eseguire i comandi seguenti in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="06456-160">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="06456-161">Per informazioni sui due modelli di hosting blazer, *Server Blazer* e *webassembly Blazer*, vedere <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="06456-161">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="06456-162">In un browser passare a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="06456-162">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="06456-163">Nelle schede della barra laterale sono disponibili più pagine:</span><span class="sxs-lookup"><span data-stu-id="06456-163">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="06456-164">Home</span><span class="sxs-lookup"><span data-stu-id="06456-164">Home</span></span>
* <span data-ttu-id="06456-165">Contatore</span><span class="sxs-lookup"><span data-stu-id="06456-165">Counter</span></span>
* <span data-ttu-id="06456-166">Recuperare i dati</span><span class="sxs-lookup"><span data-stu-id="06456-166">Fetch data</span></span>

<span data-ttu-id="06456-167">Nella pagina Counter selezionare il pulsante **Click me** per incrementare il contatore senza un aggiornamento della pagina.</span><span class="sxs-lookup"><span data-stu-id="06456-167">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="06456-168">Per incrementare un contatore in una pagina Web è in genere necessario scrivere JavaScript Blazor , ma con è possibile usare C#.</span><span class="sxs-lookup"><span data-stu-id="06456-168">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="06456-169">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="06456-169">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="06456-170">Una richiesta di `/counter` nel browser, come specificato dalla `@page` direttiva nella parte superiore, determina il rendering del `Counter` contenuto da parte del componente.</span><span class="sxs-lookup"><span data-stu-id="06456-170">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="06456-171">I componenti eseguono il rendering in una rappresentazione in memoria della struttura di rendering che può quindi essere utilizzata per aggiornare l'interfaccia utente in modo flessibile ed efficiente.</span><span class="sxs-lookup"><span data-stu-id="06456-171">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="06456-172">Ogni volta che viene selezionato il pulsante **Click me** :</span><span class="sxs-lookup"><span data-stu-id="06456-172">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="06456-173">L' `onclick` evento viene generato.</span><span class="sxs-lookup"><span data-stu-id="06456-173">The `onclick` event is fired.</span></span>
* <span data-ttu-id="06456-174">Viene chiamato il metodo `IncrementCount` .</span><span class="sxs-lookup"><span data-stu-id="06456-174">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="06456-175">`currentCount` Viene incrementato.</span><span class="sxs-lookup"><span data-stu-id="06456-175">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="06456-176">Il rendering del componente viene eseguito nuovamente.</span><span class="sxs-lookup"><span data-stu-id="06456-176">The component is rendered again.</span></span>

<span data-ttu-id="06456-177">Il runtime confronta il nuovo contenuto con il contenuto precedente e applica solo il contenuto modificato al Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="06456-177">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="06456-178">Aggiungere un componente a un altro componente usando la sintassi HTML.</span><span class="sxs-lookup"><span data-stu-id="06456-178">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="06456-179">Ad esempio, aggiungere il `Counter` componente alla Home page dell'app aggiungendo un `<Counter />` elemento al `Index` componente.</span><span class="sxs-lookup"><span data-stu-id="06456-179">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="06456-180">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="06456-180">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="06456-181">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="06456-181">Run the app.</span></span> <span data-ttu-id="06456-182">La Home page presenta il proprio contatore fornito dal `Counter` componente.</span><span class="sxs-lookup"><span data-stu-id="06456-182">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="06456-183">I parametri del componente vengono specificati utilizzando attributi o [contenuto figlio](xref:blazor/components#child-content), che consentono di impostare le proprietà per il componente figlio.</span><span class="sxs-lookup"><span data-stu-id="06456-183">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="06456-184">Per aggiungere un parametro al `Counter` componente, aggiornare il `@code` blocco del componente:</span><span class="sxs-lookup"><span data-stu-id="06456-184">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="06456-185">Aggiungere una proprietà pubblica per `IncrementAmount` con un `[Parameter]` attributo.</span><span class="sxs-lookup"><span data-stu-id="06456-185">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="06456-186">Modificare il metodo `IncrementCount` per usare `IncrementAmount` quando si aumenta il valore di `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="06456-186">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="06456-187">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="06456-187">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="06456-188">Specificare `IncrementAmount` nell' `<Counter>` elemento del `Index` componente usando un attributo.</span><span class="sxs-lookup"><span data-stu-id="06456-188">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="06456-189">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="06456-189">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="06456-190">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="06456-190">Run the app.</span></span> <span data-ttu-id="06456-191">Il `Index` componente dispone di un contatore specifico che aumenta di dieci ogni volta che viene selezionato il pulsante **Click me** .</span><span class="sxs-lookup"><span data-stu-id="06456-191">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="06456-192">Il `Counter` componente (*Counter. Razor*) a `/counter` continua a incrementare di uno.</span><span class="sxs-lookup"><span data-stu-id="06456-192">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="06456-193">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="06456-193">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="06456-194">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="06456-194">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
