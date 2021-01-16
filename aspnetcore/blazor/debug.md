---
title: ASP.NET Core di debug Blazor WebAssembly
author: guardrex
description: Informazioni su come eseguire il debug delle Blazor app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 5bdfcc5660b4c897d3552d4cf25e43dade71541c
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252513"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="25c65-103">ASP.NET Core di debug Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="25c65-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="25c65-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="25c65-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="25c65-105">Blazor WebAssembly è possibile eseguire il debug delle app usando gli strumenti di sviluppo del browser nei browser basati su cromo (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="25c65-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="25c65-106">È anche possibile eseguire il debug dell'app usando gli ambienti di sviluppo integrato (IDE) seguenti:</span><span class="sxs-lookup"><span data-stu-id="25c65-106">You can also debug your app using the following integrated development environments (IDEs):</span></span>

* <span data-ttu-id="25c65-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="25c65-107">Visual Studio</span></span>
* <span data-ttu-id="25c65-108">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="25c65-108">Visual Studio for Mac</span></span>
* <span data-ttu-id="25c65-109">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="25c65-109">Visual Studio Code</span></span>

<span data-ttu-id="25c65-110">Gli scenari disponibili includono:</span><span class="sxs-lookup"><span data-stu-id="25c65-110">Available scenarios include:</span></span>

* <span data-ttu-id="25c65-111">Impostare e rimuovere punti di interruzione.</span><span class="sxs-lookup"><span data-stu-id="25c65-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="25c65-112">Eseguire l'app con supporto per il debug in IDE.</span><span class="sxs-lookup"><span data-stu-id="25c65-112">Run the app with debugging support in IDEs.</span></span>
* <span data-ttu-id="25c65-113">Singolo passaggio del codice.</span><span class="sxs-lookup"><span data-stu-id="25c65-113">Single-step through the code.</span></span>
* <span data-ttu-id="25c65-114">Riprendere l'esecuzione del codice con un tasto di scelta rapida negli IDE.</span><span class="sxs-lookup"><span data-stu-id="25c65-114">Resume code execution with a keyboard shortcut in IDEs.</span></span>
* <span data-ttu-id="25c65-115">Nella finestra variabili *locali* osservare i valori delle variabili locali.</span><span class="sxs-lookup"><span data-stu-id="25c65-115">In the *Locals* window, observe the values of local variables.</span></span>
* <span data-ttu-id="25c65-116">Vedere lo stack di chiamate, incluse le catene di chiamate tra JavaScript e .NET.</span><span class="sxs-lookup"><span data-stu-id="25c65-116">See the call stack, including call chains between JavaScript and .NET.</span></span>

<span data-ttu-id="25c65-117">Per il momento *non è possibile*:</span><span class="sxs-lookup"><span data-stu-id="25c65-117">For now, you *can't*:</span></span>

* <span data-ttu-id="25c65-118">Interrompi in corrispondenza di eccezioni non gestite.</span><span class="sxs-lookup"><span data-stu-id="25c65-118">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="25c65-119">Premere i punti di interruzione durante l'avvio dell'app prima che il proxy di debug sia in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="25c65-119">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="25c65-120">Sono inclusi i punti di interruzione in `Program.Main` ( `Program.cs` ) e i punti di interruzione nei [ `OnInitialized{Async}` Metodi](xref:blazor/components/lifecycle#component-initialization-methods) dei componenti caricati dalla prima pagina richiesta dall'app.</span><span class="sxs-lookup"><span data-stu-id="25c65-120">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>
* <span data-ttu-id="25c65-121">Eseguire il debug in scenari non locali (ad esempio, il [sottosistema Windows per Linux (WSL)](/windows/wsl/) o gli spazi dei nomi di [Visual Studio](/visualstudio/codespaces/overview/what-is-vsonline)).</span><span class="sxs-lookup"><span data-stu-id="25c65-121">Debug in non-local scenarios (for example, [Windows Subsystem for Linux (WSL)](/windows/wsl/) or [Visual Studio Codespaces](/visualstudio/codespaces/overview/what-is-vsonline)).</span></span>
* <span data-ttu-id="25c65-122">Ricompilare automaticamente l'app back-end `*Server*` di una soluzione ospitata Blazor durante il debug, ad esempio eseguendo l'app con [`dotnet watch run`](xref:tutorials/dotnet-watch) .</span><span class="sxs-lookup"><span data-stu-id="25c65-122">Automatically rebuild the backend `*Server*` app of a hosted Blazor solution during debugging, for example by running the app with [`dotnet watch run`](xref:tutorials/dotnet-watch).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="25c65-123">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="25c65-123">Prerequisites</span></span>

<span data-ttu-id="25c65-124">Il debug richiede uno dei seguenti browser:</span><span class="sxs-lookup"><span data-stu-id="25c65-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="25c65-125">Google Chrome (versione 70 o successiva) (impostazione predefinita)</span><span class="sxs-lookup"><span data-stu-id="25c65-125">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="25c65-126">Microsoft Edge (versione 80 o successiva)</span><span class="sxs-lookup"><span data-stu-id="25c65-126">Microsoft Edge (version 80 or later)</span></span>

<span data-ttu-id="25c65-127">Assicurarsi che i firewall o i proxy non blocchino le comunicazioni con il proxy di debug ( `NodeJS` processo).</span><span class="sxs-lookup"><span data-stu-id="25c65-127">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="25c65-128">Per ulteriori informazioni, vedere la sezione relativa alla [configurazione del firewall](#firewall-configuration) .</span><span class="sxs-lookup"><span data-stu-id="25c65-128">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

<span data-ttu-id="25c65-129">Visual Studio per Mac richiede la versione 8,8 (Build 1532) o versioni successive:</span><span class="sxs-lookup"><span data-stu-id="25c65-129">Visual Studio for Mac requires version 8.8 (build 1532) or later:</span></span>

1. <span data-ttu-id="25c65-130">Installare la versione più recente di Visual Studio per Mac selezionando il pulsante **scarica Visual Studio per Mac** in [Microsoft: Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="25c65-130">Install the latest release of Visual Studio for Mac by selecting the **Download Visual Studio for Mac** button at [Microsoft: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>
1. <span data-ttu-id="25c65-131">Selezionare il canale di *Anteprima* dall'interno di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="25c65-131">Select the *Preview* channel from within Visual Studio.</span></span> <span data-ttu-id="25c65-132">Per ulteriori informazioni, vedere [installare una versione di anteprima di Visual Studio per Mac](/visualstudio/mac/install-preview).</span><span class="sxs-lookup"><span data-stu-id="25c65-132">For more information, see [Install a preview version of Visual Studio for Mac](/visualstudio/mac/install-preview).</span></span>

> [!NOTE]
> <span data-ttu-id="25c65-133">Apple Safari in macOS attualmente non è supportato.</span><span class="sxs-lookup"><span data-stu-id="25c65-133">Apple Safari on macOS isn't currently supported.</span></span>

## <a name="enable-debugging"></a><span data-ttu-id="25c65-134">Abilitare il debug</span><span class="sxs-lookup"><span data-stu-id="25c65-134">Enable debugging</span></span>

<span data-ttu-id="25c65-135">Per abilitare il debug per un' Blazor WebAssembly app esistente, aggiornare il `launchSettings.json` file nel progetto di avvio per includere la `inspectUri` proprietà seguente in ogni profilo di avvio:</span><span class="sxs-lookup"><span data-stu-id="25c65-135">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="25c65-136">Al termine dell'aggiornamento, il `launchSettings.json` file dovrebbe essere simile all'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="25c65-136">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="25c65-137">`inspectUri`Proprietà:</span><span class="sxs-lookup"><span data-stu-id="25c65-137">The `inspectUri` property:</span></span>

* <span data-ttu-id="25c65-138">Consente all'IDE di rilevare che l'app è un' Blazor WebAssembly app.</span><span class="sxs-lookup"><span data-stu-id="25c65-138">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="25c65-139">Indica all'infrastruttura di debug degli script di connettersi al browser tramite il Blazor proxy di debug.</span><span class="sxs-lookup"><span data-stu-id="25c65-139">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="25c65-140">I valori segnaposto per i protocolli WebSockets ( `wsProtocol` ), host ( `url.hostname` ), Port ( `url.port` ) e Inspector URI nel browser avviato ( `browserInspectUri` ) sono forniti dal Framework.</span><span class="sxs-lookup"><span data-stu-id="25c65-140">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="25c65-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="25c65-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="25c65-142">Per eseguire il debug di un' Blazor WebAssembly app in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="25c65-142">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="25c65-143">Creare una nuova app ospitata ASP.NET Core Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="25c65-143">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="25c65-144">Premere <kbd>F5</kbd> per eseguire l'app nel debugger.</span><span class="sxs-lookup"><span data-stu-id="25c65-144">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="25c65-145">**Avvia senza eseguire debug** (<kbd>CTRL</kbd> + <kbd>F5</kbd>) non è supportato.</span><span class="sxs-lookup"><span data-stu-id="25c65-145">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="25c65-146">Quando l'app viene eseguita nella configurazione di debug, il sovraccarico del debug comporta sempre una riduzione delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="25c65-146">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="25c65-147">Nell' `*Client*` app, impostare un punto di interruzione nella `currentCount++;` riga in `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="25c65-147">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="25c65-148">Nel browser passare alla `Counter` pagina e selezionare il pulsante **fare clic su me** per raggiungere il punto di interruzione.</span><span class="sxs-lookup"><span data-stu-id="25c65-148">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>
1. <span data-ttu-id="25c65-149">In Visual Studio, controllare il valore del `currentCount` campo nella finestra **variabili locali** .</span><span class="sxs-lookup"><span data-stu-id="25c65-149">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="25c65-150">Premere <kbd>F5</kbd> per continuare l'esecuzione.</span><span class="sxs-lookup"><span data-stu-id="25c65-150">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="25c65-151">Durante il debug di un' Blazor WebAssembly app, è anche possibile eseguire il debug del codice del server:</span><span class="sxs-lookup"><span data-stu-id="25c65-151">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="25c65-152">Impostare un punto di interruzione nella `Pages/FetchData.razor` pagina in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="25c65-152">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="25c65-153">Impostare un punto di interruzione in `WeatherForecastController` nel `Get` metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="25c65-153">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="25c65-154">Passare alla `Fetch Data` pagina per raggiungere il primo punto di interruzione nel `FetchData` componente immediatamente prima di eseguire una richiesta HTTP al server.</span><span class="sxs-lookup"><span data-stu-id="25c65-154">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="25c65-155">Premere <kbd>F5</kbd> per continuare l'esecuzione e quindi raggiungere il punto di interruzione sul server nel `WeatherForecastController` .</span><span class="sxs-lookup"><span data-stu-id="25c65-155">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="25c65-156">Premere di nuovo <kbd>F5</kbd> per consentire l'esecuzione continua e visualizzare la tabella delle previsioni meteorologiche sottoposta a rendering nel browser.</span><span class="sxs-lookup"><span data-stu-id="25c65-156">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="25c65-157">I punti di interruzione **non** vengono raggiunti durante l'avvio dell'app prima che il proxy di debug sia in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="25c65-157">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="25c65-158">Sono inclusi i punti di interruzione in `Program.Main` ( `Program.cs` ) e i punti di interruzione nei [ `OnInitialized{Async}` Metodi](xref:blazor/components/lifecycle#component-initialization-methods) dei componenti caricati dalla prima pagina richiesta dall'app.</span><span class="sxs-lookup"><span data-stu-id="25c65-158">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="25c65-159">Se l'app è ospitata in un [percorso di base dell'app](xref:blazor/host-and-deploy/index#app-base-path) diverso da `/` , aggiornare le proprietà seguenti in `Properties/launchSettings.json` per riflettere il percorso di base dell'app:</span><span class="sxs-lookup"><span data-stu-id="25c65-159">If the app is hosted at a different [app base path](xref:blazor/host-and-deploy/index#app-base-path) than `/`, update the following properties in `Properties/launchSettings.json` to reflect the app's base path:</span></span>

* <span data-ttu-id="25c65-160">`applicationUrl`:</span><span class="sxs-lookup"><span data-stu-id="25c65-160">`applicationUrl`:</span></span>

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* <span data-ttu-id="25c65-161">`inspectUri` di ogni profilo:</span><span class="sxs-lookup"><span data-stu-id="25c65-161">`inspectUri` of each profile:</span></span>

  ```json
  "profiles": {
    ...
    "{PROFILE 1, 2, ... N}": {
      ...
      "inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/{APP BASE PATH}/_framework/debug/ws-proxy?browser={browserInspectUri}",
      ...
    }
  }
  ```

<span data-ttu-id="25c65-162">I segnaposto nelle impostazioni precedenti:</span><span class="sxs-lookup"><span data-stu-id="25c65-162">The placeholders in the preceding settings:</span></span>

* <span data-ttu-id="25c65-163">`{INSECURE PORT}`: Porta non protetta.</span><span class="sxs-lookup"><span data-stu-id="25c65-163">`{INSECURE PORT}`: The insecure port.</span></span> <span data-ttu-id="25c65-164">Per impostazione predefinita viene fornito un valore casuale, ma è consentita una porta personalizzata.</span><span class="sxs-lookup"><span data-stu-id="25c65-164">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="25c65-165">`{APP BASE PATH}`: Percorso di base dell'app.</span><span class="sxs-lookup"><span data-stu-id="25c65-165">`{APP BASE PATH}`: The app's base path.</span></span>
* <span data-ttu-id="25c65-166">`{SECURE PORT}`: Porta protetta.</span><span class="sxs-lookup"><span data-stu-id="25c65-166">`{SECURE PORT}`: The secure port.</span></span> <span data-ttu-id="25c65-167">Per impostazione predefinita viene fornito un valore casuale, ma è consentita una porta personalizzata.</span><span class="sxs-lookup"><span data-stu-id="25c65-167">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="25c65-168">`{PROFILE 1, 2, ... N}`: Avvia profili impostazioni.</span><span class="sxs-lookup"><span data-stu-id="25c65-168">`{PROFILE 1, 2, ... N}`: Launch settings profiles.</span></span> <span data-ttu-id="25c65-169">In genere, un'app specifica più di un profilo per impostazione predefinita (ad esempio, un profilo per IIS Express e un profilo di progetto, che viene usato dal server gheppio).</span><span class="sxs-lookup"><span data-stu-id="25c65-169">Usually, an app specifies more than one profile by default (for example, a profile for IIS Express and a project profile, which is used by Kestrel server).</span></span>

<span data-ttu-id="25c65-170">Negli esempi seguenti l'app è ospitata in `/OAT` con un percorso di base dell'app configurato in `wwwroot/index.html` come `<base href="/OAT/">` :</span><span class="sxs-lookup"><span data-stu-id="25c65-170">In the following examples, the app is hosted at `/OAT` with an app base path configured in `wwwroot/index.html` as `<base href="/OAT/">`:</span></span>

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

<span data-ttu-id="25c65-171">Per informazioni sull'uso di un percorso di base dell'app personalizzato per le Blazor WebAssembly app, vedere <xref:blazor/host-and-deploy/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="25c65-171">For information on using a custom app base path for Blazor WebAssembly apps, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="25c65-172">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="25c65-172">Visual Studio Code</span></span>](#tab/visual-studio-code)

<h2 id="vscode"><span data-ttu-id="25c65-173">Debug autonomo Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="25c65-173">Debug standalone Blazor WebAssembly</span></span></h2>

1. <span data-ttu-id="25c65-174">Aprire l'app autonoma Blazor WebAssembly in vs code.</span><span class="sxs-lookup"><span data-stu-id="25c65-174">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="25c65-175">È possibile che venga ricevuta una notifica che è necessaria un'installazione aggiuntiva per abilitare il debug:</span><span class="sxs-lookup"><span data-stu-id="25c65-175">You may receive a notification that additional setup is required to enable debugging:</span></span>

   > <span data-ttu-id="25c65-176">Per eseguire il debug delle applicazioni, è necessaria un'installazione aggiuntiva Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="25c65-176">Additional setup is required to debug Blazor WebAssembly applications.</span></span>

   <span data-ttu-id="25c65-177">Se si riceve la notifica:</span><span class="sxs-lookup"><span data-stu-id="25c65-177">If you receive the notification:</span></span>

   * <span data-ttu-id="25c65-178">Verificare che sia installata la versione più recente [di C# per Visual Studio Code estensione](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) .</span><span class="sxs-lookup"><span data-stu-id="25c65-178">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="25c65-179">Per esaminare le estensioni installate, aprire **View**  >  **Extensions** dalla barra dei menu o selezionare l'icona **Extensions** nella barra laterale **Activity** .</span><span class="sxs-lookup"><span data-stu-id="25c65-179">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="25c65-180">Verificare che l'anteprima JavaScript sia abilitata.</span><span class="sxs-lookup"><span data-stu-id="25c65-180">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="25c65-181">Aprire le impostazioni dalla barra dei menu (  >    >  **Impostazioni** preferenze file).</span><span class="sxs-lookup"><span data-stu-id="25c65-181">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="25c65-182">Eseguire la ricerca usando le parole chiave `debug preview` .</span><span class="sxs-lookup"><span data-stu-id="25c65-182">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="25c65-183">Nei risultati della ricerca verificare che la casella di controllo per **Debug > JavaScript: USA anteprima** sia selezionata.</span><span class="sxs-lookup"><span data-stu-id="25c65-183">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="25c65-184">Se l'opzione per abilitare il debug in anteprima non è presente, eseguire l'aggiornamento alla versione più recente di VS Code o installare l' [estensione del debugger JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versioni 1,46 o precedenti).</span><span class="sxs-lookup"><span data-stu-id="25c65-184">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="25c65-185">Ricaricare la finestra.</span><span class="sxs-lookup"><span data-stu-id="25c65-185">Reload the window.</span></span>

1. <span data-ttu-id="25c65-186">Avviare il debug usando il tasto di scelta rapida <kbd>F5</kbd> o la voce di menu.</span><span class="sxs-lookup"><span data-stu-id="25c65-186">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="25c65-187">**Avvia senza eseguire debug** (<kbd>CTRL</kbd> + <kbd>F5</kbd>) non è supportato.</span><span class="sxs-lookup"><span data-stu-id="25c65-187">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="25c65-188">Quando l'app viene eseguita nella configurazione di debug, il sovraccarico del debug comporta sempre una riduzione delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="25c65-188">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="25c65-189">Quando richiesto, selezionare l'opzione **Blazor WebAssembly debug** per avviare il debug.</span><span class="sxs-lookup"><span data-stu-id="25c65-189">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

1. <span data-ttu-id="25c65-190">Viene avviata l'app autonoma e viene aperto un browser di debug.</span><span class="sxs-lookup"><span data-stu-id="25c65-190">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="25c65-191">Nell' `*Client*` app, impostare un punto di interruzione nella `currentCount++;` riga in `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="25c65-191">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>

1. <span data-ttu-id="25c65-192">Nel browser passare alla `Counter` pagina e selezionare il pulsante **fare clic su me** per raggiungere il punto di interruzione.</span><span class="sxs-lookup"><span data-stu-id="25c65-192">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>

> [!NOTE]
> <span data-ttu-id="25c65-193">I punti di interruzione **non** vengono raggiunti durante l'avvio dell'app prima che il proxy di debug sia in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="25c65-193">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="25c65-194">Sono inclusi i punti di interruzione in `Program.Main` ( `Program.cs` ) e i punti di interruzione nei [ `OnInitialized{Async}` Metodi](xref:blazor/components/lifecycle#component-initialization-methods) dei componenti caricati dalla prima pagina richiesta dall'app.</span><span class="sxs-lookup"><span data-stu-id="25c65-194">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="25c65-195">Debug ospitato Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="25c65-195">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="25c65-196">Aprire la cartella della soluzione dell'app ospitata Blazor WebAssembly in vs code.</span><span class="sxs-lookup"><span data-stu-id="25c65-196">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="25c65-197">Se non è impostata alcuna configurazione di avvio per il progetto, viene visualizzata la notifica seguente.</span><span class="sxs-lookup"><span data-stu-id="25c65-197">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="25c65-198">Selezionare **Sì**.</span><span class="sxs-lookup"><span data-stu-id="25c65-198">Select **Yes**.</span></span>

   > <span data-ttu-id="25c65-199">Gli asset necessari per la compilazione e il debug non sono presenti in ' {nome applicazione}'.</span><span class="sxs-lookup"><span data-stu-id="25c65-199">Required assets to build and debug are missing from '{APPLICATION NAME}'.</span></span> <span data-ttu-id="25c65-200">e se si vuole aggiungerle.</span><span class="sxs-lookup"><span data-stu-id="25c65-200">Add them?</span></span>

1. <span data-ttu-id="25c65-201">Nel riquadro comandi nella parte superiore della finestra selezionare il progetto *Server* all'interno della soluzione ospitata.</span><span class="sxs-lookup"><span data-stu-id="25c65-201">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="25c65-202">`launch.json`Viene generato un file con la configurazione di avvio per l'avvio del debugger.</span><span class="sxs-lookup"><span data-stu-id="25c65-202">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

## <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="25c65-203">Connetti a una sessione di debug esistente</span><span class="sxs-lookup"><span data-stu-id="25c65-203">Attach to an existing debugging session</span></span>

<span data-ttu-id="25c65-204">Per connettersi a un'app in esecuzione Blazor , creare un `launch.json` file con la configurazione seguente:</span><span class="sxs-lookup"><span data-stu-id="25c65-204">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="25c65-205">Il fissaggio a una sessione di debug è supportato solo per le app autonome.</span><span class="sxs-lookup"><span data-stu-id="25c65-205">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="25c65-206">Per usare il debug dello stack completo, è necessario avviare l'app da VS Code.</span><span class="sxs-lookup"><span data-stu-id="25c65-206">To use full-stack debugging, you must launch the app from VS Code.</span></span>

## <a name="launch-configuration-options"></a><span data-ttu-id="25c65-207">Opzioni di configurazione di avvio</span><span class="sxs-lookup"><span data-stu-id="25c65-207">Launch configuration options</span></span>

<span data-ttu-id="25c65-208">Le seguenti opzioni di configurazione di avvio sono supportate per il `blazorwasm` tipo di debug ( `.vscode/launch.json` ).</span><span class="sxs-lookup"><span data-stu-id="25c65-208">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="25c65-209">Opzione</span><span class="sxs-lookup"><span data-stu-id="25c65-209">Option</span></span>    | <span data-ttu-id="25c65-210">Descrizione</span><span class="sxs-lookup"><span data-stu-id="25c65-210">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="25c65-211">Usare `launch` per avviare e alleghi una sessione di debug a un' Blazor WebAssembly app o `attach` per alleghi una sessione di debug a un'app già in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="25c65-211">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="25c65-212">URL da aprire nel browser durante il debug.</span><span class="sxs-lookup"><span data-stu-id="25c65-212">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="25c65-213">Il valore predefinito è `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="25c65-213">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="25c65-214">Browser da avviare per la sessione di debug.</span><span class="sxs-lookup"><span data-stu-id="25c65-214">The browser to launch for the debugging session.</span></span> <span data-ttu-id="25c65-215">Impostare su `edge` o `chrome`.</span><span class="sxs-lookup"><span data-stu-id="25c65-215">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="25c65-216">Il valore predefinito è `chrome`.</span><span class="sxs-lookup"><span data-stu-id="25c65-216">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="25c65-217">Usato per generare log dal debugger JS.</span><span class="sxs-lookup"><span data-stu-id="25c65-217">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="25c65-218">Impostare su `true` per generare i log.</span><span class="sxs-lookup"><span data-stu-id="25c65-218">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="25c65-219">Deve essere impostato su `true` se si avvia e si esegue il debug di un'app ospitata Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="25c65-219">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="25c65-220">Specifica il percorso assoluto del server Web.</span><span class="sxs-lookup"><span data-stu-id="25c65-220">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="25c65-221">Deve essere impostato se un'app viene gestita da una route secondaria.</span><span class="sxs-lookup"><span data-stu-id="25c65-221">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="25c65-222">Numero di millisecondi di attesa per il fissaggio della sessione di debug.</span><span class="sxs-lookup"><span data-stu-id="25c65-222">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="25c65-223">Il valore predefinito è 30.000 millisecondi (30 secondi).</span><span class="sxs-lookup"><span data-stu-id="25c65-223">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="25c65-224">Riferimento al file eseguibile per eseguire il server dell'app ospitata.</span><span class="sxs-lookup"><span data-stu-id="25c65-224">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="25c65-225">Deve essere impostato se `hosted` è `true` .</span><span class="sxs-lookup"><span data-stu-id="25c65-225">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="25c65-226">Directory di lavoro in cui avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="25c65-226">The working directory to launch the app under.</span></span> <span data-ttu-id="25c65-227">Deve essere impostato se `hosted` è `true` .</span><span class="sxs-lookup"><span data-stu-id="25c65-227">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="25c65-228">Variabili di ambiente da fornire al processo avviato.</span><span class="sxs-lookup"><span data-stu-id="25c65-228">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="25c65-229">Applicabile solo se `hosted` è impostato su `true` .</span><span class="sxs-lookup"><span data-stu-id="25c65-229">Only applicable if `hosted` is set to `true`.</span></span> |

## <a name="example-launch-configurations"></a><span data-ttu-id="25c65-230">Configurazioni di avvio di esempio</span><span class="sxs-lookup"><span data-stu-id="25c65-230">Example launch configurations</span></span>

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="25c65-231">Avviare ed eseguire il debug di un'app autonoma Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="25c65-231">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="25c65-232">Connettersi a un'app in esecuzione in un URL specificato</span><span class="sxs-lookup"><span data-stu-id="25c65-232">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="25c65-233">Avviare ed eseguire il debug di un'app ospitata Blazor WebAssembly con Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="25c65-233">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="25c65-234">Per impostazione predefinita, la configurazione del browser è Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="25c65-234">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="25c65-235">Quando si usa Microsoft Edge per il debug, impostare `browser` su `edge` .</span><span class="sxs-lookup"><span data-stu-id="25c65-235">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="25c65-236">Per usare Google Chrome, non impostare l' `browser` opzione o impostare il valore dell'opzione su `chrome` .</span><span class="sxs-lookup"><span data-stu-id="25c65-236">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

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

<span data-ttu-id="25c65-237">Nell'esempio precedente, `MyHostedApp.Server.dll` è l'assembly dell'app *Server* .</span><span class="sxs-lookup"><span data-stu-id="25c65-237">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="25c65-238">La `.vscode` cartella si trova nella cartella della soluzione accanto alle `Client` `Server` cartelle, e `Shared` .</span><span class="sxs-lookup"><span data-stu-id="25c65-238">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="25c65-239">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="25c65-239">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="25c65-240">Per eseguire il debug di un' Blazor WebAssembly app in Visual Studio per Mac:</span><span class="sxs-lookup"><span data-stu-id="25c65-240">To debug a Blazor WebAssembly app in Visual Studio for Mac:</span></span>

1. <span data-ttu-id="25c65-241">Creare una nuova app ospitata ASP.NET Core Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="25c65-241">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="25c65-242">Premere <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> per eseguire l'applicazione nel debugger.</span><span class="sxs-lookup"><span data-stu-id="25c65-242">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="25c65-243">**Avvia senza eseguire debug** (<kbd>&#8997;</kbd> + <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd>) non è supportato.</span><span class="sxs-lookup"><span data-stu-id="25c65-243">**Start Without Debugging** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) isn't supported.</span></span> <span data-ttu-id="25c65-244">Quando l'app viene eseguita nella configurazione di debug, il sovraccarico del debug comporta sempre una riduzione delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="25c65-244">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="25c65-245">Google Chrome o Microsoft Edge deve essere il browser selezionato per la sessione di debug.</span><span class="sxs-lookup"><span data-stu-id="25c65-245">Google Chrome or Microsoft Edge must be the selected browser for the debugging session.</span></span>

1. <span data-ttu-id="25c65-246">Nell' `*Client*` app, impostare un punto di interruzione nella `currentCount++;` riga in `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="25c65-246">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="25c65-247">Nel browser passare alla `Counter` pagina e selezionare il pulsante **fare clic su me** per raggiungere il punto di interruzione:</span><span class="sxs-lookup"><span data-stu-id="25c65-247">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint:</span></span>
1. <span data-ttu-id="25c65-248">In Visual Studio, controllare il valore del `currentCount` campo nella finestra **variabili locali** .</span><span class="sxs-lookup"><span data-stu-id="25c65-248">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="25c65-249">Premere <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> per continuare l'esecuzione.</span><span class="sxs-lookup"><span data-stu-id="25c65-249">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution.</span></span>

<span data-ttu-id="25c65-250">Durante il debug di un' Blazor WebAssembly app, è anche possibile eseguire il debug del codice del server:</span><span class="sxs-lookup"><span data-stu-id="25c65-250">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="25c65-251">Impostare un punto di interruzione nella `Pages/FetchData.razor` pagina in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="25c65-251">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="25c65-252">Impostare un punto di interruzione in `WeatherForecastController` nel `Get` metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="25c65-252">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="25c65-253">Passare alla `Fetch Data` pagina per raggiungere il primo punto di interruzione nel `FetchData` componente immediatamente prima di eseguire una richiesta HTTP al server.</span><span class="sxs-lookup"><span data-stu-id="25c65-253">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="25c65-254">Premere <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> per continuare l'esecuzione e quindi raggiungere il punto di interruzione sul server nel `WeatherForecastController` .</span><span class="sxs-lookup"><span data-stu-id="25c65-254">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="25c65-255">Premere <kbd></kbd> + di nuovo&#8984;<kbd>&#8617;</kbd> per consentire l'esecuzione continua e visualizzare la tabella delle previsioni meteorologiche sottoposta a rendering nel browser.</span><span class="sxs-lookup"><span data-stu-id="25c65-255">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="25c65-256">I punti di interruzione **non** vengono raggiunti durante l'avvio dell'app prima che il proxy di debug sia in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="25c65-256">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="25c65-257">Sono inclusi i punti di interruzione in `Program.Main` ( `Program.cs` ) e i punti di interruzione nei [ `OnInitialized{Async}` Metodi](xref:blazor/components/lifecycle#component-initialization-methods) dei componenti caricati dalla prima pagina richiesta dall'app.</span><span class="sxs-lookup"><span data-stu-id="25c65-257">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="25c65-258">Per ulteriori informazioni, vedere [debug con Visual Studio per Mac](/visualstudio/mac/debugging).</span><span class="sxs-lookup"><span data-stu-id="25c65-258">For more information, see [Debugging with Visual Studio for Mac](/visualstudio/mac/debugging).</span></span>

---

## <a name="debug-in-the-browser"></a><span data-ttu-id="25c65-259">Debug nel browser</span><span class="sxs-lookup"><span data-stu-id="25c65-259">Debug in the browser</span></span>

<span data-ttu-id="25c65-260">*Le indicazioni fornite in questa sezione si applicano a Google Chrome e Microsoft Edge in esecuzione su Windows.*</span><span class="sxs-lookup"><span data-stu-id="25c65-260">*The guidance in this section applies to Google Chrome and Microsoft Edge running on Windows.*</span></span>

1. <span data-ttu-id="25c65-261">Eseguire una build di debug dell'app nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="25c65-261">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="25c65-262">Avviare un browser e passare all'URL dell'app, ad esempio `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="25c65-262">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="25c65-263">Nel browser provare ad avviare il debug remoto premendo <kbd>MAIUSC</kbd> + <kbd>ALT</kbd> + <kbd>d</kbd>.</span><span class="sxs-lookup"><span data-stu-id="25c65-263">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd>.</span></span>

   <span data-ttu-id="25c65-264">Il browser deve essere in esecuzione con il debug remoto abilitato, che non è il valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="25c65-264">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="25c65-265">Se il debug remoto è disabilitato, viene eseguito il rendering della pagina di errore della **scheda del browser di cui è possibile eseguire** il debug con le istruzioni per avviare il browser con la porta di debug aperta.</span><span class="sxs-lookup"><span data-stu-id="25c65-265">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="25c65-266">Seguire le istruzioni per il browser, che consente di aprire una nuova finestra del browser.</span><span class="sxs-lookup"><span data-stu-id="25c65-266">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="25c65-267">Chiudere la finestra del browser precedente.</span><span class="sxs-lookup"><span data-stu-id="25c65-267">Close the previous browser window.</span></span>

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. <span data-ttu-id="25c65-268">Quando il browser è in esecuzione con il debug remoto abilitato, il tasto di scelta rapida di debug nel passaggio precedente apre una nuova scheda del debugger.</span><span class="sxs-lookup"><span data-stu-id="25c65-268">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut in the previous step opens a new debugger tab.</span></span>

1. <span data-ttu-id="25c65-269">Dopo qualche istante, la scheda **origini** Mostra un elenco degli assembly .NET dell'app all'interno del `file://` nodo.</span><span class="sxs-lookup"><span data-stu-id="25c65-269">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="25c65-270">Nel codice componente ( `.razor` file) e nei file di codice C# ( `.cs` ), i punti di interruzione impostati vengono raggiunti durante l'esecuzione del codice.</span><span class="sxs-lookup"><span data-stu-id="25c65-270">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="25c65-271">Quando viene raggiunto un punto di interruzione, l'esecuzione<kbd></kbd>del codice in un singolo passaggio (F10<kbd>) viene</kbd>eseguito normalmente.</span><span class="sxs-lookup"><span data-stu-id="25c65-271">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="25c65-272">Blazor fornisce un proxy di debug che implementa il [protocollo devtools di Chrome](https://chromedevtools.github.io/devtools-protocol/) e potenzia il protocollo con. Informazioni specifiche del NET.</span><span class="sxs-lookup"><span data-stu-id="25c65-272">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="25c65-273">Quando si preme il tasto di scelta rapida Blazor per il debug, punta il devtools di Chrome sul proxy.</span><span class="sxs-lookup"><span data-stu-id="25c65-273">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="25c65-274">Il proxy si connette alla finestra del browser che si sta tentando di eseguire il debug, quindi è necessario abilitare il debug remoto.</span><span class="sxs-lookup"><span data-stu-id="25c65-274">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="25c65-275">Mappe di origine del browser</span><span class="sxs-lookup"><span data-stu-id="25c65-275">Browser source maps</span></span>

<span data-ttu-id="25c65-276">Le mappe di origine del browser consentono al browser di eseguire il mapping dei file compilati ai file di origine originali e vengono comunemente usati per il debug sul lato client.</span><span class="sxs-lookup"><span data-stu-id="25c65-276">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="25c65-277">Tuttavia, Blazor attualmente non esegue il mapping di C# direttamente a JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="25c65-277">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="25c65-278">Al contrario, Blazor l'interpretazione il nel browser, quindi le mappe di origine non sono rilevanti.</span><span class="sxs-lookup"><span data-stu-id="25c65-278">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="firewall-configuration"></a><span data-ttu-id="25c65-279">Configurazione del firewall</span><span class="sxs-lookup"><span data-stu-id="25c65-279">Firewall configuration</span></span>

<span data-ttu-id="25c65-280">Se un firewall blocca la comunicazione con il proxy di debug, creare una regola di eccezione del firewall che consenta la comunicazione tra il browser e il `NodeJS` processo.</span><span class="sxs-lookup"><span data-stu-id="25c65-280">If a firewall blocks communication with the debug proxy, create a firewall exception rule that permits communication between the browser and the `NodeJS` process.</span></span>

> [!WARNING]
> <span data-ttu-id="25c65-281">La modifica di una configurazione del firewall deve essere eseguita con cautela per evitare la creazione di vulnerablities di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="25c65-281">Modification of a firewall configuration must be made with care to avoid creating security vulnerablities.</span></span> <span data-ttu-id="25c65-282">Applicare con attenzione le linee guida per la sicurezza, attenersi alle procedure di sicurezza consigliate e rispettare gli avvisi emessi dal produttore del firewall.</span><span class="sxs-lookup"><span data-stu-id="25c65-282">Carefully apply security guidance, follow best security practices, and respect warnings issued by the firewall's manufacturer.</span></span>
>
> <span data-ttu-id="25c65-283">Consentire la comunicazione aperta con il `NodeJS` processo:</span><span class="sxs-lookup"><span data-stu-id="25c65-283">Permitting open communication with the `NodeJS` process:</span></span>
>
> * <span data-ttu-id="25c65-284">Apre il server del nodo a qualsiasi connessione, a seconda delle funzionalità e della configurazione del firewall.</span><span class="sxs-lookup"><span data-stu-id="25c65-284">Opens up the Node server to any connection, depending on the firewall's capabilities and configuration.</span></span>
> * <span data-ttu-id="25c65-285">Potrebbe essere rischioso a seconda della rete.</span><span class="sxs-lookup"><span data-stu-id="25c65-285">Might be risky depending on your network.</span></span>
> * <span data-ttu-id="25c65-286">**È consigliato solo nei computer di sviluppo.**</span><span class="sxs-lookup"><span data-stu-id="25c65-286">**Is only recommended on developer machines.**</span></span>
>
> <span data-ttu-id="25c65-287">Se possibile, consentire la comunicazione aperta solo con il `NodeJS` processo **su reti private o attendibili**.</span><span class="sxs-lookup"><span data-stu-id="25c65-287">If possible, only allow open communication with the `NodeJS` process **on trusted or private networks**.</span></span>

<span data-ttu-id="25c65-288">Per informazioni aggiuntive sulla configurazione di [Windows Firewall](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) , vedere [creare una regola di servizio o programma in ingresso](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule).</span><span class="sxs-lookup"><span data-stu-id="25c65-288">For [Windows Firewall](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) configuration guidance, see [Create an Inbound Program or Service Rule](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule).</span></span> <span data-ttu-id="25c65-289">Per ulteriori informazioni, vedere [Windows Defender Firewall con protezione avanzata](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) e articoli correlati nel set di documentazione Windows Firewall.</span><span class="sxs-lookup"><span data-stu-id="25c65-289">For more information, see [Windows Defender Firewall with Advanced Security](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) and related articles in the Windows Firewall documentation set.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="25c65-290">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="25c65-290">Troubleshoot</span></span>

<span data-ttu-id="25c65-291">Se si verificano errori, è possibile che vengano visualizzati i suggerimenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="25c65-291">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="25c65-292">Nella scheda **debugger** aprire gli strumenti di sviluppo nel browser.</span><span class="sxs-lookup"><span data-stu-id="25c65-292">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="25c65-293">Nella console eseguire `localStorage.clear()` per rimuovere tutti i punti di interruzione.</span><span class="sxs-lookup"><span data-stu-id="25c65-293">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="25c65-294">Verificare di aver installato e considerato attendibile il certificato di sviluppo ASP.NET Core HTTPS.</span><span class="sxs-lookup"><span data-stu-id="25c65-294">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="25c65-295">Per altre informazioni, vedere <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="25c65-295">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="25c65-296">Visual Studio richiede l'opzione **Abilita debug JavaScript per ASP.NET (Chrome, Edge e IE)** in **strumenti**  >  **Opzioni**  >  **debug**  >  **generale**.</span><span class="sxs-lookup"><span data-stu-id="25c65-296">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="25c65-297">Questa è l'impostazione predefinita per Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="25c65-297">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="25c65-298">Se il debug non funziona, verificare che sia selezionata l'opzione.</span><span class="sxs-lookup"><span data-stu-id="25c65-298">If debugging isn't working, confirm that the option is selected.</span></span>
* <span data-ttu-id="25c65-299">Se l'ambiente usa un proxy HTTP, assicurarsi che `localhost` sia incluso nelle impostazioni di bypass del proxy.</span><span class="sxs-lookup"><span data-stu-id="25c65-299">If your environment uses an HTTP proxy, make sure that `localhost` is included in the proxy bypass settings.</span></span> <span data-ttu-id="25c65-300">Questa operazione può essere eseguita impostando la `NO_PROXY` variabile di ambiente in uno dei seguenti valori:</span><span class="sxs-lookup"><span data-stu-id="25c65-300">This can be done by setting the `NO_PROXY` environment variable in either:</span></span>
  * <span data-ttu-id="25c65-301">`launchSettings.json`File per il progetto.</span><span class="sxs-lookup"><span data-stu-id="25c65-301">The `launchSettings.json` file for the project.</span></span>
  * <span data-ttu-id="25c65-302">A livello di utente o di variabili di ambiente di sistema per applicarlo a tutte le app.</span><span class="sxs-lookup"><span data-stu-id="25c65-302">At the user or system environment variables level for it to apply to all apps.</span></span> <span data-ttu-id="25c65-303">Quando si usa una variabile di ambiente, riavviare Visual Studio per rendere effettive le modifiche.</span><span class="sxs-lookup"><span data-stu-id="25c65-303">When using an environment variable, restart Visual Studio for the change to take effect.</span></span>
* <span data-ttu-id="25c65-304">Assicurarsi che i firewall o i proxy non blocchino le comunicazioni con il proxy di debug ( `NodeJS` processo).</span><span class="sxs-lookup"><span data-stu-id="25c65-304">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="25c65-305">Per ulteriori informazioni, vedere la sezione relativa alla [configurazione del firewall](#firewall-configuration) .</span><span class="sxs-lookup"><span data-stu-id="25c65-305">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="25c65-306">Punti di interruzione in `OnInitialized{Async}` non riscontri</span><span class="sxs-lookup"><span data-stu-id="25c65-306">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="25c65-307">Il Blazor proxy di debug del Framework richiede un breve intervallo di tempo, quindi i punti di interruzione nel [ `OnInitialized{Async}` metodo del ciclo](xref:blazor/components/lifecycle#component-initialization-methods) di vita potrebbero non essere raggiunti.</span><span class="sxs-lookup"><span data-stu-id="25c65-307">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="25c65-308">Si consiglia di aggiungere un ritardo all'inizio del corpo del metodo per consentire al proxy di debug di avviarsi prima che venga raggiunto il punto di interruzione.</span><span class="sxs-lookup"><span data-stu-id="25c65-308">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="25c65-309">È possibile includere il ritardo in base a una [ `if` direttiva del compilatore](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) per assicurarsi che il ritardo non sia presente per una build di rilascio dell'app.</span><span class="sxs-lookup"><span data-stu-id="25c65-309">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="25c65-310"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="25c65-310"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="25c65-311"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="25c65-311"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a><span data-ttu-id="25c65-312">Timeout di Visual Studio (Windows)</span><span class="sxs-lookup"><span data-stu-id="25c65-312">Visual Studio (Windows) timeout</span></span>

<span data-ttu-id="25c65-313">Se Visual Studio genera un'eccezione che non è stato possibile avviare l'adattatore di debug, indicando che è stato raggiunto il timeout, è possibile modificare il timeout con un'impostazione del registro di sistema:</span><span class="sxs-lookup"><span data-stu-id="25c65-313">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="25c65-314">Il `{TIMEOUT}` segnaposto nel comando precedente è in millisecondi.</span><span class="sxs-lookup"><span data-stu-id="25c65-314">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="25c65-315">Ad esempio, un minuto viene assegnato come `60000` .</span><span class="sxs-lookup"><span data-stu-id="25c65-315">For example, one minute is assigned as `60000`.</span></span>
