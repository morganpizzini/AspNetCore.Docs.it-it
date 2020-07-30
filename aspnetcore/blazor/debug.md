---
title: ASP.NET Core di debugBlazor WebAssembly
author: guardrex
description: Informazioni su come eseguire il debug delle Blazor app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: b4199c3a99af5875c5d9a87f29f7c7e2758ffd71
ms.sourcegitcommit: 5a36758cca2861aeb10840093e46d273a6e6e91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87303560"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="3340b-103">ASP.NET Core di debugBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="3340b-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="3340b-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="3340b-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="3340b-105">Blazor WebAssemblyè possibile eseguire il debug delle app usando gli strumenti di sviluppo del browser nei browser basati su cromo (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="3340b-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="3340b-106">In alternativa, è possibile eseguire il debug dell'app usando Visual Studio o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3340b-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="3340b-107">Gli scenari disponibili includono:</span><span class="sxs-lookup"><span data-stu-id="3340b-107">Available scenarios include:</span></span>

* <span data-ttu-id="3340b-108">Impostare e rimuovere punti di interruzione.</span><span class="sxs-lookup"><span data-stu-id="3340b-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="3340b-109">Eseguire l'app con supporto per il debug in Visual Studio e Visual Studio Code (supporto<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="3340b-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="3340b-110">Singolo passaggio (<kbd>F10</kbd>) tramite il codice.</span><span class="sxs-lookup"><span data-stu-id="3340b-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="3340b-111">Riprendere l'esecuzione del codice con <kbd>F8</kbd> in un browser o <kbd>F5</kbd> in Visual Studio o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3340b-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="3340b-112">Nella visualizzazione variabili *locali* osservare i valori delle variabili locali.</span><span class="sxs-lookup"><span data-stu-id="3340b-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="3340b-113">Vedere lo stack di chiamate, incluse le catene di chiamate che passano da JavaScript a .NET e da .NET a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3340b-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="3340b-114">Per il momento *non è possibile*:</span><span class="sxs-lookup"><span data-stu-id="3340b-114">For now, you *can't*:</span></span>

* <span data-ttu-id="3340b-115">Interrompi in corrispondenza di eccezioni non gestite.</span><span class="sxs-lookup"><span data-stu-id="3340b-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="3340b-116">Raggiunge i punti di interruzione durante l'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="3340b-116">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="3340b-117">Si continuerà a migliorare l'esperienza di debug nelle prossime versioni.</span><span class="sxs-lookup"><span data-stu-id="3340b-117">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3340b-118">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="3340b-118">Prerequisites</span></span>

<span data-ttu-id="3340b-119">Il debug richiede uno dei seguenti browser:</span><span class="sxs-lookup"><span data-stu-id="3340b-119">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="3340b-120">Google Chrome (versione 70 o successiva) (impostazione predefinita)</span><span class="sxs-lookup"><span data-stu-id="3340b-120">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="3340b-121">Microsoft Edge (versione 80 o successiva)</span><span class="sxs-lookup"><span data-stu-id="3340b-121">Microsoft Edge (version 80 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="3340b-122">Abilitare il debug per Visual Studio e Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3340b-122">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="3340b-123">Per abilitare il debug per un' Blazor WebAssembly app esistente, aggiornare il `launchSettings.json` file nel progetto di avvio per includere la `inspectUri` proprietà seguente in ogni profilo di avvio:</span><span class="sxs-lookup"><span data-stu-id="3340b-123">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="3340b-124">Al termine dell'aggiornamento, il `launchSettings.json` file dovrebbe essere simile all'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="3340b-124">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="3340b-125">`inspectUri`Proprietà:</span><span class="sxs-lookup"><span data-stu-id="3340b-125">The `inspectUri` property:</span></span>

* <span data-ttu-id="3340b-126">Consente all'IDE di rilevare che l'app è un' Blazor WebAssembly app.</span><span class="sxs-lookup"><span data-stu-id="3340b-126">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="3340b-127">Indica all'infrastruttura di debug degli script di connettersi al browser tramite il Blazor proxy di debug.</span><span class="sxs-lookup"><span data-stu-id="3340b-127">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="3340b-128">I valori segnaposto per i protocolli WebSockets ( `wsProtocol` ), host ( `url.hostname` ), Port ( `url.port` ) e Inspector URI nel browser avviato ( `browserInspectUri` ) sono forniti dal Framework.</span><span class="sxs-lookup"><span data-stu-id="3340b-128">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="3340b-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3340b-129">Visual Studio</span></span>

<span data-ttu-id="3340b-130">Per eseguire il debug di un' Blazor WebAssembly app in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="3340b-130">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="3340b-131">Creare una nuova app ospitata ASP.NET Core Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="3340b-131">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="3340b-132">Premere <kbd>F5</kbd> per eseguire l'app nel debugger.</span><span class="sxs-lookup"><span data-stu-id="3340b-132">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="3340b-133">Impostare un punto di interruzione in `Pages/Counter.razor` nel `IncrementCount` metodo.</span><span class="sxs-lookup"><span data-stu-id="3340b-133">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="3340b-134">Passare alla **`Counter`** scheda e selezionare il pulsante per raggiungere il punto di interruzione:</span><span class="sxs-lookup"><span data-stu-id="3340b-134">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![Contatore debug](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="3340b-136">Verificare il valore del `currentCount` campo nella finestra variabili locali:</span><span class="sxs-lookup"><span data-stu-id="3340b-136">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Visualizza variabili locali](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="3340b-138">Premere <kbd>F5</kbd> per continuare l'esecuzione.</span><span class="sxs-lookup"><span data-stu-id="3340b-138">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="3340b-139">Quando si esegue il debug dell' Blazor WebAssembly app, è anche possibile eseguire il debug del codice del server:</span><span class="sxs-lookup"><span data-stu-id="3340b-139">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="3340b-140">Impostare un punto di interruzione nella `Pages/FetchData.razor` pagina in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="3340b-140">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="3340b-141">Impostare un punto di interruzione in `WeatherForecastController` nel `Get` metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="3340b-141">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="3340b-142">Passare alla **`Fetch Data`** scheda per raggiungere il primo punto di interruzione nel `FetchData` componente immediatamente prima di eseguire una richiesta HTTP al server:</span><span class="sxs-lookup"><span data-stu-id="3340b-142">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Eseguire il debug dei dati di recupero](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="3340b-144">Premere <kbd>F5</kbd> per continuare l'esecuzione e quindi raggiungere il punto di interruzione sul server nell' `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="3340b-144">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Server di debug](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="3340b-146">Premere di nuovo <kbd>F5</kbd> per consentire l'esecuzione continua e visualizzare la tabella delle previsioni meteorologiche sottoposta a rendering.</span><span class="sxs-lookup"><span data-stu-id="3340b-146">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="3340b-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3340b-147">Visual Studio Code</span></span>

### <a name="debug-standalone-no-locblazor-webassembly"></a><span data-ttu-id="3340b-148">Debug autonomoBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="3340b-148">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="3340b-149">Aprire l'app autonoma Blazor WebAssembly in vs code.</span><span class="sxs-lookup"><span data-stu-id="3340b-149">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="3340b-150">È possibile che venga ricevuta la notifica seguente che è necessaria un'installazione aggiuntiva per abilitare il debug:</span><span class="sxs-lookup"><span data-stu-id="3340b-150">You may receive the following notification that additional setup is required to enable debugging:</span></span>
   
   ![Installazione aggiuntiva richiesta](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)
   
   <span data-ttu-id="3340b-152">Se si riceve la notifica:</span><span class="sxs-lookup"><span data-stu-id="3340b-152">If you receive the notification:</span></span>

   * <span data-ttu-id="3340b-153">Verificare che sia installata la versione più recente [di C# per Visual Studio Code estensione](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) .</span><span class="sxs-lookup"><span data-stu-id="3340b-153">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="3340b-154">Per esaminare le estensioni installate, aprire **View**  >  **Extensions** dalla barra dei menu o selezionare l'icona **Extensions** nella barra laterale **Activity** .</span><span class="sxs-lookup"><span data-stu-id="3340b-154">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="3340b-155">Verificare che l'anteprima JavaScript sia abilitata.</span><span class="sxs-lookup"><span data-stu-id="3340b-155">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="3340b-156">Aprire le impostazioni dalla barra dei menu (**File**  >  **Preferences**  >  **Impostazioni**preferenze file).</span><span class="sxs-lookup"><span data-stu-id="3340b-156">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="3340b-157">Eseguire la ricerca usando le parole chiave `debug preview` .</span><span class="sxs-lookup"><span data-stu-id="3340b-157">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="3340b-158">Nei risultati della ricerca verificare che la casella di controllo per **Debug > JavaScript: USA anteprima** sia selezionata.</span><span class="sxs-lookup"><span data-stu-id="3340b-158">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span>
   * <span data-ttu-id="3340b-159">Ricaricare la finestra.</span><span class="sxs-lookup"><span data-stu-id="3340b-159">Reload the window.</span></span>

1. <span data-ttu-id="3340b-160">Avviare il debug usando il tasto di scelta rapida <kbd>F5</kbd> o la voce di menu.</span><span class="sxs-lookup"><span data-stu-id="3340b-160">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

1. <span data-ttu-id="3340b-161">Quando richiesto, selezionare l'opzione \*\* Blazor WebAssembly debug\*\* per avviare il debug.</span><span class="sxs-lookup"><span data-stu-id="3340b-161">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![Elenco delle opzioni di debug disponibili](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="3340b-163">Viene avviata l'app autonoma e viene aperto un browser di debug.</span><span class="sxs-lookup"><span data-stu-id="3340b-163">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="3340b-164">Impostare un punto di interruzione nel `IncrementCount` metodo nel `Counter` componente, quindi selezionare il pulsante per raggiungere il punto di interruzione:</span><span class="sxs-lookup"><span data-stu-id="3340b-164">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Esegui il debug del contatore in VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="3340b-166">Debug ospitatoBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="3340b-166">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="3340b-167">Aprire la cartella della soluzione dell'app ospitata Blazor WebAssembly in vs code.</span><span class="sxs-lookup"><span data-stu-id="3340b-167">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="3340b-168">Se non è impostata alcuna configurazione di avvio per il progetto, viene visualizzata la notifica seguente.</span><span class="sxs-lookup"><span data-stu-id="3340b-168">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="3340b-169">Selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="3340b-169">Select **Yes**.</span></span>

   ![Aggiungi asset necessari](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="3340b-171">Nel riquadro comandi nella parte superiore della finestra selezionare il progetto *Server* all'interno della soluzione ospitata.</span><span class="sxs-lookup"><span data-stu-id="3340b-171">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="3340b-172">`launch.json`Viene generato un file con la configurazione di avvio per l'avvio del debugger.</span><span class="sxs-lookup"><span data-stu-id="3340b-172">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="3340b-173">Connetti a una sessione di debug esistente</span><span class="sxs-lookup"><span data-stu-id="3340b-173">Attach to an existing debugging session</span></span>

<span data-ttu-id="3340b-174">Per connettersi a un'app in esecuzione Blazor , creare un `launch.json` file con la configurazione seguente:</span><span class="sxs-lookup"><span data-stu-id="3340b-174">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="3340b-175">Il fissaggio a una sessione di debug è supportato solo per le app autonome.</span><span class="sxs-lookup"><span data-stu-id="3340b-175">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="3340b-176">Per usare il debug dello stack completo, è necessario avviare l'app da VS Code.</span><span class="sxs-lookup"><span data-stu-id="3340b-176">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="3340b-177">Opzioni di configurazione di avvio</span><span class="sxs-lookup"><span data-stu-id="3340b-177">Launch configuration options</span></span>

<span data-ttu-id="3340b-178">Le seguenti opzioni di configurazione di avvio sono supportate per il `blazorwasm` tipo di debug ( `.vscode/launch.json` ).</span><span class="sxs-lookup"><span data-stu-id="3340b-178">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="3340b-179">Opzione</span><span class="sxs-lookup"><span data-stu-id="3340b-179">Option</span></span>    | <span data-ttu-id="3340b-180">Descrizione</span><span class="sxs-lookup"><span data-stu-id="3340b-180">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="3340b-181">Usare `launch` per avviare e alleghi una sessione di debug a un' Blazor WebAssembly app o `attach` per alleghi una sessione di debug a un'app già in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="3340b-181">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="3340b-182">URL da aprire nel browser durante il debug.</span><span class="sxs-lookup"><span data-stu-id="3340b-182">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="3340b-183">Il valore predefinito è `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="3340b-183">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="3340b-184">Browser da avviare per la sessione di debug.</span><span class="sxs-lookup"><span data-stu-id="3340b-184">The browser to launch for the debugging session.</span></span> <span data-ttu-id="3340b-185">Impostare su `edge` o `chrome`.</span><span class="sxs-lookup"><span data-stu-id="3340b-185">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="3340b-186">Il valore predefinito è `chrome`.</span><span class="sxs-lookup"><span data-stu-id="3340b-186">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="3340b-187">Usato per generare log dal debugger JS.</span><span class="sxs-lookup"><span data-stu-id="3340b-187">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="3340b-188">Impostare su `true` per generare i log.</span><span class="sxs-lookup"><span data-stu-id="3340b-188">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="3340b-189">Deve essere impostato su `true` se si avvia e si esegue il debug di un'app ospitata Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="3340b-189">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="3340b-190">Specifica il percorso assoluto del server Web.</span><span class="sxs-lookup"><span data-stu-id="3340b-190">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="3340b-191">Deve essere impostato se un'app viene gestita da una route secondaria.</span><span class="sxs-lookup"><span data-stu-id="3340b-191">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="3340b-192">Numero di millisecondi di attesa per il fissaggio della sessione di debug.</span><span class="sxs-lookup"><span data-stu-id="3340b-192">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="3340b-193">Il valore predefinito è 30.000 millisecondi (30 secondi).</span><span class="sxs-lookup"><span data-stu-id="3340b-193">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="3340b-194">Riferimento al file eseguibile per eseguire il server dell'app ospitata.</span><span class="sxs-lookup"><span data-stu-id="3340b-194">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="3340b-195">Deve essere impostato se `hosted` è `true` .</span><span class="sxs-lookup"><span data-stu-id="3340b-195">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="3340b-196">Directory di lavoro in cui avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="3340b-196">The working directory to launch the app under.</span></span> <span data-ttu-id="3340b-197">Deve essere impostato se `hosted` è `true` .</span><span class="sxs-lookup"><span data-stu-id="3340b-197">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="3340b-198">Variabili di ambiente da fornire al processo avviato.</span><span class="sxs-lookup"><span data-stu-id="3340b-198">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="3340b-199">Applicabile solo se `hosted` è impostato su `true` .</span><span class="sxs-lookup"><span data-stu-id="3340b-199">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="3340b-200">Configurazioni di avvio di esempio</span><span class="sxs-lookup"><span data-stu-id="3340b-200">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="3340b-201">Avviare ed eseguire il debug di un'app autonoma Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="3340b-201">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="3340b-202">Connettersi a un'app in esecuzione in un URL specificato</span><span class="sxs-lookup"><span data-stu-id="3340b-202">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="3340b-203">Avviare ed eseguire il debug di un'app ospitata Blazor WebAssembly con Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="3340b-203">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="3340b-204">Per impostazione predefinita, la configurazione del browser è Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="3340b-204">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="3340b-205">Quando si usa Microsoft Edge per il debug, impostare `browser` su `edge` .</span><span class="sxs-lookup"><span data-stu-id="3340b-205">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="3340b-206">Per usare Google Chrome, non impostare l' `browser` opzione o impostare il valore dell'opzione su `chrome` .</span><span class="sxs-lookup"><span data-stu-id="3340b-206">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

<span data-ttu-id="3340b-207">Nell'esempio precedente, `MyHostedApp.Server.dll` è l'assembly dell'app *Server* .</span><span class="sxs-lookup"><span data-stu-id="3340b-207">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="3340b-208">La `.vscode` cartella si trova nella cartella della soluzione accanto alle `Client` `Server` cartelle, e `Shared` .</span><span class="sxs-lookup"><span data-stu-id="3340b-208">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

## <a name="debug-in-the-browser"></a><span data-ttu-id="3340b-209">Debug nel browser</span><span class="sxs-lookup"><span data-stu-id="3340b-209">Debug in the browser</span></span>

1. <span data-ttu-id="3340b-210">Eseguire una build di debug dell'app nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="3340b-210">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="3340b-211">Avviare un browser e passare all'URL dell'app, ad esempio `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="3340b-211">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="3340b-212">Nel browser provare ad avviare il debug remoto premendo <kbd>MAIUSC</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="3340b-212">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

   <span data-ttu-id="3340b-213">Il browser deve essere in esecuzione con il debug remoto abilitato, che non è il valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="3340b-213">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="3340b-214">Se il debug remoto è disabilitato, viene eseguito il rendering della pagina di errore della **scheda del browser di cui è possibile eseguire** il debug con le istruzioni per avviare il browser con la porta di debug aperta.</span><span class="sxs-lookup"><span data-stu-id="3340b-214">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="3340b-215">Seguire le istruzioni per il browser, che consente di aprire una nuova finestra del browser.</span><span class="sxs-lookup"><span data-stu-id="3340b-215">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="3340b-216">Chiudere la finestra del browser precedente.</span><span class="sxs-lookup"><span data-stu-id="3340b-216">Close the previous browser window.</span></span>

1. <span data-ttu-id="3340b-217">Quando il browser è in esecuzione con il debug remoto abilitato, il tasto di scelta rapida per il debug (<kbd>MAIUSC</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>) apre una nuova scheda del debugger.</span><span class="sxs-lookup"><span data-stu-id="3340b-217">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut (<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>) opens a new debugger tab.</span></span>

1. <span data-ttu-id="3340b-218">Dopo qualche istante, la scheda **origini** Mostra un elenco degli assembly .NET dell'app all'interno del `file://` nodo.</span><span class="sxs-lookup"><span data-stu-id="3340b-218">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="3340b-219">Nel codice componente ( `.razor` file) e nei file di codice C# ( `.cs` ), i punti di interruzione impostati vengono raggiunti durante l'esecuzione del codice.</span><span class="sxs-lookup"><span data-stu-id="3340b-219">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="3340b-220">Quando viene raggiunto un punto di interruzione, l'esecuzione<kbd>F10</kbd>del codice in un singolo passaggio (F10<kbd>) viene</kbd>eseguito normalmente.</span><span class="sxs-lookup"><span data-stu-id="3340b-220">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="3340b-221">Blazorfornisce un proxy di debug che implementa il [protocollo devtools di Chrome](https://chromedevtools.github.io/devtools-protocol/) e potenzia il protocollo con. Informazioni specifiche del NET.</span><span class="sxs-lookup"><span data-stu-id="3340b-221">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="3340b-222">Quando si preme il tasto di scelta rapida Blazor per il debug, punta il devtools di Chrome sul proxy.</span><span class="sxs-lookup"><span data-stu-id="3340b-222">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="3340b-223">Il proxy si connette alla finestra del browser che si sta tentando di eseguire il debug, quindi è necessario abilitare il debug remoto.</span><span class="sxs-lookup"><span data-stu-id="3340b-223">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="3340b-224">Mappe di origine del browser</span><span class="sxs-lookup"><span data-stu-id="3340b-224">Browser source maps</span></span>

<span data-ttu-id="3340b-225">Le mappe di origine del browser consentono al browser di eseguire il mapping dei file compilati ai file di origine originali e vengono comunemente usati per il debug sul lato client.</span><span class="sxs-lookup"><span data-stu-id="3340b-225">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="3340b-226">Tuttavia, Blazor attualmente non esegue il mapping di C# direttamente a JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="3340b-226">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="3340b-227">Al contrario, Blazor l'interpretazione il nel browser, quindi le mappe di origine non sono rilevanti.</span><span class="sxs-lookup"><span data-stu-id="3340b-227">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="3340b-228">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="3340b-228">Troubleshoot</span></span>

<span data-ttu-id="3340b-229">Se si verificano errori, è possibile che vengano visualizzati i suggerimenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="3340b-229">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="3340b-230">Nella scheda **debugger** aprire gli strumenti di sviluppo nel browser.</span><span class="sxs-lookup"><span data-stu-id="3340b-230">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="3340b-231">Nella console eseguire `localStorage.clear()` per rimuovere tutti i punti di interruzione.</span><span class="sxs-lookup"><span data-stu-id="3340b-231">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="3340b-232">Verificare di aver installato e considerato attendibile il certificato di sviluppo ASP.NET Core HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3340b-232">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="3340b-233">Per altre informazioni, vedere <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="3340b-233">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="3340b-234">Visual Studio richiede l'opzione **Abilita debug JavaScript per ASP.NET (Chrome, Edge e IE)** in **strumenti**  >  **Opzioni**  >  **debug**  >  **generale**.</span><span class="sxs-lookup"><span data-stu-id="3340b-234">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="3340b-235">Questa è l'impostazione predefinita per Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3340b-235">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="3340b-236">Se il debug non funziona, verificare che sia selezionata l'opzione.</span><span class="sxs-lookup"><span data-stu-id="3340b-236">If debugging isn't working, confirm that the option is selected.</span></span>
