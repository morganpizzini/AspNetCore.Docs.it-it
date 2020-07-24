---
title: 'ASP.NET Core di debug:::no-loc(Blazor WebAssembly):::'
author: guardrex
description: 'Informazioni su come eseguire il debug delle :::no-loc(Blazor)::: app.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
no-loc:
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/debug
ms.openlocfilehash: 14943b9f7847ac9144addfdf16a003f6fc8c340c
ms.sourcegitcommit: cc845634a490c49ff869c89b6e422b6d65d0e886
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87159702"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="a086e-103">ASP.NET Core di debug:::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="a086e-103">Debug ASP.NET Core :::no-loc(Blazor WebAssembly):::</span></span>

[<span data-ttu-id="a086e-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="a086e-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="a086e-105">:::no-loc(Blazor WebAssembly):::è possibile eseguire il debug delle app usando gli strumenti di sviluppo del browser nei browser basati su cromo (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="a086e-105">:::no-loc(Blazor WebAssembly)::: apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="a086e-106">In alternativa, è possibile eseguire il debug dell'app usando Visual Studio o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="a086e-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="a086e-107">Gli scenari disponibili includono:</span><span class="sxs-lookup"><span data-stu-id="a086e-107">Available scenarios include:</span></span>

* <span data-ttu-id="a086e-108">Impostare e rimuovere punti di interruzione.</span><span class="sxs-lookup"><span data-stu-id="a086e-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="a086e-109">Eseguire l'app con supporto per il debug in Visual Studio e Visual Studio Code (supporto<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="a086e-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="a086e-110">Singolo passaggio (<kbd>F10</kbd>) tramite il codice.</span><span class="sxs-lookup"><span data-stu-id="a086e-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="a086e-111">Riprendere l'esecuzione del codice con <kbd>F8</kbd> in un browser o <kbd>F5</kbd> in Visual Studio o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="a086e-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="a086e-112">Nella visualizzazione variabili *locali* osservare i valori delle variabili locali.</span><span class="sxs-lookup"><span data-stu-id="a086e-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="a086e-113">Vedere lo stack di chiamate, incluse le catene di chiamate che passano da JavaScript a .NET e da .NET a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a086e-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="a086e-114">Per il momento *non è possibile*:</span><span class="sxs-lookup"><span data-stu-id="a086e-114">For now, you *can't*:</span></span>

* <span data-ttu-id="a086e-115">Interrompi in corrispondenza di eccezioni non gestite.</span><span class="sxs-lookup"><span data-stu-id="a086e-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="a086e-116">Raggiunge i punti di interruzione durante l'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="a086e-116">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="a086e-117">Si continuerà a migliorare l'esperienza di debug nelle prossime versioni.</span><span class="sxs-lookup"><span data-stu-id="a086e-117">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a086e-118">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="a086e-118">Prerequisites</span></span>

<span data-ttu-id="a086e-119">Il debug richiede uno dei seguenti browser:</span><span class="sxs-lookup"><span data-stu-id="a086e-119">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="a086e-120">Google Chrome (versione 70 o successiva) (impostazione predefinita)</span><span class="sxs-lookup"><span data-stu-id="a086e-120">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="a086e-121">Microsoft Edge (versione 80 o successiva)</span><span class="sxs-lookup"><span data-stu-id="a086e-121">Microsoft Edge (version 80 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="a086e-122">Abilitare il debug per Visual Studio e Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a086e-122">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="a086e-123">Per abilitare il debug per un' :::no-loc(Blazor WebAssembly)::: app esistente, aggiornare il `launchSettings.json` file nel progetto di avvio per includere la `inspectUri` proprietà seguente in ogni profilo di avvio:</span><span class="sxs-lookup"><span data-stu-id="a086e-123">To enable debugging for an existing :::no-loc(Blazor WebAssembly)::: app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="a086e-124">Al termine dell'aggiornamento, il `launchSettings.json` file dovrebbe essere simile all'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="a086e-124">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="a086e-125">`inspectUri`Proprietà:</span><span class="sxs-lookup"><span data-stu-id="a086e-125">The `inspectUri` property:</span></span>

* <span data-ttu-id="a086e-126">Consente all'IDE di rilevare che l'app è un' :::no-loc(Blazor WebAssembly)::: app.</span><span class="sxs-lookup"><span data-stu-id="a086e-126">Enables the IDE to detect that the app is a :::no-loc(Blazor WebAssembly)::: app.</span></span>
* <span data-ttu-id="a086e-127">Indica all'infrastruttura di debug degli script di connettersi al browser tramite il :::no-loc(Blazor)::: proxy di debug.</span><span class="sxs-lookup"><span data-stu-id="a086e-127">Instructs the script debugging infrastructure to connect to the browser through :::no-loc(Blazor):::'s debugging proxy.</span></span>

<span data-ttu-id="a086e-128">I valori segnaposto per i protocolli WebSockets ( `wsProtocol` ), host ( `url.hostname` ), Port ( `url.port` ) e Inspector URI nel browser avviato ( `browserInspectUri` ) sono forniti dal Framework.</span><span class="sxs-lookup"><span data-stu-id="a086e-128">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="a086e-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a086e-129">Visual Studio</span></span>

<span data-ttu-id="a086e-130">Per eseguire il debug di un' :::no-loc(Blazor WebAssembly)::: app in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a086e-130">To debug a :::no-loc(Blazor WebAssembly)::: app in Visual Studio:</span></span>

1. <span data-ttu-id="a086e-131">Creare una nuova app ospitata ASP.NET Core :::no-loc(Blazor WebAssembly)::: .</span><span class="sxs-lookup"><span data-stu-id="a086e-131">Create a new ASP.NET Core hosted :::no-loc(Blazor WebAssembly)::: app.</span></span>
1. <span data-ttu-id="a086e-132">Premere <kbd>F5</kbd> per eseguire l'app nel debugger.</span><span class="sxs-lookup"><span data-stu-id="a086e-132">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="a086e-133">Impostare un punto di interruzione in `Pages/Counter.razor` nel `IncrementCount` metodo.</span><span class="sxs-lookup"><span data-stu-id="a086e-133">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="a086e-134">Passare alla **`Counter`** scheda e selezionare il pulsante per raggiungere il punto di interruzione:</span><span class="sxs-lookup"><span data-stu-id="a086e-134">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![Contatore debug](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="a086e-136">Verificare il valore del `currentCount` campo nella finestra variabili locali:</span><span class="sxs-lookup"><span data-stu-id="a086e-136">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Visualizza variabili locali](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="a086e-138">Premere <kbd>F5</kbd> per continuare l'esecuzione.</span><span class="sxs-lookup"><span data-stu-id="a086e-138">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="a086e-139">Quando si esegue il debug dell' :::no-loc(Blazor WebAssembly)::: app, è anche possibile eseguire il debug del codice del server:</span><span class="sxs-lookup"><span data-stu-id="a086e-139">While debugging your :::no-loc(Blazor WebAssembly)::: app, you can also debug your server code:</span></span>

1. <span data-ttu-id="a086e-140">Impostare un punto di interruzione nella `Pages/FetchData.razor` pagina in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="a086e-140">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="a086e-141">Impostare un punto di interruzione in `WeatherForecastController` nel `Get` metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="a086e-141">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="a086e-142">Passare alla **`Fetch Data`** scheda per raggiungere il primo punto di interruzione nel `FetchData` componente immediatamente prima di eseguire una richiesta HTTP al server:</span><span class="sxs-lookup"><span data-stu-id="a086e-142">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Eseguire il debug dei dati di recupero](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="a086e-144">Premere <kbd>F5</kbd> per continuare l'esecuzione e quindi raggiungere il punto di interruzione sul server nell' `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="a086e-144">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Server di debug](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="a086e-146">Premere di nuovo <kbd>F5</kbd> per consentire l'esecuzione continua e visualizzare la tabella delle previsioni meteorologiche sottoposta a rendering.</span><span class="sxs-lookup"><span data-stu-id="a086e-146">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="a086e-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a086e-147">Visual Studio Code</span></span>

### <a name="debug-standalone-no-locblazor-webassembly"></a><span data-ttu-id="a086e-148">Debug autonomo:::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="a086e-148">Debug standalone :::no-loc(Blazor WebAssembly):::</span></span>

1. <span data-ttu-id="a086e-149">Aprire l'app autonoma :::no-loc(Blazor WebAssembly)::: in vs code.</span><span class="sxs-lookup"><span data-stu-id="a086e-149">Open the standalone :::no-loc(Blazor WebAssembly)::: app in VS Code.</span></span>

   <span data-ttu-id="a086e-150">Se viene visualizzata la notifica seguente, è necessaria un'installazione aggiuntiva per abilitare il debug:</span><span class="sxs-lookup"><span data-stu-id="a086e-150">If you receive the following notification that additional setup is required to enable debugging:</span></span>
   
   * <span data-ttu-id="a086e-151">Verificare che siano installate le estensioni corrette.</span><span class="sxs-lookup"><span data-stu-id="a086e-151">Confirm that you have the correct extensions installed.</span></span>
   * <span data-ttu-id="a086e-152">Verificare che l'anteprima JavaScript sia abilitata.</span><span class="sxs-lookup"><span data-stu-id="a086e-152">Confirm that JavaScript preview debugging is enabled.</span></span>
   * <span data-ttu-id="a086e-153">Ricaricare la finestra.</span><span class="sxs-lookup"><span data-stu-id="a086e-153">Reload the window.</span></span>

   ![Installazione aggiuntiva richiesta](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <span data-ttu-id="a086e-155">Avviare il debug usando il tasto di scelta rapida <kbd>F5</kbd> o la voce di menu.</span><span class="sxs-lookup"><span data-stu-id="a086e-155">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

1. <span data-ttu-id="a086e-156">Quando richiesto, selezionare l'opzione \*\* :::no-loc(Blazor WebAssembly)::: debug\*\* per avviare il debug.</span><span class="sxs-lookup"><span data-stu-id="a086e-156">When prompted, select the **:::no-loc(Blazor WebAssembly)::: Debug** option to start debugging.</span></span>

   ![Elenco delle opzioni di debug disponibili](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="a086e-158">Viene avviata l'app autonoma e viene aperto un browser di debug.</span><span class="sxs-lookup"><span data-stu-id="a086e-158">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="a086e-159">Impostare un punto di interruzione nel `IncrementCount` metodo nel `Counter` componente, quindi selezionare il pulsante per raggiungere il punto di interruzione:</span><span class="sxs-lookup"><span data-stu-id="a086e-159">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Esegui il debug del contatore in VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="a086e-161">Debug ospitato:::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="a086e-161">Debug hosted :::no-loc(Blazor WebAssembly):::</span></span>

1. <span data-ttu-id="a086e-162">Aprire la cartella della soluzione dell'app ospitata :::no-loc(Blazor WebAssembly)::: in vs code.</span><span class="sxs-lookup"><span data-stu-id="a086e-162">Open the hosted :::no-loc(Blazor WebAssembly)::: app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="a086e-163">Se non è impostata alcuna configurazione di avvio per il progetto, viene visualizzata la notifica seguente.</span><span class="sxs-lookup"><span data-stu-id="a086e-163">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="a086e-164">Selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="a086e-164">Select **Yes**.</span></span>

   ![Aggiungi asset necessari](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="a086e-166">Nel riquadro comandi nella parte superiore della finestra selezionare il progetto *Server* all'interno della soluzione ospitata.</span><span class="sxs-lookup"><span data-stu-id="a086e-166">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="a086e-167">`launch.json`Viene generato un file con la configurazione di avvio per l'avvio del debugger.</span><span class="sxs-lookup"><span data-stu-id="a086e-167">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="a086e-168">Connetti a una sessione di debug esistente</span><span class="sxs-lookup"><span data-stu-id="a086e-168">Attach to an existing debugging session</span></span>

<span data-ttu-id="a086e-169">Per connettersi a un'app in esecuzione :::no-loc(Blazor)::: , creare un `launch.json` file con la configurazione seguente:</span><span class="sxs-lookup"><span data-stu-id="a086e-169">To attach to a running :::no-loc(Blazor)::: app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing :::no-loc(Blazor WebAssembly)::: Application"
}
```

> [!NOTE]
> <span data-ttu-id="a086e-170">Il fissaggio a una sessione di debug è supportato solo per le app autonome.</span><span class="sxs-lookup"><span data-stu-id="a086e-170">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="a086e-171">Per usare il debug dello stack completo, è necessario avviare l'app da VS Code.</span><span class="sxs-lookup"><span data-stu-id="a086e-171">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="a086e-172">Opzioni di configurazione di avvio</span><span class="sxs-lookup"><span data-stu-id="a086e-172">Launch configuration options</span></span>

<span data-ttu-id="a086e-173">Le seguenti opzioni di configurazione di avvio sono supportate per il `blazorwasm` tipo di debug ( `.vscode/launch.json` ).</span><span class="sxs-lookup"><span data-stu-id="a086e-173">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="a086e-174">Opzione</span><span class="sxs-lookup"><span data-stu-id="a086e-174">Option</span></span>    | <span data-ttu-id="a086e-175">Description</span><span class="sxs-lookup"><span data-stu-id="a086e-175">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="a086e-176">Usare `launch` per avviare e alleghi una sessione di debug a un' :::no-loc(Blazor WebAssembly)::: app o `attach` per alleghi una sessione di debug a un'app già in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="a086e-176">Use `launch` to launch and attach a debugging session to a :::no-loc(Blazor WebAssembly)::: app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="a086e-177">URL da aprire nel browser durante il debug.</span><span class="sxs-lookup"><span data-stu-id="a086e-177">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="a086e-178">Il valore predefinito è `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="a086e-178">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="a086e-179">Browser da avviare per la sessione di debug.</span><span class="sxs-lookup"><span data-stu-id="a086e-179">The browser to launch for the debugging session.</span></span> <span data-ttu-id="a086e-180">Impostare su `edge` o `chrome`.</span><span class="sxs-lookup"><span data-stu-id="a086e-180">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="a086e-181">Il valore predefinito è `chrome`.</span><span class="sxs-lookup"><span data-stu-id="a086e-181">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="a086e-182">Usato per generare log dal debugger JS.</span><span class="sxs-lookup"><span data-stu-id="a086e-182">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="a086e-183">Impostare su `true` per generare i log.</span><span class="sxs-lookup"><span data-stu-id="a086e-183">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="a086e-184">Deve essere impostato su `true` se si avvia e si esegue il debug di un'app ospitata :::no-loc(Blazor WebAssembly)::: .</span><span class="sxs-lookup"><span data-stu-id="a086e-184">Must be set to `true` if launching and debugging a hosted :::no-loc(Blazor WebAssembly)::: app.</span></span> |
| `webRoot` | <span data-ttu-id="a086e-185">Specifica il percorso assoluto del server Web.</span><span class="sxs-lookup"><span data-stu-id="a086e-185">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="a086e-186">Deve essere impostato se un'app viene gestita da una route secondaria.</span><span class="sxs-lookup"><span data-stu-id="a086e-186">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="a086e-187">Numero di millisecondi di attesa per il fissaggio della sessione di debug.</span><span class="sxs-lookup"><span data-stu-id="a086e-187">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="a086e-188">Il valore predefinito è 30.000 millisecondi (30 secondi).</span><span class="sxs-lookup"><span data-stu-id="a086e-188">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="a086e-189">Riferimento al file eseguibile per eseguire il server dell'app ospitata.</span><span class="sxs-lookup"><span data-stu-id="a086e-189">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="a086e-190">Deve essere impostato se `hosted` è `true` .</span><span class="sxs-lookup"><span data-stu-id="a086e-190">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="a086e-191">Directory di lavoro in cui avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="a086e-191">The working directory to launch the app under.</span></span> <span data-ttu-id="a086e-192">Deve essere impostato se `hosted` è `true` .</span><span class="sxs-lookup"><span data-stu-id="a086e-192">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="a086e-193">Variabili di ambiente da fornire al processo avviato.</span><span class="sxs-lookup"><span data-stu-id="a086e-193">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="a086e-194">Applicabile solo se `hosted` è impostato su `true` .</span><span class="sxs-lookup"><span data-stu-id="a086e-194">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="a086e-195">Configurazioni di avvio di esempio</span><span class="sxs-lookup"><span data-stu-id="a086e-195">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="a086e-196">Avviare ed eseguire il debug di un'app autonoma :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="a086e-196">Launch and debug a standalone :::no-loc(Blazor WebAssembly)::: app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="a086e-197">Connettersi a un'app in esecuzione in un URL specificato</span><span class="sxs-lookup"><span data-stu-id="a086e-197">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="a086e-198">Avviare ed eseguire il debug di un'app ospitata :::no-loc(Blazor WebAssembly)::: con Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="a086e-198">Launch and debug a hosted :::no-loc(Blazor WebAssembly)::: app with Microsoft Edge</span></span>

<span data-ttu-id="a086e-199">Per impostazione predefinita, la configurazione del browser è Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="a086e-199">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="a086e-200">Quando si usa Microsoft Edge per il debug, impostare `browser` su `edge` .</span><span class="sxs-lookup"><span data-stu-id="a086e-200">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="a086e-201">Per usare Google Chrome, non impostare l' `browser` opzione o impostare il valore dell'opzione su `chrome` .</span><span class="sxs-lookup"><span data-stu-id="a086e-201">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

```json
{
  "name": "Launch and Debug Hosted :::no-loc(Blazor WebAssembly)::: App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

<span data-ttu-id="a086e-202">Nell'esempio precedente, `MyHostedApp.Server.dll` è l'assembly dell'app *Server* .</span><span class="sxs-lookup"><span data-stu-id="a086e-202">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="a086e-203">La `.vscode` cartella si trova nella cartella della soluzione accanto alle `Client` `Server` cartelle, e `Shared` .</span><span class="sxs-lookup"><span data-stu-id="a086e-203">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

## <a name="debug-in-the-browser"></a><span data-ttu-id="a086e-204">Debug nel browser</span><span class="sxs-lookup"><span data-stu-id="a086e-204">Debug in the browser</span></span>

1. <span data-ttu-id="a086e-205">Eseguire una build di debug dell'app nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="a086e-205">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="a086e-206">Avviare un browser e passare all'URL dell'app, ad esempio `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="a086e-206">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="a086e-207">Nel browser provare ad avviare il debug remoto premendo <kbd>MAIUSC</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="a086e-207">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

   <span data-ttu-id="a086e-208">Il browser deve essere in esecuzione con il debug remoto abilitato, che non è il valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="a086e-208">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="a086e-209">Se il debug remoto è disabilitato, viene eseguito il rendering della pagina di errore della **scheda del browser di cui è possibile eseguire** il debug con le istruzioni per avviare il browser con la porta di debug aperta.</span><span class="sxs-lookup"><span data-stu-id="a086e-209">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="a086e-210">Seguire le istruzioni per il browser, che consente di aprire una nuova finestra del browser.</span><span class="sxs-lookup"><span data-stu-id="a086e-210">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="a086e-211">Chiudere la finestra del browser precedente.</span><span class="sxs-lookup"><span data-stu-id="a086e-211">Close the previous browser window.</span></span>

1. <span data-ttu-id="a086e-212">Quando il browser è in esecuzione con il debug remoto abilitato, il tasto di scelta rapida per il debug (<kbd>MAIUSC</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>) apre una nuova scheda del debugger.</span><span class="sxs-lookup"><span data-stu-id="a086e-212">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut (<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>) opens a new debugger tab.</span></span>

1. <span data-ttu-id="a086e-213">Dopo qualche istante, la scheda **origini** Mostra un elenco degli assembly .NET dell'app all'interno del `file://` nodo.</span><span class="sxs-lookup"><span data-stu-id="a086e-213">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="a086e-214">Nel codice componente ( `.razor` file) e nei file di codice C# ( `.cs` ), i punti di interruzione impostati vengono raggiunti durante l'esecuzione del codice.</span><span class="sxs-lookup"><span data-stu-id="a086e-214">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="a086e-215">Quando viene raggiunto un punto di interruzione, l'esecuzione<kbd>F10</kbd>del codice in un singolo passaggio (F10<kbd>) viene</kbd>eseguito normalmente.</span><span class="sxs-lookup"><span data-stu-id="a086e-215">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="a086e-216">:::no-loc(Blazor):::fornisce un proxy di debug che implementa il [protocollo devtools di Chrome](https://chromedevtools.github.io/devtools-protocol/) e potenzia il protocollo con. Informazioni specifiche del NET.</span><span class="sxs-lookup"><span data-stu-id="a086e-216">:::no-loc(Blazor)::: provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="a086e-217">Quando si preme il tasto di scelta rapida :::no-loc(Blazor)::: per il debug, punta il devtools di Chrome sul proxy.</span><span class="sxs-lookup"><span data-stu-id="a086e-217">When debugging keyboard shortcut is pressed, :::no-loc(Blazor)::: points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="a086e-218">Il proxy si connette alla finestra del browser che si sta tentando di eseguire il debug, quindi è necessario abilitare il debug remoto.</span><span class="sxs-lookup"><span data-stu-id="a086e-218">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="a086e-219">Mappe di origine del browser</span><span class="sxs-lookup"><span data-stu-id="a086e-219">Browser source maps</span></span>

<span data-ttu-id="a086e-220">Le mappe di origine del browser consentono al browser di eseguire il mapping dei file compilati ai file di origine originali e vengono comunemente usati per il debug sul lato client.</span><span class="sxs-lookup"><span data-stu-id="a086e-220">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="a086e-221">Tuttavia, :::no-loc(Blazor)::: attualmente non esegue il mapping di C# direttamente a JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="a086e-221">However, :::no-loc(Blazor)::: doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="a086e-222">Al contrario, :::no-loc(Blazor)::: l'interpretazione il nel browser, quindi le mappe di origine non sono rilevanti.</span><span class="sxs-lookup"><span data-stu-id="a086e-222">Instead, :::no-loc(Blazor)::: does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="a086e-223">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="a086e-223">Troubleshoot</span></span>

<span data-ttu-id="a086e-224">Se si verificano errori, è possibile che vengano visualizzati i suggerimenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="a086e-224">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="a086e-225">Nella scheda **debugger** aprire gli strumenti di sviluppo nel browser.</span><span class="sxs-lookup"><span data-stu-id="a086e-225">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="a086e-226">Nella console eseguire `localStorage.clear()` per rimuovere tutti i punti di interruzione.</span><span class="sxs-lookup"><span data-stu-id="a086e-226">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="a086e-227">Verificare di aver installato e considerato attendibile il certificato di sviluppo ASP.NET Core HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a086e-227">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="a086e-228">Per altre informazioni, vedere <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="a086e-228">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="a086e-229">Visual Studio richiede l'opzione **Abilita debug JavaScript per ASP.NET (Chrome, Edge e IE)** in **strumenti**  >  **Opzioni**  >  **debug**  >  **generale**.</span><span class="sxs-lookup"><span data-stu-id="a086e-229">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="a086e-230">Questa è l'impostazione predefinita per Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a086e-230">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="a086e-231">Se il debug non funziona, verificare che sia selezionata l'opzione.</span><span class="sxs-lookup"><span data-stu-id="a086e-231">If debugging isn't working, confirm that the option is selected.</span></span>
