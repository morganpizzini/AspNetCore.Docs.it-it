---
title: Debug ASP.NET Core Blazor Webassembly
author: guardrex
description: Informazioni su come eseguire il debug delle Blazor app.
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
uid: blazor/debug
ms.openlocfilehash: 75db5d5e69cb200ebf3bd1dc1e0afed0300214cc
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242771"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="09fce-103">Debug ASP.NET Core Blazor Webassembly</span><span class="sxs-lookup"><span data-stu-id="09fce-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="09fce-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="09fce-104">Daniel Roth</span></span>](https://github.com/danroth27)

Blazor<span data-ttu-id="09fce-105">È possibile eseguire il debug delle app webassembly usando gli strumenti di sviluppo del browser nei browser basati su cromo (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="09fce-105"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="09fce-106">In alternativa, è possibile eseguire il debug dell'app usando Visual Studio o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="09fce-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="09fce-107">Gli scenari disponibili includono:</span><span class="sxs-lookup"><span data-stu-id="09fce-107">Available scenarios include:</span></span>

* <span data-ttu-id="09fce-108">Impostare e rimuovere punti di interruzione.</span><span class="sxs-lookup"><span data-stu-id="09fce-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="09fce-109">Eseguire l'app con supporto per il debug in Visual Studio e Visual Studio Code (supporto<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="09fce-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="09fce-110">Singolo passaggio (<kbd>F10</kbd>) tramite il codice.</span><span class="sxs-lookup"><span data-stu-id="09fce-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="09fce-111">Riprendere l'esecuzione del codice con <kbd>F8</kbd> in un browser o <kbd>F5</kbd> in Visual Studio o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="09fce-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="09fce-112">Nella visualizzazione variabili *locali* osservare i valori delle variabili locali.</span><span class="sxs-lookup"><span data-stu-id="09fce-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="09fce-113">Vedere lo stack di chiamate, incluse le catene di chiamate che passano da JavaScript a .NET e da .NET a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="09fce-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="09fce-114">Per il momento *non è possibile*:</span><span class="sxs-lookup"><span data-stu-id="09fce-114">For now, you *can't*:</span></span>

* <span data-ttu-id="09fce-115">Interrompi in corrispondenza di eccezioni non gestite.</span><span class="sxs-lookup"><span data-stu-id="09fce-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="09fce-116">Raggiunge i punti di interruzione durante l'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="09fce-116">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="09fce-117">Si continuerà a migliorare l'esperienza di debug nelle prossime versioni.</span><span class="sxs-lookup"><span data-stu-id="09fce-117">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="09fce-118">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="09fce-118">Prerequisites</span></span>

<span data-ttu-id="09fce-119">Il debug richiede uno dei seguenti browser:</span><span class="sxs-lookup"><span data-stu-id="09fce-119">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="09fce-120">Microsoft Edge (versione 80 o successiva)</span><span class="sxs-lookup"><span data-stu-id="09fce-120">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="09fce-121">Google Chrome (versione 70 o successiva)</span><span class="sxs-lookup"><span data-stu-id="09fce-121">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="09fce-122">Abilitare il debug per Visual Studio e Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="09fce-122">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="09fce-123">Per abilitare il debug per un' Blazor app webassembly esistente, aggiornare il `launchSettings.json` file nel progetto di avvio per includere la `inspectUri` proprietà seguente in ogni profilo di avvio:</span><span class="sxs-lookup"><span data-stu-id="09fce-123">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="09fce-124">Al termine dell'aggiornamento, il `launchSettings.json` file dovrebbe essere simile all'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="09fce-124">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="09fce-125">`inspectUri`Proprietà:</span><span class="sxs-lookup"><span data-stu-id="09fce-125">The `inspectUri` property:</span></span>

* <span data-ttu-id="09fce-126">Consente all'IDE di rilevare che l'app è un' Blazor app webassembly.</span><span class="sxs-lookup"><span data-stu-id="09fce-126">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="09fce-127">Indica all'infrastruttura di debug degli script di connettersi al browser tramite il Blazor proxy di debug.</span><span class="sxs-lookup"><span data-stu-id="09fce-127">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="09fce-128">I valori segnaposto per i protocolli WebSockets ( `wsProtocol` ), host ( `url.hostname` ), Port ( `url.port` ) e Inspector URI nel browser avviato ( `browserInspectUri` ) sono forniti dal Framework.</span><span class="sxs-lookup"><span data-stu-id="09fce-128">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="09fce-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="09fce-129">Visual Studio</span></span>

<span data-ttu-id="09fce-130">Per eseguire il debug di un' Blazor app webassembly in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="09fce-130">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="09fce-131">Creare una nuova app ASP.NET Core Blazor webassembly ospitata.</span><span class="sxs-lookup"><span data-stu-id="09fce-131">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="09fce-132">Premere <kbd>F5</kbd> per eseguire l'app nel debugger.</span><span class="sxs-lookup"><span data-stu-id="09fce-132">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="09fce-133">Impostare un punto di interruzione in `Pages/Counter.razor` nel `IncrementCount` metodo.</span><span class="sxs-lookup"><span data-stu-id="09fce-133">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="09fce-134">Passare alla **`Counter`** scheda e selezionare il pulsante per raggiungere il punto di interruzione:</span><span class="sxs-lookup"><span data-stu-id="09fce-134">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![Contatore debug](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="09fce-136">Verificare il valore del `currentCount` campo nella finestra variabili locali:</span><span class="sxs-lookup"><span data-stu-id="09fce-136">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Visualizza variabili locali](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="09fce-138">Premere <kbd>F5</kbd> per continuare l'esecuzione.</span><span class="sxs-lookup"><span data-stu-id="09fce-138">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="09fce-139">Quando si esegue il debug dell' Blazor app webassembly, è anche possibile eseguire il debug del codice del server:</span><span class="sxs-lookup"><span data-stu-id="09fce-139">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="09fce-140">Impostare un punto di interruzione nella `Pages/FetchData.razor` pagina in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="09fce-140">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="09fce-141">Impostare un punto di interruzione in `WeatherForecastController` nel `Get` metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="09fce-141">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="09fce-142">Passare alla **`Fetch Data`** scheda per raggiungere il primo punto di interruzione nel `FetchData` componente immediatamente prima di eseguire una richiesta HTTP al server:</span><span class="sxs-lookup"><span data-stu-id="09fce-142">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Eseguire il debug dei dati di recupero](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="09fce-144">Premere <kbd>F5</kbd> per continuare l'esecuzione e quindi raggiungere il punto di interruzione sul server nell' `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="09fce-144">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Server di debug](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="09fce-146">Premere di nuovo <kbd>F5</kbd> per consentire l'esecuzione continua e visualizzare la tabella delle previsioni meteorologiche sottoposta a rendering.</span><span class="sxs-lookup"><span data-stu-id="09fce-146">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="09fce-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="09fce-147">Visual Studio Code</span></span>

<span data-ttu-id="09fce-148">Per eseguire il debug di un' Blazor app webassembly in Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="09fce-148">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
<span data-ttu-id="09fce-149">Installare l' [estensione C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e l'estensione del [debugger JavaScript (notturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con `debug.javascript.usePreview` impostato su `true` .</span><span class="sxs-lookup"><span data-stu-id="09fce-149">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

![Estensioni](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![Debugger di anteprima JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a><span data-ttu-id="09fce-152">Debug del Blazor webassembly autonomo</span><span class="sxs-lookup"><span data-stu-id="09fce-152">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="09fce-153">Aprire l' Blazor app webassembly autonoma in vs code.</span><span class="sxs-lookup"><span data-stu-id="09fce-153">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="09fce-154">Se viene visualizzata la notifica seguente, è necessaria un'installazione aggiuntiva per abilitare il debug:</span><span class="sxs-lookup"><span data-stu-id="09fce-154">If you receive the following notification that additional setup is required to enable debugging:</span></span>
   
   * <span data-ttu-id="09fce-155">Verificare che siano installate le estensioni corrette.</span><span class="sxs-lookup"><span data-stu-id="09fce-155">Confirm that you have the correct extensions installed.</span></span>
   * <span data-ttu-id="09fce-156">Verificare che l'anteprima JavaScript sia abilitata.</span><span class="sxs-lookup"><span data-stu-id="09fce-156">Confirm that JavaScript preview debugging is enabled.</span></span>
   * <span data-ttu-id="09fce-157">Ricaricare la finestra.</span><span class="sxs-lookup"><span data-stu-id="09fce-157">Reload the window.</span></span>

   ![Installazione aggiuntiva richiesta](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <span data-ttu-id="09fce-159">Avviare il debug usando il tasto di scelta rapida <kbd>F5</kbd> o la voce di menu.</span><span class="sxs-lookup"><span data-stu-id="09fce-159">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

1. <span data-ttu-id="09fce-160">Quando richiesto, selezionare l'opzione di \*\* Blazor debug webassembly\*\* per avviare il debug.</span><span class="sxs-lookup"><span data-stu-id="09fce-160">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![Elenco delle opzioni di debug disponibili](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="09fce-162">Viene avviata l'app autonoma e viene aperto un browser di debug.</span><span class="sxs-lookup"><span data-stu-id="09fce-162">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="09fce-163">Impostare un punto di interruzione nel `IncrementCount` metodo nel `Counter` componente, quindi selezionare il pulsante per raggiungere il punto di interruzione:</span><span class="sxs-lookup"><span data-stu-id="09fce-163">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Esegui il debug del contatore in VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a><span data-ttu-id="09fce-165">Debug Blazor webassembly ospitato</span><span class="sxs-lookup"><span data-stu-id="09fce-165">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="09fce-166">Aprire l' Blazor app webassembly ospitata in vs code.</span><span class="sxs-lookup"><span data-stu-id="09fce-166">Open the hosted Blazor WebAssembly app in VS Code.</span></span>

1. <span data-ttu-id="09fce-167">Se non è impostata alcuna configurazione di avvio per il progetto, viene visualizzata la notifica seguente.</span><span class="sxs-lookup"><span data-stu-id="09fce-167">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="09fce-168">Selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="09fce-168">Select **Yes**.</span></span>

   ![Aggiungi asset necessari](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="09fce-170">Nella finestra di selezione selezionare il progetto *Server* all'interno della soluzione ospitata.</span><span class="sxs-lookup"><span data-stu-id="09fce-170">In the selection window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="09fce-171">`launch.json`Viene generato un file con la configurazione di avvio per l'avvio del debugger.</span><span class="sxs-lookup"><span data-stu-id="09fce-171">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="09fce-172">Connetti a una sessione di debug esistente</span><span class="sxs-lookup"><span data-stu-id="09fce-172">Attach to an existing debugging session</span></span>

<span data-ttu-id="09fce-173">Per connettersi a un'app in esecuzione Blazor , creare un `launch.json` file con la configurazione seguente:</span><span class="sxs-lookup"><span data-stu-id="09fce-173">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="09fce-174">Il fissaggio a una sessione di debug è supportato solo per le app autonome.</span><span class="sxs-lookup"><span data-stu-id="09fce-174">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="09fce-175">Per usare il debug dello stack completo, è necessario avviare l'app da VS Code.</span><span class="sxs-lookup"><span data-stu-id="09fce-175">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="09fce-176">Opzioni di configurazione di avvio</span><span class="sxs-lookup"><span data-stu-id="09fce-176">Launch configuration options</span></span>

<span data-ttu-id="09fce-177">Per il tipo di debug sono supportate le seguenti opzioni di configurazione di avvio `blazorwasm` .</span><span class="sxs-lookup"><span data-stu-id="09fce-177">The following launch configuration options are supported for the `blazorwasm` debug type.</span></span>

| <span data-ttu-id="09fce-178">Opzione</span><span class="sxs-lookup"><span data-stu-id="09fce-178">Option</span></span>    | <span data-ttu-id="09fce-179">Descrizione</span><span class="sxs-lookup"><span data-stu-id="09fce-179">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="09fce-180">Usare `launch` per avviare e alleghi una sessione di debug a un' Blazor app webassembly o `attach` per alleghi una sessione di debug a un'app già in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="09fce-180">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="09fce-181">URL da aprire nel browser durante il debug.</span><span class="sxs-lookup"><span data-stu-id="09fce-181">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="09fce-182">Il valore predefinito è `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="09fce-182">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="09fce-183">Browser da avviare per la sessione di debug.</span><span class="sxs-lookup"><span data-stu-id="09fce-183">The browser to launch for the debugging session.</span></span> <span data-ttu-id="09fce-184">Impostare su `edge` o `chrome`.</span><span class="sxs-lookup"><span data-stu-id="09fce-184">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="09fce-185">Il valore predefinito è `chrome`.</span><span class="sxs-lookup"><span data-stu-id="09fce-185">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="09fce-186">Usato per generare log dal debugger JS.</span><span class="sxs-lookup"><span data-stu-id="09fce-186">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="09fce-187">Impostare su `true` per generare i log.</span><span class="sxs-lookup"><span data-stu-id="09fce-187">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="09fce-188">Deve essere impostato su `true` se si avvia e si esegue il debug di un' Blazor app webassembly ospitata.</span><span class="sxs-lookup"><span data-stu-id="09fce-188">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="09fce-189">Specifica il percorso assoluto del server Web.</span><span class="sxs-lookup"><span data-stu-id="09fce-189">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="09fce-190">Deve essere impostato se un'app viene gestita da una route secondaria.</span><span class="sxs-lookup"><span data-stu-id="09fce-190">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="09fce-191">Numero di millisecondi di attesa per il fissaggio della sessione di debug.</span><span class="sxs-lookup"><span data-stu-id="09fce-191">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="09fce-192">Il valore predefinito è 30.000 millisecondi (30 secondi).</span><span class="sxs-lookup"><span data-stu-id="09fce-192">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="09fce-193">Riferimento al file eseguibile per eseguire il server dell'app ospitata.</span><span class="sxs-lookup"><span data-stu-id="09fce-193">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="09fce-194">Deve essere impostato se `hosted` è `true` .</span><span class="sxs-lookup"><span data-stu-id="09fce-194">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="09fce-195">Directory di lavoro in cui avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="09fce-195">The working directory to launch the app under.</span></span> <span data-ttu-id="09fce-196">Deve essere impostato se `hosted` è `true` .</span><span class="sxs-lookup"><span data-stu-id="09fce-196">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="09fce-197">Variabili di ambiente da fornire al processo avviato.</span><span class="sxs-lookup"><span data-stu-id="09fce-197">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="09fce-198">Applicabile solo se `hosted` è impostato su `true` .</span><span class="sxs-lookup"><span data-stu-id="09fce-198">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="09fce-199">Configurazioni di avvio di esempio</span><span class="sxs-lookup"><span data-stu-id="09fce-199">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a><span data-ttu-id="09fce-200">Avviare ed eseguire il debug di un' Blazor app webassembly autonoma</span><span class="sxs-lookup"><span data-stu-id="09fce-200">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="09fce-201">Connettersi a un'app in esecuzione in un URL specificato</span><span class="sxs-lookup"><span data-stu-id="09fce-201">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="09fce-202">Avviare ed eseguire il debug di un' Blazor app webassembly ospitata</span><span class="sxs-lookup"><span data-stu-id="09fce-202">Launch and debug a hosted Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a><span data-ttu-id="09fce-203">Debug nel browser</span><span class="sxs-lookup"><span data-stu-id="09fce-203">Debug in the browser</span></span>

1. <span data-ttu-id="09fce-204">Eseguire una build di debug dell'app nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="09fce-204">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="09fce-205">Premere <kbd>MAIUSC</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="09fce-205">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="09fce-206">Il browser deve essere eseguito con il debug remoto abilitato.</span><span class="sxs-lookup"><span data-stu-id="09fce-206">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="09fce-207">Se il debug remoto è disabilitato, viene generata una pagina di errore della **scheda del browser di cui è possibile eseguire il debug** .</span><span class="sxs-lookup"><span data-stu-id="09fce-207">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="09fce-208">La pagina di errore contiene le istruzioni per eseguire il browser con la porta di debug aperta, in modo che il Blazor proxy di debug possa connettersi all'app.</span><span class="sxs-lookup"><span data-stu-id="09fce-208">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="09fce-209">*Chiudere tutte le istanze del browser* e riavviare il browser come indicato.</span><span class="sxs-lookup"><span data-stu-id="09fce-209">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="09fce-210">Quando il browser è in esecuzione con il debug remoto abilitato, il tasto di scelta rapida di debug apre una nuova scheda del debugger. Dopo qualche istante la scheda **Sources (origini** ) Mostra un elenco degli assembly .NET nell'app.</span><span class="sxs-lookup"><span data-stu-id="09fce-210">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="09fce-211">Espandere ogni assembly e trovare i `.cs` / `.razor` file di origine disponibili per il debug.</span><span class="sxs-lookup"><span data-stu-id="09fce-211">Expand each assembly and find the `.cs`/`.razor` source files available for debugging.</span></span> <span data-ttu-id="09fce-212">Impostare i punti di interruzione, tornare alla scheda dell'app e i punti di interruzione vengono raggiunti quando viene eseguito il codice.</span><span class="sxs-lookup"><span data-stu-id="09fce-212">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="09fce-213">Quando viene raggiunto un punto di interruzione, l'esecuzione<kbd>F10</kbd>del codice in un singolo passaggio (F10<kbd>) viene</kbd>eseguito normalmente.</span><span class="sxs-lookup"><span data-stu-id="09fce-213">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="09fce-214">fornisce un proxy di debug che implementa il [protocollo devtools di Chrome](https://chromedevtools.github.io/devtools-protocol/) e potenzia il protocollo con. Informazioni specifiche del NET.</span><span class="sxs-lookup"><span data-stu-id="09fce-214"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="09fce-215">Quando si preme il tasto di scelta rapida Blazor per il debug, punta il devtools di Chrome sul proxy.</span><span class="sxs-lookup"><span data-stu-id="09fce-215">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="09fce-216">Il proxy si connette alla finestra del browser che si sta tentando di eseguire il debug, quindi è necessario abilitare il debug remoto.</span><span class="sxs-lookup"><span data-stu-id="09fce-216">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="09fce-217">Mappe di origine del browser</span><span class="sxs-lookup"><span data-stu-id="09fce-217">Browser source maps</span></span>

<span data-ttu-id="09fce-218">Le mappe di origine del browser consentono al browser di eseguire il mapping dei file compilati ai file di origine originali e vengono comunemente usati per il debug sul lato client.</span><span class="sxs-lookup"><span data-stu-id="09fce-218">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="09fce-219">Tuttavia, Blazor attualmente non esegue il mapping di C# direttamente a JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="09fce-219">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="09fce-220">Al contrario, Blazor l'interpretazione il nel browser, quindi le mappe di origine non sono rilevanti.</span><span class="sxs-lookup"><span data-stu-id="09fce-220">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="09fce-221">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="09fce-221">Troubleshoot</span></span>

<span data-ttu-id="09fce-222">Se si verificano errori, è possibile che vengano visualizzati i suggerimenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="09fce-222">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="09fce-223">Nella scheda **debugger** aprire gli strumenti di sviluppo nel browser.</span><span class="sxs-lookup"><span data-stu-id="09fce-223">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="09fce-224">Nella console eseguire `localStorage.clear()` per rimuovere tutti i punti di interruzione.</span><span class="sxs-lookup"><span data-stu-id="09fce-224">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="09fce-225">Verificare di aver installato e considerato attendibile il certificato di sviluppo ASP.NET Core HTTPS.</span><span class="sxs-lookup"><span data-stu-id="09fce-225">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="09fce-226">Per altre informazioni, vedere <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="09fce-226">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
