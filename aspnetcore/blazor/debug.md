---
<span data-ttu-id="f0abd-101">title: "debug ASP.NET Core Blazor webassembly" Author: Description: "informazioni su come eseguire il debug delle Blazor app".</span><span class="sxs-lookup"><span data-stu-id="f0abd-101">title: 'Debug ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to debug Blazor apps.'</span></span>
<span data-ttu-id="f0abd-102">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f0abd-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f0abd-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f0abd-103">'Blazor'</span></span>
- <span data-ttu-id="f0abd-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f0abd-104">'Identity'</span></span>
- <span data-ttu-id="f0abd-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f0abd-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="f0abd-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f0abd-106">'Razor'</span></span>
- <span data-ttu-id="f0abd-107">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="f0abd-107">'SignalR' uid:</span></span> 

---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="f0abd-108">Debug ASP.NET Core Blazor Webassembly</span><span class="sxs-lookup"><span data-stu-id="f0abd-108">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="f0abd-109">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="f0abd-109">Daniel Roth</span></span>](https://github.com/danroth27)

Blazor<span data-ttu-id="f0abd-110">È possibile eseguire il debug delle app webassembly usando gli strumenti di sviluppo del browser nei browser basati su cromo (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="f0abd-110"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>  <span data-ttu-id="f0abd-111">In alternativa è possibile eseguire il debug dell'app usando Visual Studio o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="f0abd-111">Alternatively you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="f0abd-112">Gli scenari disponibili includono:</span><span class="sxs-lookup"><span data-stu-id="f0abd-112">Available scenarios include:</span></span>

* <span data-ttu-id="f0abd-113">Impostare e rimuovere punti di interruzione.</span><span class="sxs-lookup"><span data-stu-id="f0abd-113">Set and remove breakpoints.</span></span>
* <span data-ttu-id="f0abd-114">Eseguire l'app con supporto per il debug in Visual Studio e Visual Studio Code (supporto<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="f0abd-114">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="f0abd-115">Singolo passaggio (<kbd>F10</kbd>) tramite il codice.</span><span class="sxs-lookup"><span data-stu-id="f0abd-115">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="f0abd-116">Riprendere l'esecuzione del codice con <kbd>F8</kbd> in un browser o <kbd>F5</kbd> in Visual Studio o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="f0abd-116">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="f0abd-117">Nella visualizzazione variabili *locali* osservare i valori delle variabili locali.</span><span class="sxs-lookup"><span data-stu-id="f0abd-117">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="f0abd-118">Vedere lo stack di chiamate, incluse le catene di chiamate che passano da JavaScript a .NET e da .NET a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f0abd-118">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="f0abd-119">Per il momento *non è possibile*:</span><span class="sxs-lookup"><span data-stu-id="f0abd-119">For now, you *can't*:</span></span>

* <span data-ttu-id="f0abd-120">Interrompi in corrispondenza di eccezioni non gestite.</span><span class="sxs-lookup"><span data-stu-id="f0abd-120">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="f0abd-121">Raggiunge i punti di interruzione durante l'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="f0abd-121">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="f0abd-122">Si continuerà a migliorare l'esperienza di debug nelle prossime versioni.</span><span class="sxs-lookup"><span data-stu-id="f0abd-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f0abd-123">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="f0abd-123">Prerequisites</span></span>

<span data-ttu-id="f0abd-124">Il debug richiede uno dei seguenti browser:</span><span class="sxs-lookup"><span data-stu-id="f0abd-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="f0abd-125">Microsoft Edge (versione 80 o successiva)</span><span class="sxs-lookup"><span data-stu-id="f0abd-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="f0abd-126">Google Chrome (versione 70 o successiva)</span><span class="sxs-lookup"><span data-stu-id="f0abd-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="f0abd-127">Abilitare il debug per Visual Studio e Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f0abd-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="f0abd-128">Per abilitare il debug per un' Blazor app webassembly esistente, aggiornare il file *launchSettings. JSON* nel progetto di avvio per includere la `inspectUri` proprietà seguente in ogni profilo di avvio:</span><span class="sxs-lookup"><span data-stu-id="f0abd-128">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="f0abd-129">Al termine dell'aggiornamento, il file *launchSettings. JSON* dovrebbe avere un aspetto simile all'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="f0abd-129">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="f0abd-130">`inspectUri`Proprietà:</span><span class="sxs-lookup"><span data-stu-id="f0abd-130">The `inspectUri` property:</span></span>

* <span data-ttu-id="f0abd-131">Consente all'IDE di rilevare che l'app è un' Blazor app webassembly.</span><span class="sxs-lookup"><span data-stu-id="f0abd-131">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="f0abd-132">Indica all'infrastruttura di debug degli script di connettersi al browser tramite il Blazor proxy di debug.</span><span class="sxs-lookup"><span data-stu-id="f0abd-132">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="f0abd-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f0abd-133">Visual Studio</span></span>

<span data-ttu-id="f0abd-134">Per eseguire il debug di un' Blazor app webassembly in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="f0abd-134">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="f0abd-135">Creare una nuova app ASP.NET Core Blazor webassembly ospitata.</span><span class="sxs-lookup"><span data-stu-id="f0abd-135">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="f0abd-136">Premere <kbd>F5</kbd> per eseguire l'app nel debugger.</span><span class="sxs-lookup"><span data-stu-id="f0abd-136">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="f0abd-137">Impostare un punto di interruzione in *Counter. Razor* nel `IncrementCount` metodo.</span><span class="sxs-lookup"><span data-stu-id="f0abd-137">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="f0abd-138">Passare alla scheda **contatore** e selezionare il pulsante per raggiungere il punto di interruzione:</span><span class="sxs-lookup"><span data-stu-id="f0abd-138">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Contatore debug](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="f0abd-140">Verificare il valore del `currentCount` campo nella finestra variabili locali:</span><span class="sxs-lookup"><span data-stu-id="f0abd-140">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Visualizza variabili locali](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="f0abd-142">Premere <kbd>F5</kbd> per continuare l'esecuzione.</span><span class="sxs-lookup"><span data-stu-id="f0abd-142">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="f0abd-143">Quando si esegue il debug dell' Blazor app webassembly, è anche possibile eseguire il debug del codice del server:</span><span class="sxs-lookup"><span data-stu-id="f0abd-143">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="f0abd-144">Impostare un punto di interruzione nella pagina *fetchData. Razor* in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="f0abd-144">Set a breakpoint in the *FetchData.razor* page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="f0abd-145">Impostare un punto di interruzione in `WeatherForecastController` nel `Get` metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="f0abd-145">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="f0abd-146">Passare alla scheda **Recupera dati** per raggiungere il primo punto di interruzione nel `FetchData` componente appena prima di eseguire una richiesta HTTP al server:</span><span class="sxs-lookup"><span data-stu-id="f0abd-146">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Eseguire il debug dei dati di recupero](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="f0abd-148">Premere <kbd>F5</kbd> per continuare l'esecuzione e quindi raggiungere il punto di interruzione sul server nell' `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="f0abd-148">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Server di debug](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="f0abd-150">Premere di nuovo <kbd>F5</kbd> per consentire l'esecuzione continua e visualizzare la tabella delle previsioni meteorologiche sottoposta a rendering.</span><span class="sxs-lookup"><span data-stu-id="f0abd-150">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="f0abd-151">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f0abd-151">Visual Studio Code</span></span>

<span data-ttu-id="f0abd-152">Per eseguire il debug di un' Blazor app webassembly in Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="f0abd-152">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="f0abd-153">Installare l' [estensione C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e l'estensione del [debugger JavaScript (notturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con `debug.javascript.usePreview` impostato su `true` .</span><span class="sxs-lookup"><span data-stu-id="f0abd-153">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Estensioni](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Debugger di anteprima JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="f0abd-156">Aprire un' Blazor app webassembly esistente con il debug abilitato.</span><span class="sxs-lookup"><span data-stu-id="f0abd-156">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="f0abd-157">Se si riceve la notifica seguente che è necessaria un'installazione aggiuntiva per abilitare il debug, verificare che siano state installate le estensioni corrette e che sia stato abilitato il debug dell'anteprima JavaScript e quindi ricaricare la finestra:</span><span class="sxs-lookup"><span data-stu-id="f0abd-157">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Installazione aggiuntiva richiesta](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="f0abd-159">Una notifica consente di aggiungere le risorse necessarie all'app per la compilazione e il debug.</span><span class="sxs-lookup"><span data-stu-id="f0abd-159">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="f0abd-160">Selezionare **Sì**:</span><span class="sxs-lookup"><span data-stu-id="f0abd-160">Select **Yes**:</span></span>

     ![Aggiungi asset necessari](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="f0abd-162">L'avvio dell'app nel debugger è un processo in due passaggi:</span><span class="sxs-lookup"><span data-stu-id="f0abd-162">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="f0abd-163">1 \.</span><span class="sxs-lookup"><span data-stu-id="f0abd-163">1\.</span></span> <span data-ttu-id="f0abd-164">Per **prima cosa**, avviare l'app usando la configurazione di avvio di **.NET Core ( Blazor standalone)** .</span><span class="sxs-lookup"><span data-stu-id="f0abd-164">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="f0abd-165">2 \.</span><span class="sxs-lookup"><span data-stu-id="f0abd-165">2\.</span></span> <span data-ttu-id="f0abd-166">**Dopo che l'app è stata avviata**, avviare il browser usando l' \*\* Blazor assembly Web di debug di .NET Core nella configurazione di avvio di Chrome\*\* (richiede Chrome).</span><span class="sxs-lookup"><span data-stu-id="f0abd-166">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="f0abd-167">Per usare Edge anziché Chrome, modificare la `type` della configurazione di avvio in *. VSCODE/Launch. JSON* da `pwa-chrome` a `pwa-msedge` .</span><span class="sxs-lookup"><span data-stu-id="f0abd-167">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="f0abd-168">Impostare un punto di interruzione nel `IncrementCount` metodo nel `Counter` componente, quindi selezionare il pulsante per raggiungere il punto di interruzione:</span><span class="sxs-lookup"><span data-stu-id="f0abd-168">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Esegui il debug del contatore in VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="f0abd-170">Debug nel browser</span><span class="sxs-lookup"><span data-stu-id="f0abd-170">Debug in the browser</span></span>

1. <span data-ttu-id="f0abd-171">Eseguire una build di debug dell'app nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="f0abd-171">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="f0abd-172">Premere <kbd>MAIUSC</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="f0abd-172">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="f0abd-173">Il browser deve essere eseguito con il debug remoto abilitato.</span><span class="sxs-lookup"><span data-stu-id="f0abd-173">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="f0abd-174">Se il debug remoto è disabilitato, viene generata una pagina di errore della **scheda del browser di cui è possibile eseguire il debug** .</span><span class="sxs-lookup"><span data-stu-id="f0abd-174">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="f0abd-175">La pagina di errore contiene le istruzioni per eseguire il browser con la porta di debug aperta, in modo che il Blazor proxy di debug possa connettersi all'app.</span><span class="sxs-lookup"><span data-stu-id="f0abd-175">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="f0abd-176">*Chiudere tutte le istanze del browser* e riavviare il browser come indicato.</span><span class="sxs-lookup"><span data-stu-id="f0abd-176">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="f0abd-177">Quando il browser è in esecuzione con il debug remoto abilitato, il tasto di scelta rapida di debug apre una nuova scheda del debugger. Dopo qualche istante la scheda **Sources (origini** ) Mostra un elenco degli assembly .NET nell'app.</span><span class="sxs-lookup"><span data-stu-id="f0abd-177">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="f0abd-178">Espandere ogni assembly e trovare i file di origine *. cs* / *. Razor* disponibili per il debug.</span><span class="sxs-lookup"><span data-stu-id="f0abd-178">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="f0abd-179">Impostare i punti di interruzione, tornare alla scheda dell'app e i punti di interruzione vengono raggiunti quando viene eseguito il codice.</span><span class="sxs-lookup"><span data-stu-id="f0abd-179">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="f0abd-180">Quando viene raggiunto un punto di interruzione, l'esecuzione<kbd>F10</kbd>del codice in un singolo passaggio (F10<kbd>) viene</kbd>eseguito normalmente.</span><span class="sxs-lookup"><span data-stu-id="f0abd-180">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="f0abd-181">fornisce un proxy di debug che implementa il [protocollo devtools di Chrome](https://chromedevtools.github.io/devtools-protocol/) e potenzia il protocollo con. Informazioni specifiche del NET.</span><span class="sxs-lookup"><span data-stu-id="f0abd-181"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="f0abd-182">Quando si preme il tasto di scelta rapida Blazor per il debug, punta il devtools di Chrome sul proxy.</span><span class="sxs-lookup"><span data-stu-id="f0abd-182">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="f0abd-183">Il proxy si connette alla finestra del browser che si sta tentando di eseguire il debug, quindi è necessario abilitare il debug remoto.</span><span class="sxs-lookup"><span data-stu-id="f0abd-183">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="f0abd-184">Mappe di origine del browser</span><span class="sxs-lookup"><span data-stu-id="f0abd-184">Browser source maps</span></span>

<span data-ttu-id="f0abd-185">Le mappe di origine del browser consentono al browser di eseguire il mapping dei file compilati ai file di origine originali e vengono comunemente usati per il debug sul lato client.</span><span class="sxs-lookup"><span data-stu-id="f0abd-185">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="f0abd-186">Tuttavia, Blazor attualmente non esegue il mapping di C# direttamente a JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="f0abd-186">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="f0abd-187">Al contrario, Blazor l'interpretazione il nel browser, quindi le mappe di origine non sono rilevanti.</span><span class="sxs-lookup"><span data-stu-id="f0abd-187">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f0abd-188">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="f0abd-188">Troubleshoot</span></span>

<span data-ttu-id="f0abd-189">Se si verificano errori, è possibile che venga utile il suggerimento seguente:</span><span class="sxs-lookup"><span data-stu-id="f0abd-189">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="f0abd-190">Nella scheda **debugger** aprire gli strumenti di sviluppo nel browser.</span><span class="sxs-lookup"><span data-stu-id="f0abd-190">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="f0abd-191">Nella console eseguire `localStorage.clear()` per rimuovere tutti i punti di interruzione.</span><span class="sxs-lookup"><span data-stu-id="f0abd-191">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
