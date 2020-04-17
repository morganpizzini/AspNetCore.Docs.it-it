---
title: Debug ASP.NET Blazor base WebAssembly
author: guardrex
description: Scopri come Blazor eseguire il debug delle app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 8b63444ba5c8cd45e64e722c8978ba4e6d90af36
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488748"
---
# <a name="debug-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="036a8-103">Debug ASP.NET Blazor base WebAssembly</span><span class="sxs-lookup"><span data-stu-id="036a8-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="036a8-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="036a8-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="036a8-105">Le app WebAssembly possono essere sottoposte a debug utilizzando gli strumenti di sviluppo del browser nei browser basati su Chromium (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="036a8-105"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>  <span data-ttu-id="036a8-106">In alternativa, è possibile eseguire il debug dell'app usando Visual Studio o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="036a8-106">Alternatively you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="036a8-107">Gli scenari disponibili includono:</span><span class="sxs-lookup"><span data-stu-id="036a8-107">Available scenarios include:</span></span>

* <span data-ttu-id="036a8-108">Impostare e rimuovere i punti di interruzione.</span><span class="sxs-lookup"><span data-stu-id="036a8-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="036a8-109">Eseguire l'app con il supporto per il debug in Visual Studio e Visual Studio Code (supporto<kbd>F5).</kbd></span><span class="sxs-lookup"><span data-stu-id="036a8-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="036a8-110">Singolo passaggio (<kbd>F10</kbd>) tramite il codice.</span><span class="sxs-lookup"><span data-stu-id="036a8-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="036a8-111">Riprendere l'esecuzione del codice con <kbd>F8</kbd> in un browser o <kbd>F5</kbd> in Visual Studio o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="036a8-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="036a8-112">Nella visualizzazione *Variabili locali* osservare i valori delle variabili locali.</span><span class="sxs-lookup"><span data-stu-id="036a8-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="036a8-113">Vedere lo stack di chiamate, incluse le catene di chiamate che vanno da JavaScript in .NET e da .NET a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="036a8-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="036a8-114">Per ora, *non è possibile:*</span><span class="sxs-lookup"><span data-stu-id="036a8-114">For now, you *can't*:</span></span>

* <span data-ttu-id="036a8-115">Esaminare gli array.</span><span class="sxs-lookup"><span data-stu-id="036a8-115">Inspect arrays.</span></span>
* <span data-ttu-id="036a8-116">Passare il mouse per esaminare i membri.</span><span class="sxs-lookup"><span data-stu-id="036a8-116">Hover to inspect members.</span></span>
* <span data-ttu-id="036a8-117">Eseguire il debug all'azione di debug o all'esterno del codice gestito.</span><span class="sxs-lookup"><span data-stu-id="036a8-117">Step debug into or out of managed code.</span></span>
* <span data-ttu-id="036a8-118">Disporre del supporto completo per l'ispezione dei tipi di valore.</span><span class="sxs-lookup"><span data-stu-id="036a8-118">Have full support for inspecting value types.</span></span>
* <span data-ttu-id="036a8-119">Interruzione delle eccezioni non gestite.</span><span class="sxs-lookup"><span data-stu-id="036a8-119">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="036a8-120">Raggiungi i punti di interruzione durante l'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="036a8-120">Hit breakpoints during app startup.</span></span>
* <span data-ttu-id="036a8-121">Eseguire il debug di un'app con un service worker.</span><span class="sxs-lookup"><span data-stu-id="036a8-121">Debug an app with a service worker.</span></span>

<span data-ttu-id="036a8-122">Continueremo a migliorare l'esperienza di debug nelle versioni future.</span><span class="sxs-lookup"><span data-stu-id="036a8-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="036a8-123">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="036a8-123">Prerequisites</span></span>

<span data-ttu-id="036a8-124">Il debug richiede uno dei seguenti browser:</span><span class="sxs-lookup"><span data-stu-id="036a8-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="036a8-125">Microsoft Edge (versione 80 o successiva)</span><span class="sxs-lookup"><span data-stu-id="036a8-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="036a8-126">Google Chrome (versione 70 o successiva)</span><span class="sxs-lookup"><span data-stu-id="036a8-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="036a8-127">Abilitare il debug per Visual Studio e Visual Studio CodeEnable debugging for Visual Studio and Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="036a8-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="036a8-128">Il debug viene abilitato automaticamente per i nuovi progetti creati utilizzando Blazor il modello di progetto WebAssembly di ASP.NET Core 3.2 Preview 3 o versione successiva[(la versione corrente è 3.2 Preview 4](xref:blazor/get-started)).</span><span class="sxs-lookup"><span data-stu-id="036a8-128">Debugging is enabled automatically for new projects that are created using the ASP.NET Core 3.2 Preview 3 or later Blazor WebAssembly project template ([current release is 3.2 Preview 4](xref:blazor/get-started)).</span></span>

<span data-ttu-id="036a8-129">Per abilitare il Blazor debug per un'app WebAssembly esistente, aggiornare il `inspectUri` file *launchSettings.json* nel progetto di avvio in modo da includere la proprietà seguente in ogni profilo di avvio:</span><span class="sxs-lookup"><span data-stu-id="036a8-129">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="036a8-130">Una volta aggiornato, il file launchSettings.json dovrebbe essere simile all'esempio seguente:Once updated, the *launchSettings.json* file should look similar to the following example:</span><span class="sxs-lookup"><span data-stu-id="036a8-130">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="036a8-131">La `inspectUri` proprietà:</span><span class="sxs-lookup"><span data-stu-id="036a8-131">The `inspectUri` property:</span></span>

* <span data-ttu-id="036a8-132">Consente all'IDE di rilevare Blazor che l'app è un'app WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="036a8-132">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="036a8-133">Indica all'infrastruttura di debug degli script Blazordi connettersi al browser tramite il proxy di debug di 's.</span><span class="sxs-lookup"><span data-stu-id="036a8-133">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="036a8-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="036a8-134">Visual Studio</span></span>

<span data-ttu-id="036a8-135">Per eseguire Blazor il debug di un'app WebAssembly in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="036a8-135">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="036a8-136">Assicurarsi di aver [installato la versione di anteprima più recente di Visual Studio 2019 16.6](https://visualstudio.com/preview) (anteprima 2 o successiva).</span><span class="sxs-lookup"><span data-stu-id="036a8-136">Ensure you have [installed the latest preview release of Visual Studio 2019 16.6](https://visualstudio.com/preview) (Preview 2 or later).</span></span>
1. <span data-ttu-id="036a8-137">Creare una nuova app Blazor WebAssembly ospitata ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="036a8-137">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="036a8-138">Premere <kbd>F5</kbd> per eseguire l'app nel debugger.</span><span class="sxs-lookup"><span data-stu-id="036a8-138">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="036a8-139">Impostare un punto di interruzione `IncrementCount` in *Counter.razor* nel metodo.</span><span class="sxs-lookup"><span data-stu-id="036a8-139">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="036a8-140">Passare alla scheda **Contatore** e selezionare il pulsante per raggiungere il punto di interruzione:</span><span class="sxs-lookup"><span data-stu-id="036a8-140">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Contatore di debug](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="036a8-142">Scopri il valore `currentCount` del campo nella finestra della gente del posto:</span><span class="sxs-lookup"><span data-stu-id="036a8-142">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Visualizza la gente del posto](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="036a8-144">Premere <kbd>F5</kbd> per continuare l'esecuzione.</span><span class="sxs-lookup"><span data-stu-id="036a8-144">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="036a8-145">Durante il Blazor debug dell'app WebAssembly, è anche possibile eseguire il debug del codice server:</span><span class="sxs-lookup"><span data-stu-id="036a8-145">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="036a8-146">Impostare un punto di interruzione nella `OnInitializedAsync`pagina *FetchData.razor* in .</span><span class="sxs-lookup"><span data-stu-id="036a8-146">Set a breakpoint in the *FetchData.razor* page in `OnInitializedAsync`.</span></span>
1. <span data-ttu-id="036a8-147">Impostare un `WeatherForecastController` punto `Get` di interruzione nel nel metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="036a8-147">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="036a8-148">Passare alla scheda **Recupera dati** per raggiungere `FetchData` il primo punto di interruzione nel componente appena prima di emettere una richiesta HTTP al server:</span><span class="sxs-lookup"><span data-stu-id="036a8-148">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Debug Fetch Data](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="036a8-150">Premere <kbd>F5</kbd> per continuare l'esecuzione e quindi `WeatherForecastController`premere il punto di interruzione sul server nel:</span><span class="sxs-lookup"><span data-stu-id="036a8-150">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Server di debug](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="036a8-152">Premere di nuovo <kbd>F5</kbd> per consentire l'esecuzione continuare e visualizzare la tabella delle previsioni meteo renderizzata.</span><span class="sxs-lookup"><span data-stu-id="036a8-152">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

## <a name="visual-studio-code"></a><span data-ttu-id="036a8-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="036a8-153">Visual Studio Code</span></span>

<span data-ttu-id="036a8-154">Per eseguire Blazor il debug di un'app WebAssembly nel codice di Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="036a8-154">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="036a8-155">Installare [l'estensione di C,](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e l'estensione [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con `debug.javascript.usePreview` impostato su `true`.</span><span class="sxs-lookup"><span data-stu-id="036a8-155">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Estensioni](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Debugger di anteprima JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="036a8-158">Aprire un'app WebAssembly esistente Blazor con il debug abilitato.</span><span class="sxs-lookup"><span data-stu-id="036a8-158">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="036a8-159">Se viene visualizzata la seguente notifica che è necessaria un'installazione aggiuntiva per abilitare il debug, verificare che siano attivate le estensioni corrette e che sia abilitato il debug dell'anteprima JavaScript, quindi ricaricare la finestra:</span><span class="sxs-lookup"><span data-stu-id="036a8-159">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Configurazione aggiuntiva riprogrammata](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="036a8-161">Viene offerta una notifica per aggiungere le risorse necessarie all'app per la compilazione e il debug.</span><span class="sxs-lookup"><span data-stu-id="036a8-161">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="036a8-162">Selezionare **Sì**:</span><span class="sxs-lookup"><span data-stu-id="036a8-162">Select **Yes**:</span></span>

     ![Aggiungere le risorse richieste](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="036a8-164">L'avvio dell'app nel debugger è un processo in due passaggi:Starting the app in the debugger is a two-step process:</span><span class="sxs-lookup"><span data-stu-id="036a8-164">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="036a8-165">1\.</span><span class="sxs-lookup"><span data-stu-id="036a8-165">1\.</span></span> <span data-ttu-id="036a8-166">**Innanzitutto,** avviare l'app utilizzando la configurazione di avvio \*\*di .NET Core (Standalone).Blazor \*\*</span><span class="sxs-lookup"><span data-stu-id="036a8-166">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="036a8-167">2\.</span><span class="sxs-lookup"><span data-stu-id="036a8-167">2\.</span></span> <span data-ttu-id="036a8-168">**Dopo l'avvio dell'app,** avviare il browser utilizzando l'assembly Web di \*\*debug Blazor \*\* di .NET Core nella configurazione di avvio di Chrome (richiede Chrome).</span><span class="sxs-lookup"><span data-stu-id="036a8-168">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="036a8-169">Per utilizzare Edge invece `type` di Chrome, modificare la configurazione di `pwa-chrome` avvio in *.vscode/launch.json* da a `pwa-msedge`.</span><span class="sxs-lookup"><span data-stu-id="036a8-169">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="036a8-170">Impostare un `IncrementCount` punto di `Counter` interruzione nel metodo nel componente, quindi selezionare il pulsante per raggiungere il punto di interruzione:Set a breakpoint in the method in the component and then select the button to hit the breakpoint:</span><span class="sxs-lookup"><span data-stu-id="036a8-170">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Debug Counter in VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="036a8-172">Eseguire il debug nel browserDebug in the browser</span><span class="sxs-lookup"><span data-stu-id="036a8-172">Debug in the browser</span></span>

1. <span data-ttu-id="036a8-173">Eseguire una build di debug dell'app nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="036a8-173">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="036a8-174"><kbd>Premete Maiusc</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="036a8-174">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="036a8-175">Il browser deve essere eseguito con il debug remoto abilitato.</span><span class="sxs-lookup"><span data-stu-id="036a8-175">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="036a8-176">Se il debug remoto è disabilitato, viene generata una pagina di errore **Impossibile trovare la scheda del browser di cui** è possibile eseguire il debug.</span><span class="sxs-lookup"><span data-stu-id="036a8-176">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="036a8-177">La pagina di errore contiene le istruzioni per l'esecuzione del browser con la porta di debug aperta in modo che il Blazor proxy di debug possa connettersi all'app.</span><span class="sxs-lookup"><span data-stu-id="036a8-177">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="036a8-178">*Chiudere tutte le istanze del browser* e riavviare il browser come indicato.</span><span class="sxs-lookup"><span data-stu-id="036a8-178">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="036a8-179">Una volta che il browser è in esecuzione con il debug remoto abilitato, il tasto di scelta rapida di debug apre una nuova scheda del debugger. Dopo qualche istante, la scheda **Origini** mostra un elenco degli assembly .NET nell'app.</span><span class="sxs-lookup"><span data-stu-id="036a8-179">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="036a8-180">Espandere ogni assembly e trovare i file di origine *con estensione cs*/*Razor* disponibili per il debug.</span><span class="sxs-lookup"><span data-stu-id="036a8-180">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="036a8-181">Impostare i punti di interruzione, tornare alla scheda dell'app e i punti di interruzione vengono raggiunti quando viene eseguito il codice.</span><span class="sxs-lookup"><span data-stu-id="036a8-181">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="036a8-182">Dopo aver raggiunto un punto di interruzione, il codice viene sottoposto a un singolo passaggio (<kbd>F10</kbd>) tramite il codice o riprendere (<kbd>F8</kbd>) l'esecuzione del codice normalmente.</span><span class="sxs-lookup"><span data-stu-id="036a8-182">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="036a8-183">fornisce un proxy di debug che implementa il [protocollo Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) e aumenta il protocollo con . Informazioni specifiche di NET.</span><span class="sxs-lookup"><span data-stu-id="036a8-183"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="036a8-184">Quando si preme Blazor il debug dei tasti di scelta rapida, punta Chrome DevTools al proxy.</span><span class="sxs-lookup"><span data-stu-id="036a8-184">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="036a8-185">Il proxy si connette alla finestra del browser che si sta cercando di eseguire il debug (da qui la necessità di abilitare il debug remoto).</span><span class="sxs-lookup"><span data-stu-id="036a8-185">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="036a8-186">Mappe sorgente del browser</span><span class="sxs-lookup"><span data-stu-id="036a8-186">Browser source maps</span></span>

<span data-ttu-id="036a8-187">Le mappe di origine del browser consentono al browser di eseguire il mapping dei file compilati ai file di origine originali e vengono comunemente utilizzate per il debug sul lato client.</span><span class="sxs-lookup"><span data-stu-id="036a8-187">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="036a8-188">Tuttavia, Blazor al momento non esegue il mapping di C , direttamente a JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="036a8-188">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="036a8-189">Al Blazor contrario, l'interpretazione IL all'interno del browser, pertanto le mappe di origine non sono rilevanti.</span><span class="sxs-lookup"><span data-stu-id="036a8-189">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="036a8-190">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="036a8-190">Troubleshoot</span></span>

<span data-ttu-id="036a8-191">Se si verificano errori, il suggerimento seguente può essere utile:</span><span class="sxs-lookup"><span data-stu-id="036a8-191">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="036a8-192">Nella scheda **Debugger** aprire gli strumenti di sviluppo nel browser.</span><span class="sxs-lookup"><span data-stu-id="036a8-192">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="036a8-193">Nella console, `localStorage.clear()` eseguire per rimuovere eventuali punti di interruzione.</span><span class="sxs-lookup"><span data-stu-id="036a8-193">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
