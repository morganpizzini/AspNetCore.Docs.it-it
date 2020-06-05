---
title: Inizia a usare ASP.NET CoreBlazor
author: guardrex
description: Inizia a Blazor creare un' Blazor app con gli strumenti che preferisci.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 08229283882928c4cc733de19840d25872846c97
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452031"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="99437-103">Inizia a usare ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="99437-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="99437-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="99437-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="99437-105">Per iniziare Blazor , seguire le istruzioni per la scelta degli strumenti:</span><span class="sxs-lookup"><span data-stu-id="99437-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="99437-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99437-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="99437-107">Installare la versione più recente di [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) con il carico di lavoro **sviluppo di ASP.NET e Web** .</span><span class="sxs-lookup"><span data-stu-id="99437-107">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="99437-108">Creare un nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="99437-108">Create a new project.</span></span>

1. <span data-ttu-id="99437-109">Selezionare \*\* Blazor app\*\*.</span><span class="sxs-lookup"><span data-stu-id="99437-109">Select **Blazor App**.</span></span> <span data-ttu-id="99437-110">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="99437-110">Select **Next**.</span></span>

1. <span data-ttu-id="99437-111">Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito.</span><span class="sxs-lookup"><span data-stu-id="99437-111">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="99437-112">Confermare che la voce relativa al **percorso** sia corretta o specificare un percorso per il progetto.</span><span class="sxs-lookup"><span data-stu-id="99437-112">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="99437-113">Selezionare **Create** (Crea).</span><span class="sxs-lookup"><span data-stu-id="99437-113">Select **Create**.</span></span>

1. <span data-ttu-id="99437-114">Per un' Blazor esperienza di webassembly, scegliere il modello \*\* Blazor app webassembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="99437-114">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="99437-115">Per un' Blazor esperienza server, scegliere il modello \*\* Blazor applicazione server\*\* .</span><span class="sxs-lookup"><span data-stu-id="99437-115">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="99437-116">Selezionare **Create** (Crea).</span><span class="sxs-lookup"><span data-stu-id="99437-116">Select **Create**.</span></span>

   <span data-ttu-id="99437-117">Per informazioni sui due Blazor modelli di hosting, \* Blazor webassembly\* e \* Blazor Server\*, vedere <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="99437-117">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="99437-118">Premere <kbd>CTRL</kbd> + <kbd>F5</kbd> per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="99437-118">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="99437-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="99437-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="99437-120">Installare la versione più recente di [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="99437-120">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="99437-121">Se in precedenza è stato installato l'SDK, è possibile determinare la versione installata eseguendo il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="99437-121">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="99437-122">Installare la versione più recente di [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="99437-122">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="99437-123">Installare la versione più recente [di C# per Visual Studio Code estensione](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e l'estensione del [debugger JavaScript (notturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con `debug.javascript.usePreview` impostato su `true` .</span><span class="sxs-lookup"><span data-stu-id="99437-123">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

  <span data-ttu-id="99437-124">Per impostare `debug.javascript.usePreview` `true` l'utilizzo dell'interfaccia utente di vs Code **File**, aprire  >  **Preferences**  >  **Impostazioni** preferenze file e cercare `debug javascript use preview` .</span><span class="sxs-lookup"><span data-stu-id="99437-124">To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`.</span></span> <span data-ttu-id="99437-125">Selezionare la casella di controllo **Usa il nuovo debugger JavaScript in anteprima per node. js e Chrome**.</span><span class="sxs-lookup"><span data-stu-id="99437-125">Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.</span></span>

1. <span data-ttu-id="99437-126">Per un' Blazor esperienza di webassembly, eseguire il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="99437-126">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="99437-127">Per un' Blazor esperienza server, eseguire il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="99437-127">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="99437-128">Per informazioni sui due Blazor modelli di hosting, \* Blazor webassembly\* e \* Blazor Server\*, vedere <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="99437-128">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="99437-129">Aprire la cartella *WebApplication1* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="99437-129">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="99437-130">Le richieste dell'IDE aggiungono risorse per compilare ed eseguire il debug del progetto.</span><span class="sxs-lookup"><span data-stu-id="99437-130">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="99437-131">Selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="99437-131">Select **Yes**.</span></span>

1. <span data-ttu-id="99437-132">Premere <kbd>CTRL</kbd> + <kbd>F5</kbd> per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="99437-132">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="99437-133">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="99437-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="99437-134">Installare [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="99437-134">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="99437-135">Selezionare **file**  >  **nuova soluzione** o creare un **nuovo** progetto dalla **finestra Start**.</span><span class="sxs-lookup"><span data-stu-id="99437-135">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="99437-136">Nella barra laterale selezionare app **Web e console**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="99437-136">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="99437-137">Per un' Blazor esperienza di webassembly, scegliere il modello \*\* Blazor app webassembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="99437-137">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="99437-138">Per un' Blazor esperienza server, scegliere il modello \*\* Blazor applicazione server\*\* .</span><span class="sxs-lookup"><span data-stu-id="99437-138">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="99437-139">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="99437-139">Select **Next**.</span></span>

   <span data-ttu-id="99437-140">Per informazioni sui due Blazor modelli di hosting, \* Blazor webassembly\* e \* Blazor Server\*, vedere <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="99437-140">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="99437-141">Verificare le configurazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="99437-141">Confirm the following configurations:</span></span>

   * <span data-ttu-id="99437-142">Il **Framework di destinazione** è impostato su **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="99437-142">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="99437-143">**L'autenticazione** è impostata su **Nessuna autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="99437-143">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="99437-144">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="99437-144">Select **Next**.</span></span>

1. <span data-ttu-id="99437-145">Nel campo **nome progetto** assegnare un nome all'app `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="99437-145">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="99437-146">Selezionare **Create** (Crea).</span><span class="sxs-lookup"><span data-stu-id="99437-146">Select **Create**.</span></span>

1. <span data-ttu-id="99437-147">Selezionare **Esegui**  >  **Avvia senza eseguire debug** per eseguire l'app *senza il debugger*.</span><span class="sxs-lookup"><span data-stu-id="99437-147">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="99437-148">Eseguire l'app con **Esegui**  >  **debug Avvia debug** o il pulsante Esegui (&#9654;) per eseguire l'app *con il debugger*.</span><span class="sxs-lookup"><span data-stu-id="99437-148">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="99437-149">Se viene visualizzato un messaggio per considerare attendibile il certificato di sviluppo, considerare attendibile il certificato e continuare.</span><span class="sxs-lookup"><span data-stu-id="99437-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="99437-150">Per considerare attendibile il certificato, è necessario specificare le password dell'utente e del keychain.</span><span class="sxs-lookup"><span data-stu-id="99437-150">The user and keychain passwords are required to trust the certificate.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="99437-151">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="99437-151">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="99437-152">Installare la versione più recente di [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="99437-152">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="99437-153">Se in precedenza è stato installato l'SDK, è possibile determinare la versione installata eseguendo il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="99437-153">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="99437-154">Per un' Blazor esperienza di webassembly, eseguire i comandi seguenti in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="99437-154">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="99437-155">Per un' Blazor esperienza server, eseguire i comandi seguenti in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="99437-155">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="99437-156">Per informazioni sui due Blazor modelli di hosting, \* Blazor webassembly\* e \* Blazor Server\*, vedere <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="99437-156">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="99437-157">In un browser passare a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="99437-157">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="99437-158">Nelle schede della barra laterale sono disponibili più pagine:</span><span class="sxs-lookup"><span data-stu-id="99437-158">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="99437-159">Home page</span><span class="sxs-lookup"><span data-stu-id="99437-159">Home</span></span>
* <span data-ttu-id="99437-160">Contatore</span><span class="sxs-lookup"><span data-stu-id="99437-160">Counter</span></span>
* <span data-ttu-id="99437-161">Recuperare i dati</span><span class="sxs-lookup"><span data-stu-id="99437-161">Fetch data</span></span>

<span data-ttu-id="99437-162">Nella pagina Counter selezionare il pulsante **Click me** per incrementare il contatore senza un aggiornamento della pagina.</span><span class="sxs-lookup"><span data-stu-id="99437-162">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="99437-163">Per incrementare un contatore in una pagina Web è in genere necessario scrivere JavaScript, ma con Blazor è possibile usare C#.</span><span class="sxs-lookup"><span data-stu-id="99437-163">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="99437-164">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="99437-164">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="99437-165">Una richiesta di `/counter` nel browser, come specificato dalla direttiva nella `@page` parte superiore, determina il rendering del contenuto da parte del `Counter` componente.</span><span class="sxs-lookup"><span data-stu-id="99437-165">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="99437-166">I componenti eseguono il rendering in una rappresentazione in memoria della struttura di rendering che può quindi essere utilizzata per aggiornare l'interfaccia utente in modo flessibile ed efficiente.</span><span class="sxs-lookup"><span data-stu-id="99437-166">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="99437-167">Ogni volta che viene selezionato il pulsante **Click me** :</span><span class="sxs-lookup"><span data-stu-id="99437-167">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="99437-168">L' `onclick` evento viene generato.</span><span class="sxs-lookup"><span data-stu-id="99437-168">The `onclick` event is fired.</span></span>
* <span data-ttu-id="99437-169">Viene chiamato il metodo `IncrementCount` .</span><span class="sxs-lookup"><span data-stu-id="99437-169">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="99437-170">`currentCount`Viene incrementato.</span><span class="sxs-lookup"><span data-stu-id="99437-170">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="99437-171">Il rendering del componente viene eseguito nuovamente.</span><span class="sxs-lookup"><span data-stu-id="99437-171">The component is rendered again.</span></span>

<span data-ttu-id="99437-172">Il runtime confronta il nuovo contenuto con il contenuto precedente e applica solo il contenuto modificato al Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="99437-172">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="99437-173">Aggiungere un componente a un altro componente usando la sintassi HTML.</span><span class="sxs-lookup"><span data-stu-id="99437-173">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="99437-174">Ad esempio, aggiungere il `Counter` componente alla Home page dell'app aggiungendo un `<Counter />` elemento al `Index` componente.</span><span class="sxs-lookup"><span data-stu-id="99437-174">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="99437-175">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="99437-175">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="99437-176">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="99437-176">Run the app.</span></span> <span data-ttu-id="99437-177">La Home page presenta il proprio contatore fornito dal `Counter` componente.</span><span class="sxs-lookup"><span data-stu-id="99437-177">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="99437-178">I parametri del componente vengono specificati utilizzando attributi o [contenuto figlio](xref:blazor/components#child-content), che consentono di impostare le proprietà per il componente figlio.</span><span class="sxs-lookup"><span data-stu-id="99437-178">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="99437-179">Per aggiungere un parametro al `Counter` componente, aggiornare il blocco del componente `@code` :</span><span class="sxs-lookup"><span data-stu-id="99437-179">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="99437-180">Aggiungere una proprietà pubblica per `IncrementAmount` con un [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="99437-180">Add a public property for `IncrementAmount` with a [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
* <span data-ttu-id="99437-181">Modificare il metodo `IncrementCount` per usare `IncrementAmount` quando si aumenta il valore di `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="99437-181">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="99437-182">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="99437-182">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="99437-183">Specificare `IncrementAmount` nell'elemento del `Index` componente `<Counter>` usando un attributo.</span><span class="sxs-lookup"><span data-stu-id="99437-183">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="99437-184">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="99437-184">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="99437-185">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="99437-185">Run the app.</span></span> <span data-ttu-id="99437-186">Il `Index` componente dispone di un contatore specifico che aumenta di dieci ogni volta che viene selezionato il pulsante **Click me** .</span><span class="sxs-lookup"><span data-stu-id="99437-186">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="99437-187">Il `Counter` componente (*Counter. Razor*) a `/counter` continua a incrementare di uno.</span><span class="sxs-lookup"><span data-stu-id="99437-187">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="99437-188">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="99437-188">Next steps</span></span>

<span data-ttu-id="99437-189">Procedura dettagliata per la compilazione di un' Blazor app:</span><span class="sxs-lookup"><span data-stu-id="99437-189">Build a Blazor app step-by-step:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="99437-190">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="99437-190">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
