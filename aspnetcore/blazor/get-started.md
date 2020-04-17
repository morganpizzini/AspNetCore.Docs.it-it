---
title: Introduzione a ASP.NET CoreBlazor
author: guardrex
description: Inizia a Blazor creare Blazor un'app con gli strumenti di tua scelta.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 7fe4fbb082f08d4f71684c836a826d8b6dd888f6
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488728"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="4866b-103">Introduzione a ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="4866b-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="4866b-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4866b-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="4866b-105">Per iniziare a utilizzare Blazor, segui le indicazioni per la tua scelta di utensili:</span><span class="sxs-lookup"><span data-stu-id="4866b-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4866b-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4866b-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="4866b-107">Per creare app del server Blazor, installare la versione più recente di [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) con il carico di lavoro di **sviluppo Web e di ASP.NET.**</span><span class="sxs-lookup"><span data-stu-id="4866b-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="4866b-108">Per creare app Blazor Server e Blazor WebAssembly, installare l'anteprima più recente di [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) con il carico di lavoro **di ASP.NET e sviluppo Web.**</span><span class="sxs-lookup"><span data-stu-id="4866b-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="4866b-109">Per informazioni sui due modelli di hosting Blazor, *Blazor WebAssembly* e *Blazor Server*, vedere <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="4866b-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="4866b-110">Installare il modello di anteprima [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) eseguendo il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="4866b-110">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
   ```

1. <span data-ttu-id="4866b-111">Creare un nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="4866b-111">Create a new project.</span></span>

1. <span data-ttu-id="4866b-112">Selezionare **Blazor App**.</span><span class="sxs-lookup"><span data-stu-id="4866b-112">Select **Blazor App**.</span></span> <span data-ttu-id="4866b-113">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="4866b-113">Select **Next**.</span></span>

1. <span data-ttu-id="4866b-114">Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito.</span><span class="sxs-lookup"><span data-stu-id="4866b-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="4866b-115">Verificare che la voce **Posizione** sia corretta o specificare una posizione per il progetto.</span><span class="sxs-lookup"><span data-stu-id="4866b-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="4866b-116">Selezionare **Create** (Crea).</span><span class="sxs-lookup"><span data-stu-id="4866b-116">Select **Create**.</span></span>

1. <span data-ttu-id="4866b-117">Per un'esperienza Blazor WebAssembly (Visual Studio 16.6 Preview 2 o versione successiva), scegliere il modello **Blazor WebAssembly App.**</span><span class="sxs-lookup"><span data-stu-id="4866b-117">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="4866b-118">Per un'esperienza del server Blazor (Visual Studio 16.4 o versione successiva), scegliere il modello **Blazor Server App.**</span><span class="sxs-lookup"><span data-stu-id="4866b-118">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="4866b-119">Selezionare **Create** (Crea).</span><span class="sxs-lookup"><span data-stu-id="4866b-119">Select **Create**.</span></span>

1. <span data-ttu-id="4866b-120">Premere <kbd>CTRL</kbd>+<kbd>F5</kbd> per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="4866b-120">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4866b-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4866b-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="4866b-122">Installare [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="4866b-122">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="4866b-123">Facoltativamente, installare il modello di anteprima [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) eseguendo il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="4866b-123">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
   ```

   > [!NOTE]
   > <span data-ttu-id="4866b-124">Per utilizzare il modello WebAssembly [3.1.201 o versione successiva di .NET Core SDK versione 3.1.201 o successiva,](https://dotnet.microsoft.com/download/dotnet-core/3.1) è **necessario** .</span><span class="sxs-lookup"><span data-stu-id="4866b-124">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="4866b-125">Verificare la versione di .NET Core SDK installata eseguendolo `dotnet --version` in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="4866b-125">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="4866b-126">Installare [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="4866b-126">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="4866b-127">Installare l'estensione più recente per [l'estensione](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) di codice di `debug.javascript.usePreview` Visual `true`Studio e l'estensione [Debugger JavaScript (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con impostato su .</span><span class="sxs-lookup"><span data-stu-id="4866b-127">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="4866b-128">Per un'esperienza di Blazor Server, eseguire il comando seguente in una shell dei comandi:For a Blazor Server experience, execute the following command in a command shell:</span><span class="sxs-lookup"><span data-stu-id="4866b-128">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="4866b-129">Per un'esperienza WebAssembly Blazor, eseguire il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="4866b-129">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="4866b-130">Per informazioni sui due modelli di hosting Blazor, *Blazor* Server <xref:blazor/hosting-models>e *Blazor WebAssembly,* vedere .</span><span class="sxs-lookup"><span data-stu-id="4866b-130">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="4866b-131">Aprire la cartella *WebApplication1* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="4866b-131">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="4866b-132">L'IDE richiede di aggiungere risorse per compilare ed eseguire il debug del progetto.</span><span class="sxs-lookup"><span data-stu-id="4866b-132">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="4866b-133">Selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="4866b-133">Select **Yes**.</span></span>

1. <span data-ttu-id="4866b-134">Con Blazor Server, esegui l'app usando il debugger del codice di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4866b-134">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="4866b-135">Con Blazor WebAssembly, avviare l'app utilizzando la configurazione di avvio **di .NET Core Launch (Blazor Standalone)** e quindi avviare il browser utilizzando **.NET Core Debug Blazor Web Assembly nella** configurazione di avvio di Chrome (richiede Chrome).</span><span class="sxs-lookup"><span data-stu-id="4866b-135">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="4866b-136">Per altre informazioni, vedere <xref:blazor/debug#visual-studio-code>.</span><span class="sxs-lookup"><span data-stu-id="4866b-136">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="4866b-137">In un browser passare a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="4866b-137">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4866b-138">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="4866b-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4866b-139">Blazor Server è supportato in Visual Studio per Mac.</span><span class="sxs-lookup"><span data-stu-id="4866b-139">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="4866b-140">Blazor WebAssembly non è supportato in questo momento.</span><span class="sxs-lookup"><span data-stu-id="4866b-140">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="4866b-141">Per compilare app Blazor WebAssembly in macOS, seguire le indicazioni nella scheda dell'interfaccia della riga di **comando di .NET Core.**</span><span class="sxs-lookup"><span data-stu-id="4866b-141">To build Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab.</span></span>

1. <span data-ttu-id="4866b-142">Installare [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="4866b-142">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="4866b-143">Selezionare **File** > **nuova soluzione** o creare un nuovo **progetto**.</span><span class="sxs-lookup"><span data-stu-id="4866b-143">Select **File** > **New Solution** or create a **New Project**.</span></span>

1. <span data-ttu-id="4866b-144">Nella barra laterale seleziona**App** **.NET Core** > .</span><span class="sxs-lookup"><span data-stu-id="4866b-144">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="4866b-145">Selezionare il modello **Blazor Server App.**</span><span class="sxs-lookup"><span data-stu-id="4866b-145">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="4866b-146">Selezionare **Create** (Crea).</span><span class="sxs-lookup"><span data-stu-id="4866b-146">Select **Create**.</span></span>

   <span data-ttu-id="4866b-147">Per informazioni sul modello di hosting Blazor Server, vedere <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="4866b-147">For information on the Blazor Server hosting model, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="4866b-148">Impostare Framework di **destinazione** su **.NET Core 3.1** e selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="4866b-148">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

1. <span data-ttu-id="4866b-149">Nel campo **Nome progetto** assegnare un nome all'app. `WebApplication1`</span><span class="sxs-lookup"><span data-stu-id="4866b-149">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="4866b-150">Selezionare **Create** (Crea).</span><span class="sxs-lookup"><span data-stu-id="4866b-150">Select **Create**.</span></span>

1. <span data-ttu-id="4866b-151">Selezionare **Esegui** > **senza debug** per eseguire l'app senza il *debugger.*</span><span class="sxs-lookup"><span data-stu-id="4866b-151">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="4866b-152">Eseguire l'app con **Avvia debug** per eseguire l'app *con il debugger.*</span><span class="sxs-lookup"><span data-stu-id="4866b-152">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

<span data-ttu-id="4866b-153">Se viene visualizzato un prompt per considerare attendibile il certificato di sviluppo, considerare attendibile il certificato e continuare.</span><span class="sxs-lookup"><span data-stu-id="4866b-153">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4866b-154">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="4866b-154">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="4866b-155">Installare [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="4866b-155">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="4866b-156">Facoltativamente, installare il modello di anteprima [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) eseguendo il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="4866b-156">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
   ```

   > [!NOTE]
   > <span data-ttu-id="4866b-157">Per utilizzare il modello WebAssembly [3.1.201 o versione successiva di .NET Core SDK versione 3.1.201 o successiva,](https://dotnet.microsoft.com/download/dotnet-core/3.1) è **necessario** .</span><span class="sxs-lookup"><span data-stu-id="4866b-157">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="4866b-158">Verificare la versione di .NET Core SDK installata eseguendolo `dotnet --version` in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="4866b-158">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="4866b-159">Per un'esperienza di Blazor Server, eseguire i seguenti comandi in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="4866b-159">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="4866b-160">Per un'esperienza WebAssembly Blazor, eseguire i seguenti comandi in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="4866b-160">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="4866b-161">Per informazioni sui due modelli di hosting Blazor, *Blazor* Server <xref:blazor/hosting-models>e *Blazor WebAssembly,* vedere .</span><span class="sxs-lookup"><span data-stu-id="4866b-161">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="4866b-162">In un browser passare a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="4866b-162">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="4866b-163">Più pagine sono disponibili dalle schede nella barra laterale:</span><span class="sxs-lookup"><span data-stu-id="4866b-163">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="4866b-164">Home</span><span class="sxs-lookup"><span data-stu-id="4866b-164">Home</span></span>
* <span data-ttu-id="4866b-165">Contatore</span><span class="sxs-lookup"><span data-stu-id="4866b-165">Counter</span></span>
* <span data-ttu-id="4866b-166">Recuperare i dati</span><span class="sxs-lookup"><span data-stu-id="4866b-166">Fetch data</span></span>

<span data-ttu-id="4866b-167">Nella pagina Counter selezionare il pulsante **Click me** per incrementare il contatore senza un aggiornamento della pagina.</span><span class="sxs-lookup"><span data-stu-id="4866b-167">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="4866b-168">L'incremento di un contatore in una Blazor pagina Web richiede in genere la scrittura di JavaScript, ma con l'uso di C.</span><span class="sxs-lookup"><span data-stu-id="4866b-168">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="4866b-169">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="4866b-169">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="4866b-170">Una richiesta `/counter` per nel browser, `@page` come specificato dalla direttiva nella parte superiore, fa sì che il componente per eseguire il rendering del `Counter` contenuto.</span><span class="sxs-lookup"><span data-stu-id="4866b-170">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="4866b-171">I componenti eseguono il rendering in una rappresentazione in memoria dell'albero di rendering che può quindi essere utilizzata per aggiornare l'interfaccia utente in modo flessibile ed efficiente.</span><span class="sxs-lookup"><span data-stu-id="4866b-171">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="4866b-172">Ogni volta che viene selezionato il pulsante **Click me:**</span><span class="sxs-lookup"><span data-stu-id="4866b-172">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="4866b-173">L'evento `onclick` viene generato.</span><span class="sxs-lookup"><span data-stu-id="4866b-173">The `onclick` event is fired.</span></span>
* <span data-ttu-id="4866b-174">Viene chiamato il metodo `IncrementCount` .</span><span class="sxs-lookup"><span data-stu-id="4866b-174">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="4866b-175">L'oggetto `currentCount` viene incrementato.</span><span class="sxs-lookup"><span data-stu-id="4866b-175">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="4866b-176">Il rendering del componente viene eseguito nuovamente.</span><span class="sxs-lookup"><span data-stu-id="4866b-176">The component is rendered again.</span></span>

<span data-ttu-id="4866b-177">Il runtime confronta il nuovo contenuto con il contenuto precedente e applica il contenuto modificato solo al modello DOM (Document Object Model).</span><span class="sxs-lookup"><span data-stu-id="4866b-177">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="4866b-178">Aggiungere un componente a un altro componente utilizzando la sintassi HTML.</span><span class="sxs-lookup"><span data-stu-id="4866b-178">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="4866b-179">Ad esempio, `Counter` aggiungi il componente alla home page `<Counter />` dell'app aggiungendo un elemento al `Index` componente.</span><span class="sxs-lookup"><span data-stu-id="4866b-179">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="4866b-180">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="4866b-180">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="4866b-181">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="4866b-181">Run the app.</span></span> <span data-ttu-id="4866b-182">La homepage ha un proprio `Counter` contatore fornito dal componente.</span><span class="sxs-lookup"><span data-stu-id="4866b-182">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="4866b-183">I parametri del componente vengono specificati utilizzando gli attributi o il [contenuto figlio](xref:blazor/components#child-content), che consentono di impostare le proprietà del componente figlio.</span><span class="sxs-lookup"><span data-stu-id="4866b-183">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="4866b-184">Per aggiungere un `Counter` parametro al componente, `@code` aggiornare il blocco del componente:</span><span class="sxs-lookup"><span data-stu-id="4866b-184">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="4866b-185">Aggiungere una proprietà `IncrementAmount` pubblica `[Parameter]` per con un attributo.</span><span class="sxs-lookup"><span data-stu-id="4866b-185">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="4866b-186">Modificare il metodo `IncrementCount` per usare `IncrementAmount` quando si aumenta il valore di `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="4866b-186">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="4866b-187">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="4866b-187">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="4866b-188">Specificare `IncrementAmount` l'elemento `Index` `<Counter>` nell'elemento del componente utilizzando un attributo.</span><span class="sxs-lookup"><span data-stu-id="4866b-188">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="4866b-189">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="4866b-189">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="4866b-190">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="4866b-190">Run the app.</span></span> <span data-ttu-id="4866b-191">Il `Index` componente ha un proprio contatore che viene incrementato di dieci ogni volta che viene selezionato il pulsante **Click me.**</span><span class="sxs-lookup"><span data-stu-id="4866b-191">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="4866b-192">Il `Counter` componente (*Counter.razor*) `/counter` continua ad aumentare di uno.</span><span class="sxs-lookup"><span data-stu-id="4866b-192">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4866b-193">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="4866b-193">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="4866b-194">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="4866b-194">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
