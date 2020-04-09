---
title: Introduzione a ASP.NET CoreBlazor
author: guardrex
description: Inizia a Blazor creare Blazor un'app con gli strumenti di tua scelta.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: c49209afde21046a6bc0b197cc4b8d93b164b23e
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80471821"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="87830-103">Introduzione a ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="87830-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="87830-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="87830-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="87830-105">Per iniziare a utilizzare Blazor, segui le indicazioni per la tua scelta di utensili:</span><span class="sxs-lookup"><span data-stu-id="87830-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="87830-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87830-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="87830-107">Per creare app del server Blazor, installare la versione più recente di [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) con il carico di lavoro di **sviluppo Web e di ASP.NET.**</span><span class="sxs-lookup"><span data-stu-id="87830-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="87830-108">Per creare app Blazor Server e Blazor WebAssembly, installare l'anteprima più recente di [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) con il carico di lavoro **di ASP.NET e sviluppo Web.**</span><span class="sxs-lookup"><span data-stu-id="87830-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="87830-109">Per informazioni sui due modelli di hosting Blazor, *Blazor WebAssembly* e *Blazor Server*, vedere <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="87830-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="87830-110">Creare un nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="87830-110">Create a new project.</span></span>

1. <span data-ttu-id="87830-111">Selezionare **Blazor App**.</span><span class="sxs-lookup"><span data-stu-id="87830-111">Select **Blazor App**.</span></span> <span data-ttu-id="87830-112">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="87830-112">Select **Next**.</span></span>

1. <span data-ttu-id="87830-113">Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito.</span><span class="sxs-lookup"><span data-stu-id="87830-113">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="87830-114">Verificare che la voce **Posizione** sia corretta o specificare una posizione per il progetto.</span><span class="sxs-lookup"><span data-stu-id="87830-114">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="87830-115">Selezionare **Create** (Crea).</span><span class="sxs-lookup"><span data-stu-id="87830-115">Select **Create**.</span></span>

1. <span data-ttu-id="87830-116">Per un'esperienza Blazor WebAssembly (Visual Studio 16.6 Preview 2 o versione successiva), scegliere il modello **Blazor WebAssembly App.**</span><span class="sxs-lookup"><span data-stu-id="87830-116">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="87830-117">Per un'esperienza del server Blazor (Visual Studio 16.4 o versione successiva), scegliere il modello **Blazor Server App.**</span><span class="sxs-lookup"><span data-stu-id="87830-117">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="87830-118">Selezionare **Create** (Crea).</span><span class="sxs-lookup"><span data-stu-id="87830-118">Select **Create**.</span></span>

1. <span data-ttu-id="87830-119">Premere <kbd>CTRL</kbd>+<kbd>F5</kbd> per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="87830-119">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="87830-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="87830-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="87830-121">Installare [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="87830-121">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="87830-122">Facoltativamente, installare il modello di anteprima [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) eseguendo il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="87830-122">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > <span data-ttu-id="87830-123">Per utilizzare il modello WebAssembly [3.1.201 o versione successiva di .NET Core SDK versione 3.1.201 o successiva,](https://dotnet.microsoft.com/download/dotnet-core/3.1) è **necessario** .</span><span class="sxs-lookup"><span data-stu-id="87830-123">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 3 Blazor WebAssembly template.</span></span> <span data-ttu-id="87830-124">Verificare la versione di .NET Core SDK installata eseguendolo `dotnet --version` in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="87830-124">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="87830-125">Installare [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="87830-125">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="87830-126">Installare l'estensione più recente per [l'estensione](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) di codice di `debug.javascript.usePreview` Visual `true`Studio e l'estensione [Debugger JavaScript (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con impostato su .</span><span class="sxs-lookup"><span data-stu-id="87830-126">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="87830-127">Per un'esperienza di Blazor Server, eseguire il comando seguente in una shell dei comandi:For a Blazor Server experience, execute the following command in a command shell:</span><span class="sxs-lookup"><span data-stu-id="87830-127">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="87830-128">Per un'esperienza WebAssembly Blazor, eseguire il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="87830-128">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="87830-129">Per informazioni sui due modelli di hosting Blazor, *Blazor* Server <xref:blazor/hosting-models>e *Blazor WebAssembly,* vedere .</span><span class="sxs-lookup"><span data-stu-id="87830-129">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="87830-130">Aprire la cartella *WebApplication1* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="87830-130">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="87830-131">L'IDE richiede di aggiungere risorse per compilare ed eseguire il debug del progetto.</span><span class="sxs-lookup"><span data-stu-id="87830-131">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="87830-132">Selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="87830-132">Select **Yes**.</span></span>

1. <span data-ttu-id="87830-133">Esegui l'app usando il debugger del codice di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="87830-133">Run the app using the Visual Studio Code debugger.</span></span>

1. <span data-ttu-id="87830-134">In un browser passare a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="87830-134">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="87830-135">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="87830-135">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="87830-136">Blazor Server è supportato in Visual Studio per Mac.</span><span class="sxs-lookup"><span data-stu-id="87830-136">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="87830-137">Blazor WebAssembly non è supportato in questo momento.</span><span class="sxs-lookup"><span data-stu-id="87830-137">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="87830-138">Per compilare app Blazor WebAssembly in macOS, seguire le indicazioni nella scheda dell'interfaccia della riga di **comando di .NET Core.**</span><span class="sxs-lookup"><span data-stu-id="87830-138">To build Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab.</span></span>

1. <span data-ttu-id="87830-139">Installare [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="87830-139">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="87830-140">Selezionare **File** > **nuova soluzione** o creare un nuovo **progetto**.</span><span class="sxs-lookup"><span data-stu-id="87830-140">Select **File** > **New Solution** or create a **New Project**.</span></span>

1. <span data-ttu-id="87830-141">Nella barra laterale seleziona**App** **.NET Core** > .</span><span class="sxs-lookup"><span data-stu-id="87830-141">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="87830-142">Selezionare il modello **Blazor Server App.**</span><span class="sxs-lookup"><span data-stu-id="87830-142">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="87830-143">Selezionare **Create** (Crea).</span><span class="sxs-lookup"><span data-stu-id="87830-143">Select **Create**.</span></span>

   <span data-ttu-id="87830-144">Per informazioni sul modello di hosting Blazor Server, vedere <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="87830-144">For information on the Blazor Server hosting model, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="87830-145">Impostare Framework di **destinazione** su **.NET Core 3.1** e selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="87830-145">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

1. <span data-ttu-id="87830-146">Nel campo **Nome progetto** assegnare un nome all'app. `WebApplication1`</span><span class="sxs-lookup"><span data-stu-id="87830-146">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="87830-147">Selezionare **Create** (Crea).</span><span class="sxs-lookup"><span data-stu-id="87830-147">Select **Create**.</span></span>

1. <span data-ttu-id="87830-148">Selezionare **Esegui** > **senza debug** per eseguire l'app senza il *debugger.*</span><span class="sxs-lookup"><span data-stu-id="87830-148">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="87830-149">Eseguire l'app con **Avvia debug** per eseguire l'app *con il debugger.*</span><span class="sxs-lookup"><span data-stu-id="87830-149">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

<span data-ttu-id="87830-150">Se viene visualizzato un prompt per considerare attendibile il certificato di sviluppo, considerare attendibile il certificato e continuare.</span><span class="sxs-lookup"><span data-stu-id="87830-150">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="87830-151">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="87830-151">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="87830-152">Installare [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="87830-152">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="87830-153">Facoltativamente, installare il modello di anteprima [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) eseguendo il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="87830-153">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > <span data-ttu-id="87830-154">Per utilizzare il modello WebAssembly [3.1.201 o versione successiva di .NET Core SDK versione 3.1.201 o successiva,](https://dotnet.microsoft.com/download/dotnet-core/3.1) è **necessario** .</span><span class="sxs-lookup"><span data-stu-id="87830-154">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 3 Blazor WebAssembly template.</span></span> <span data-ttu-id="87830-155">Verificare la versione di .NET Core SDK installata eseguendolo `dotnet --version` in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="87830-155">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="87830-156">Per un'esperienza di Blazor Server, eseguire i seguenti comandi in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="87830-156">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="87830-157">Per un'esperienza WebAssembly Blazor, eseguire i seguenti comandi in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="87830-157">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="87830-158">Per informazioni sui due modelli di hosting Blazor, *Blazor* Server <xref:blazor/hosting-models>e *Blazor WebAssembly,* vedere .</span><span class="sxs-lookup"><span data-stu-id="87830-158">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="87830-159">In un browser passare a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="87830-159">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="87830-160">Più pagine sono disponibili dalle schede nella barra laterale:</span><span class="sxs-lookup"><span data-stu-id="87830-160">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="87830-161">Home</span><span class="sxs-lookup"><span data-stu-id="87830-161">Home</span></span>
* <span data-ttu-id="87830-162">Counter</span><span class="sxs-lookup"><span data-stu-id="87830-162">Counter</span></span>
* <span data-ttu-id="87830-163">Recuperare i dati</span><span class="sxs-lookup"><span data-stu-id="87830-163">Fetch data</span></span>

<span data-ttu-id="87830-164">Nella pagina Counter selezionare il pulsante **Click me** per incrementare il contatore senza un aggiornamento della pagina.</span><span class="sxs-lookup"><span data-stu-id="87830-164">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="87830-165">L'incremento di un contatore in una Blazor pagina Web richiede in genere la scrittura di JavaScript, ma con l'uso di C.</span><span class="sxs-lookup"><span data-stu-id="87830-165">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="87830-166">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="87830-166">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="87830-167">Una richiesta `/counter` per nel browser, `@page` come specificato dalla direttiva nella parte superiore, fa sì che il componente per eseguire il rendering del `Counter` contenuto.</span><span class="sxs-lookup"><span data-stu-id="87830-167">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="87830-168">I componenti eseguono il rendering in una rappresentazione in memoria dell'albero di rendering che può quindi essere utilizzata per aggiornare l'interfaccia utente in modo flessibile ed efficiente.</span><span class="sxs-lookup"><span data-stu-id="87830-168">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="87830-169">Ogni volta che viene selezionato il pulsante **Click me:**</span><span class="sxs-lookup"><span data-stu-id="87830-169">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="87830-170">L'evento `onclick` viene generato.</span><span class="sxs-lookup"><span data-stu-id="87830-170">The `onclick` event is fired.</span></span>
* <span data-ttu-id="87830-171">Viene chiamato il metodo `IncrementCount` .</span><span class="sxs-lookup"><span data-stu-id="87830-171">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="87830-172">L'oggetto `currentCount` viene incrementato.</span><span class="sxs-lookup"><span data-stu-id="87830-172">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="87830-173">Il rendering del componente viene eseguito nuovamente.</span><span class="sxs-lookup"><span data-stu-id="87830-173">The component is rendered again.</span></span>

<span data-ttu-id="87830-174">Il runtime confronta il nuovo contenuto con il contenuto precedente e applica il contenuto modificato solo al modello DOM (Document Object Model).</span><span class="sxs-lookup"><span data-stu-id="87830-174">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="87830-175">Aggiungere un componente a un altro componente utilizzando la sintassi HTML.</span><span class="sxs-lookup"><span data-stu-id="87830-175">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="87830-176">Ad esempio, `Counter` aggiungi il componente alla home page `<Counter />` dell'app aggiungendo un elemento al `Index` componente.</span><span class="sxs-lookup"><span data-stu-id="87830-176">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="87830-177">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="87830-177">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="87830-178">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="87830-178">Run the app.</span></span> <span data-ttu-id="87830-179">La homepage ha un proprio `Counter` contatore fornito dal componente.</span><span class="sxs-lookup"><span data-stu-id="87830-179">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="87830-180">I parametri del componente vengono specificati utilizzando gli attributi o il [contenuto figlio](xref:blazor/components#child-content), che consentono di impostare le proprietà del componente figlio.</span><span class="sxs-lookup"><span data-stu-id="87830-180">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="87830-181">Per aggiungere un `Counter` parametro al componente, `@code` aggiornare il blocco del componente:</span><span class="sxs-lookup"><span data-stu-id="87830-181">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="87830-182">Aggiungere una proprietà `IncrementAmount` pubblica `[Parameter]` per con un attributo.</span><span class="sxs-lookup"><span data-stu-id="87830-182">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="87830-183">Modificare il metodo `IncrementCount` per usare `IncrementAmount` quando si aumenta il valore di `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="87830-183">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="87830-184">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="87830-184">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="87830-185">Specificare `IncrementAmount` l'elemento `Index` `<Counter>` nell'elemento del componente utilizzando un attributo.</span><span class="sxs-lookup"><span data-stu-id="87830-185">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="87830-186">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="87830-186">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="87830-187">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="87830-187">Run the app.</span></span> <span data-ttu-id="87830-188">Il `Index` componente ha un proprio contatore che viene incrementato di dieci ogni volta che viene selezionato il pulsante **Click me.**</span><span class="sxs-lookup"><span data-stu-id="87830-188">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="87830-189">Il `Counter` componente (*Counter.razor*) `/counter` continua ad aumentare di uno.</span><span class="sxs-lookup"><span data-stu-id="87830-189">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="87830-190">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="87830-190">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="87830-191">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="87830-191">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
