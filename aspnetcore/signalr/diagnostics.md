---
title: Registrazione e diagnostica in ASP.NET Core SignalR
author: anurse
description: Informazioni su come raccogliere dati diagnostici dall' SignalR app ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: devx-track-csharp, signalr
ms.date: 06/12/2020
no-loc:
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
uid: signalr/diagnostics
ms.openlocfilehash: 649398a3868117b2e7f3358aa25544c99cc625b3
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631342"
---
# <a name="logging-and-diagnostics-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="2cae8-103">Registrazione e diagnostica in ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="2cae8-103">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="2cae8-104">Di [Andrew Stanton-Nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="2cae8-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="2cae8-105">Questo articolo fornisce indicazioni per la raccolta di dati diagnostici dall' SignalR app ASP.NET Core per facilitare la risoluzione dei problemi.</span><span class="sxs-lookup"><span data-stu-id="2cae8-105">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="2cae8-106">Registrazione lato server</span><span class="sxs-lookup"><span data-stu-id="2cae8-106">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="2cae8-107">I log lato server possono contenere informazioni riservate dall'app.</span><span class="sxs-lookup"><span data-stu-id="2cae8-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="2cae8-108">**Non pubblicare mai log non** elaborati dalle app di produzione nei forum pubblici come github.</span><span class="sxs-lookup"><span data-stu-id="2cae8-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="2cae8-109">Poiché SignalR fa parte di ASP.NET Core, usa il sistema di registrazione ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2cae8-109">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="2cae8-110">Nella configurazione predefinita, SignalR registra informazioni molto piccole, ma ciò può essere configurato.</span><span class="sxs-lookup"><span data-stu-id="2cae8-110">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="2cae8-111">Per informazioni dettagliate sulla configurazione della registrazione ASP.NET Core, vedere la documentazione relativa alla [registrazione di ASP.NET Core](xref:fundamentals/logging/index#configuration) .</span><span class="sxs-lookup"><span data-stu-id="2cae8-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="2cae8-112">SignalR USA due categorie di logger:</span><span class="sxs-lookup"><span data-stu-id="2cae8-112">SignalR uses two logger categories:</span></span>

* <span data-ttu-id="2cae8-113">`Microsoft.AspNetCore.SignalR`: Per i log relativi ai protocolli Hub, attivazione di hub, richiamo di metodi e altre attività correlate all'hub.</span><span class="sxs-lookup"><span data-stu-id="2cae8-113">`Microsoft.AspNetCore.SignalR`: For logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="2cae8-114">`Microsoft.AspNetCore.Http.Connections`: Per i log relativi ai trasporti, ad esempio WebSocket, polling prolungato, eventi inviati dal server e infrastruttura di basso livello SignalR .</span><span class="sxs-lookup"><span data-stu-id="2cae8-114">`Microsoft.AspNetCore.Http.Connections`: For logs related to transports, such as WebSockets, Long Polling, Server-Sent Events, and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="2cae8-115">Per abilitare i log dettagliati da SignalR , configurare entrambi i prefissi precedenti al `Debug` livello del *appsettings.js* nel file aggiungendo gli elementi seguenti alla sottosezione `LogLevel` in `Logging` :</span><span class="sxs-lookup"><span data-stu-id="2cae8-115">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="2cae8-116">È anche possibile configurarlo nel codice nel `CreateWebHostBuilder` Metodo:</span><span class="sxs-lookup"><span data-stu-id="2cae8-116">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="2cae8-117">Se non si usa la configurazione basata su JSON, impostare i valori di configurazione seguenti nel sistema di configurazione:</span><span class="sxs-lookup"><span data-stu-id="2cae8-117">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="2cae8-118">Controllare la documentazione del sistema di configurazione per determinare come specificare i valori di configurazione annidati.</span><span class="sxs-lookup"><span data-stu-id="2cae8-118">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="2cae8-119">Quando si usano le variabili di ambiente, ad esempio, `_` vengono usati due caratteri al posto di `:` (ad esempio, `Logging__LogLevel__Microsoft.AspNetCore.SignalR` ).</span><span class="sxs-lookup"><span data-stu-id="2cae8-119">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="2cae8-120">È consigliabile usare il `Debug` livello quando si raccolgono dati di diagnostica più dettagliati per l'app.</span><span class="sxs-lookup"><span data-stu-id="2cae8-120">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="2cae8-121">Il `Trace` livello produce diagnostica di basso livello e raramente è necessario per diagnosticare i problemi nell'app.</span><span class="sxs-lookup"><span data-stu-id="2cae8-121">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="2cae8-122">Accedere ai log lato server</span><span class="sxs-lookup"><span data-stu-id="2cae8-122">Access server-side logs</span></span>

<span data-ttu-id="2cae8-123">La modalità di accesso ai log lato server dipende dall'ambiente in cui si esegue.</span><span class="sxs-lookup"><span data-stu-id="2cae8-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="2cae8-124">Come app console all'esterno di IIS</span><span class="sxs-lookup"><span data-stu-id="2cae8-124">As a console app outside IIS</span></span>

<span data-ttu-id="2cae8-125">Se è in esecuzione un'app console, il [logger della console](xref:fundamentals/logging/index#console) deve essere abilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="2cae8-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> <span data-ttu-id="2cae8-126">SignalR i log verranno visualizzati nella console di.</span><span class="sxs-lookup"><span data-stu-id="2cae8-126">SignalR logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="2cae8-127">All'interno IIS Express da Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2cae8-127">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="2cae8-128">Visual Studio Visualizza l'output del log nella finestra **output** .</span><span class="sxs-lookup"><span data-stu-id="2cae8-128">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="2cae8-129">Selezionare l'opzione elenco a discesa **server Web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="2cae8-129">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="2cae8-130">Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="2cae8-130">Azure App Service</span></span>

<span data-ttu-id="2cae8-131">Abilitare l'opzione **registrazione applicazioni (file System)** nella sezione **log di diagnostica** del portale del servizio app Azure e configurare il **livello** su `Verbose` .</span><span class="sxs-lookup"><span data-stu-id="2cae8-131">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="2cae8-132">I log devono essere disponibili dal servizio di **streaming dei log** e nei log nel file System del servizio app.</span><span class="sxs-lookup"><span data-stu-id="2cae8-132">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="2cae8-133">Per altre informazioni, vedere [streaming dei log di Azure](xref:fundamentals/logging/index#azure-log-streaming).</span><span class="sxs-lookup"><span data-stu-id="2cae8-133">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="2cae8-134">Altri ambienti</span><span class="sxs-lookup"><span data-stu-id="2cae8-134">Other environments</span></span>

<span data-ttu-id="2cae8-135">Se l'app viene distribuita in un altro ambiente, ad esempio Docker, Kubernetes o il servizio Windows, vedere <xref:fundamentals/logging/index> per altre informazioni su come configurare i provider di registrazione appropriati per l'ambiente.</span><span class="sxs-lookup"><span data-stu-id="2cae8-135">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="2cae8-136">Registrazione client JavaScript</span><span class="sxs-lookup"><span data-stu-id="2cae8-136">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="2cae8-137">I log lato client possono contenere informazioni riservate dall'app.</span><span class="sxs-lookup"><span data-stu-id="2cae8-137">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="2cae8-138">**Non pubblicare mai log non** elaborati dalle app di produzione nei forum pubblici come github.</span><span class="sxs-lookup"><span data-stu-id="2cae8-138">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="2cae8-139">Quando si usa il client JavaScript, è possibile configurare le opzioni di registrazione usando il `configureLogging` metodo in `HubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="2cae8-139">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="2cae8-140">Per disabilitare completamente la registrazione, specificare `signalR.LogLevel.None` nel `configureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="2cae8-140">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="2cae8-141">La tabella seguente illustra i livelli di log disponibili per il client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2cae8-141">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="2cae8-142">Impostando il livello di registrazione su uno di questi valori, viene abilitata la registrazione a tale livello e a tutti i livelli superiori nella tabella.</span><span class="sxs-lookup"><span data-stu-id="2cae8-142">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="2cae8-143">Level</span><span class="sxs-lookup"><span data-stu-id="2cae8-143">Level</span></span> | <span data-ttu-id="2cae8-144">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2cae8-144">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="2cae8-145">Nessun messaggio registrato.</span><span class="sxs-lookup"><span data-stu-id="2cae8-145">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="2cae8-146">Messaggi che indicano un errore nell'intera app.</span><span class="sxs-lookup"><span data-stu-id="2cae8-146">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="2cae8-147">Messaggi che indicano un errore nell'operazione corrente.</span><span class="sxs-lookup"><span data-stu-id="2cae8-147">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="2cae8-148">Messaggi che indicano un problema non irreversibile.</span><span class="sxs-lookup"><span data-stu-id="2cae8-148">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="2cae8-149">Messaggi informativi.</span><span class="sxs-lookup"><span data-stu-id="2cae8-149">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="2cae8-150">Messaggi di diagnostica utili per il debug.</span><span class="sxs-lookup"><span data-stu-id="2cae8-150">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="2cae8-151">Messaggi di diagnostica molto dettagliati progettati per la diagnosi di problemi specifici.</span><span class="sxs-lookup"><span data-stu-id="2cae8-151">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="2cae8-152">Dopo aver configurato il livello di dettaglio, i log verranno scritti nella console del browser o nell'output standard in un'app NodeJS.</span><span class="sxs-lookup"><span data-stu-id="2cae8-152">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="2cae8-153">Se si desidera inviare i log a un sistema di registrazione personalizzato, è possibile fornire un oggetto JavaScript che implementa l' `ILogger` interfaccia.</span><span class="sxs-lookup"><span data-stu-id="2cae8-153">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="2cae8-154">L'unico metodo che deve essere implementato è `log` , che accetta il livello dell'evento e il messaggio associato all'evento.</span><span class="sxs-lookup"><span data-stu-id="2cae8-154">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="2cae8-155">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="2cae8-155">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="2cae8-156">Registrazione client .NET</span><span class="sxs-lookup"><span data-stu-id="2cae8-156">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="2cae8-157">I log lato client possono contenere informazioni riservate dall'app.</span><span class="sxs-lookup"><span data-stu-id="2cae8-157">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="2cae8-158">**Non pubblicare mai log non** elaborati dalle app di produzione nei forum pubblici come github.</span><span class="sxs-lookup"><span data-stu-id="2cae8-158">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="2cae8-159">Per ottenere i log dal client .NET, è possibile usare il `ConfigureLogging` metodo su `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2cae8-159">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="2cae8-160">Funziona allo stesso modo del `ConfigureLogging` metodo in `WebHostBuilder` e `HostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2cae8-160">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="2cae8-161">È possibile configurare gli stessi provider di registrazione usati in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2cae8-161">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="2cae8-162">Tuttavia, è necessario installare e abilitare manualmente i pacchetti NuGet per i singoli provider di registrazione.</span><span class="sxs-lookup"><span data-stu-id="2cae8-162">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

<span data-ttu-id="2cae8-163">Per aggiungere la registrazione del client .NET a un' Blazor WebAssembly app, vedere <xref:blazor/fundamentals/logging#blazor-webassembly-signalr-net-client-logging> .</span><span class="sxs-lookup"><span data-stu-id="2cae8-163">To add .NET client logging to a Blazor WebAssembly app, see <xref:blazor/fundamentals/logging#blazor-webassembly-signalr-net-client-logging>.</span></span>

### <a name="console-logging"></a><span data-ttu-id="2cae8-164">Registrazione console</span><span class="sxs-lookup"><span data-stu-id="2cae8-164">Console logging</span></span>

<span data-ttu-id="2cae8-165">Per abilitare la registrazione della console, aggiungere il pacchetto [Microsoft. Extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) .</span><span class="sxs-lookup"><span data-stu-id="2cae8-165">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="2cae8-166">Usare quindi il `AddConsole` metodo per configurare il logger della console:</span><span class="sxs-lookup"><span data-stu-id="2cae8-166">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="2cae8-167">Debug della registrazione della finestra di output</span><span class="sxs-lookup"><span data-stu-id="2cae8-167">Debug output window logging</span></span>

<span data-ttu-id="2cae8-168">È anche possibile configurare i log per accedere alla finestra di **output** in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2cae8-168">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="2cae8-169">Installare il pacchetto [Microsoft. Extensions. Logging. debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) e usare il `AddDebug` Metodo:</span><span class="sxs-lookup"><span data-stu-id="2cae8-169">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="2cae8-170">Altri provider di registrazione</span><span class="sxs-lookup"><span data-stu-id="2cae8-170">Other logging providers</span></span>

<span data-ttu-id="2cae8-171">SignalR supporta altri provider di registrazione, ad esempio Serilog, seq, NLog o qualsiasi altro sistema di registrazione che si integra con `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="2cae8-171">SignalR supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="2cae8-172">Se il sistema di registrazione fornisce un `ILoggerProvider` , è possibile registrarlo con `AddProvider` :</span><span class="sxs-lookup"><span data-stu-id="2cae8-172">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="2cae8-173">Controllo del livello di dettaglio</span><span class="sxs-lookup"><span data-stu-id="2cae8-173">Control verbosity</span></span>

<span data-ttu-id="2cae8-174">Se si esegue la registrazione da altre posizioni nell'app, è possibile che la modifica del livello predefinito a `Debug` sia troppo dettagliata.</span><span class="sxs-lookup"><span data-stu-id="2cae8-174">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="2cae8-175">È possibile usare un filtro per configurare il livello di registrazione per i SignalR log.</span><span class="sxs-lookup"><span data-stu-id="2cae8-175">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="2cae8-176">Questa operazione può essere eseguita nel codice, nello stesso modo in cui si trova nel server:</span><span class="sxs-lookup"><span data-stu-id="2cae8-176">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="2cae8-177">Tracce di rete</span><span class="sxs-lookup"><span data-stu-id="2cae8-177">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="2cae8-178">Una traccia di rete contiene il contenuto completo di tutti i messaggi inviati dall'app.</span><span class="sxs-lookup"><span data-stu-id="2cae8-178">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="2cae8-179">**Non pubblicare mai** tracce di rete RAW da app di produzione a forum pubblici come github.</span><span class="sxs-lookup"><span data-stu-id="2cae8-179">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="2cae8-180">Se si verifica un problema, una traccia di rete può talvolta fornire numerose informazioni utili.</span><span class="sxs-lookup"><span data-stu-id="2cae8-180">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="2cae8-181">Questa operazione è particolarmente utile se si sta per archiviare un problema nello strumento di rilevamento dei problemi.</span><span class="sxs-lookup"><span data-stu-id="2cae8-181">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="2cae8-182">Raccogliere una traccia di rete con Fiddler (opzione preferita)</span><span class="sxs-lookup"><span data-stu-id="2cae8-182">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="2cae8-183">Questo metodo funziona per tutte le app.</span><span class="sxs-lookup"><span data-stu-id="2cae8-183">This method works for all apps.</span></span>

<span data-ttu-id="2cae8-184">Fiddler è uno strumento molto potente per la raccolta di tracce HTTP.</span><span class="sxs-lookup"><span data-stu-id="2cae8-184">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="2cae8-185">Installarlo da [Telerik.com/Fiddler](https://www.telerik.com/fiddler), avviarlo, quindi eseguire l'app e riprodurre il problema.</span><span class="sxs-lookup"><span data-stu-id="2cae8-185">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="2cae8-186">Fiddler è disponibile per Windows e sono disponibili versioni beta per macOS e Linux.</span><span class="sxs-lookup"><span data-stu-id="2cae8-186">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="2cae8-187">Se si esegue la connessione tramite HTTPS, sono necessari alcuni passaggi aggiuntivi per assicurarsi che Fiddler possa decrittografare il traffico HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2cae8-187">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="2cae8-188">Per ulteriori informazioni, vedere la [documentazione di Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span><span class="sxs-lookup"><span data-stu-id="2cae8-188">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="2cae8-189">Una volta raccolta la traccia, è possibile esportare la traccia scegliendo **file**  >  **Salva**  >  **tutte le sessioni** dalla barra dei menu.</span><span class="sxs-lookup"><span data-stu-id="2cae8-189">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Esportazione di tutte le sessioni da Fiddler](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="2cae8-191">Raccogliere una traccia di rete con tcpdump (solo macOS e Linux)</span><span class="sxs-lookup"><span data-stu-id="2cae8-191">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="2cae8-192">Questo metodo funziona per tutte le app.</span><span class="sxs-lookup"><span data-stu-id="2cae8-192">This method works for all apps.</span></span>

<span data-ttu-id="2cae8-193">È possibile raccogliere tracce TCP non elaborate tramite tcpdump eseguendo il comando seguente da una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="2cae8-193">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="2cae8-194">`root` `sudo` Se viene ricevuto un errore di autorizzazione, potrebbe essere necessario o precedere il comando:</span><span class="sxs-lookup"><span data-stu-id="2cae8-194">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="2cae8-195">Sostituire `[interface]` con l'interfaccia di rete su cui si vuole eseguire l'acquisizione.</span><span class="sxs-lookup"><span data-stu-id="2cae8-195">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="2cae8-196">In genere, questa operazione è simile a `/dev/eth0` (per l'interfaccia Ethernet standard) o `/dev/lo0` (per il traffico localhost).</span><span class="sxs-lookup"><span data-stu-id="2cae8-196">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="2cae8-197">Per ulteriori informazioni, vedere la `tcpdump` pagina relativa all'utente nel sistema host.</span><span class="sxs-lookup"><span data-stu-id="2cae8-197">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="2cae8-198">Raccogliere una traccia di rete nel browser</span><span class="sxs-lookup"><span data-stu-id="2cae8-198">Collect a network trace in the browser</span></span>

<span data-ttu-id="2cae8-199">Questo metodo funziona solo per le app basate su browser.</span><span class="sxs-lookup"><span data-stu-id="2cae8-199">This method only works for browser-based apps.</span></span>

<span data-ttu-id="2cae8-200">La maggior parte del browser Strumenti di sviluppo dispone di una scheda di rete che consente di acquisire l'attività di rete tra il browser e il server.</span><span class="sxs-lookup"><span data-stu-id="2cae8-200">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="2cae8-201">Tuttavia, queste tracce non includono messaggi di evento WebSocket e inviati dal server.</span><span class="sxs-lookup"><span data-stu-id="2cae8-201">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="2cae8-202">Se si usano questi trasporti, un approccio migliore è quello di usare uno strumento come Fiddler o TcpDump (descritto di seguito).</span><span class="sxs-lookup"><span data-stu-id="2cae8-202">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="2cae8-203">Microsoft Edge e Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="2cae8-203">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="2cae8-204">(Le istruzioni sono le stesse per Microsoft Edge e Internet Explorer)</span><span class="sxs-lookup"><span data-stu-id="2cae8-204">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="2cae8-205">Premere F12 per aprire gli strumenti di sviluppo</span><span class="sxs-lookup"><span data-stu-id="2cae8-205">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="2cae8-206">Fare clic sulla scheda rete</span><span class="sxs-lookup"><span data-stu-id="2cae8-206">Click the Network Tab</span></span>
3. <span data-ttu-id="2cae8-207">Aggiornare la pagina (se necessario) e riprodurre il problema</span><span class="sxs-lookup"><span data-stu-id="2cae8-207">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="2cae8-208">Fare clic sull'icona Salva sulla barra degli strumenti per esportare la traccia come file "HAR":</span><span class="sxs-lookup"><span data-stu-id="2cae8-208">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![Icona Salva nella scheda rete Microsoft Edge Dev Tools](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="2cae8-210">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="2cae8-210">Google Chrome</span></span>

1. <span data-ttu-id="2cae8-211">Premere F12 per aprire gli strumenti di sviluppo</span><span class="sxs-lookup"><span data-stu-id="2cae8-211">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="2cae8-212">Fare clic sulla scheda rete</span><span class="sxs-lookup"><span data-stu-id="2cae8-212">Click the Network Tab</span></span>
3. <span data-ttu-id="2cae8-213">Aggiornare la pagina (se necessario) e riprodurre il problema</span><span class="sxs-lookup"><span data-stu-id="2cae8-213">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="2cae8-214">Fare clic con il pulsante destro del mouse in un punto qualsiasi dell'elenco di richieste e scegliere "Salva come HAR con contenuto":</span><span class="sxs-lookup"><span data-stu-id="2cae8-214">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Opzione "Salva come HAR con contenuto" nella scheda rete di strumenti di sviluppo di Google Chrome](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="2cae8-216">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="2cae8-216">Mozilla Firefox</span></span>

1. <span data-ttu-id="2cae8-217">Premere F12 per aprire gli strumenti di sviluppo</span><span class="sxs-lookup"><span data-stu-id="2cae8-217">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="2cae8-218">Fare clic sulla scheda rete</span><span class="sxs-lookup"><span data-stu-id="2cae8-218">Click the Network Tab</span></span>
3. <span data-ttu-id="2cae8-219">Aggiornare la pagina (se necessario) e riprodurre il problema</span><span class="sxs-lookup"><span data-stu-id="2cae8-219">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="2cae8-220">Fare clic con il pulsante destro del mouse in un punto qualsiasi dell'elenco di richieste e scegliere "Salva tutto come HAR"</span><span class="sxs-lookup"><span data-stu-id="2cae8-220">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Opzione "Salva tutto come HAR" nella scheda rete di Mozilla Firefox Dev Tools](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="2cae8-222">Alleghi i file di diagnostica ai problemi di GitHub</span><span class="sxs-lookup"><span data-stu-id="2cae8-222">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="2cae8-223">È possibile aggiungere i file di diagnostica ai problemi di GitHub rinominando i file in modo che abbiano un' `.txt` estensione e quindi trascinandoli e rilasciandoli al problema.</span><span class="sxs-lookup"><span data-stu-id="2cae8-223">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="2cae8-224">Non incollare il contenuto dei file di log o delle tracce di rete in un problema di GitHub.</span><span class="sxs-lookup"><span data-stu-id="2cae8-224">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="2cae8-225">Questi log e tracce possono avere dimensioni molto elevate e GitHub li tronca in genere.</span><span class="sxs-lookup"><span data-stu-id="2cae8-225">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![Trascinamento dei file di log in un problema di GitHub](diagnostics/attaching-diagnostics-files.png)

## <a name="metrics"></a><span data-ttu-id="2cae8-227">Metriche</span><span class="sxs-lookup"><span data-stu-id="2cae8-227">Metrics</span></span>

<span data-ttu-id="2cae8-228">Metrica è una rappresentazione delle misure dei dati in intervalli di tempo.</span><span class="sxs-lookup"><span data-stu-id="2cae8-228">Metrics is a representation of data measures over intervals of time.</span></span> <span data-ttu-id="2cae8-229">Ad esempio, richieste al secondo.</span><span class="sxs-lookup"><span data-stu-id="2cae8-229">For example, requests per second.</span></span> <span data-ttu-id="2cae8-230">I dati di metrica consentono di osservare lo stato di un'app a un livello elevato.</span><span class="sxs-lookup"><span data-stu-id="2cae8-230">Metrics data allows observation of the state of an app at a high level.</span></span> <span data-ttu-id="2cae8-231">Le metriche gRPC di .NET vengono emesse usando <xref:System.Diagnostics.Tracing.EventCounter> .</span><span class="sxs-lookup"><span data-stu-id="2cae8-231">.NET gRPC metrics are emitted using <xref:System.Diagnostics.Tracing.EventCounter>.</span></span>

### <a name="no-locsignalr-server-metrics"></a><span data-ttu-id="2cae8-232">SignalR metriche del server</span><span class="sxs-lookup"><span data-stu-id="2cae8-232">SignalR server metrics</span></span>

<span data-ttu-id="2cae8-233">SignalR le metriche del server sono segnalate nell' <xref:Microsoft.AspNetCore.Http.Connections> origine evento.</span><span class="sxs-lookup"><span data-stu-id="2cae8-233">SignalR server metrics are reported on the <xref:Microsoft.AspNetCore.Http.Connections> event source.</span></span>

| <span data-ttu-id="2cae8-234">Nome</span><span class="sxs-lookup"><span data-stu-id="2cae8-234">Name</span></span>                    | <span data-ttu-id="2cae8-235">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2cae8-235">Description</span></span>                 |
|-------------------------|-----------------------------|
| `connections-started`   | <span data-ttu-id="2cae8-236">Totale connessioni avviate</span><span class="sxs-lookup"><span data-stu-id="2cae8-236">Total connections started</span></span>   |
| `connections-stopped`   | <span data-ttu-id="2cae8-237">Totale connessioni interrotte</span><span class="sxs-lookup"><span data-stu-id="2cae8-237">Total connections stopped</span></span>   |
| `connections-timed-out` | <span data-ttu-id="2cae8-238">Timeout connessioni totali</span><span class="sxs-lookup"><span data-stu-id="2cae8-238">Total connections timed out</span></span> |
| `current-connections`   | <span data-ttu-id="2cae8-239">connessioni correnti</span><span class="sxs-lookup"><span data-stu-id="2cae8-239">Current connections</span></span>         |
| `connections-duration`  | <span data-ttu-id="2cae8-240">Durata media connessione</span><span class="sxs-lookup"><span data-stu-id="2cae8-240">Average connection duration</span></span> |

### <a name="observe-metrics"></a><span data-ttu-id="2cae8-241">Osservare le metriche</span><span class="sxs-lookup"><span data-stu-id="2cae8-241">Observe metrics</span></span>

<span data-ttu-id="2cae8-242">[DotNet-Counters](/dotnet/core/diagnostics/dotnet-counters) è uno strumento di monitoraggio delle prestazioni per il monitoraggio dell'integrità ad hoc e l'analisi delle prestazioni di primo livello.</span><span class="sxs-lookup"><span data-stu-id="2cae8-242">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="2cae8-243">Monitorare un'app .NET con `Microsoft.AspNetCore.Http.Connections` come nome del provider.</span><span class="sxs-lookup"><span data-stu-id="2cae8-243">Monitor a .NET app with `Microsoft.AspNetCore.Http.Connections` as the provider name.</span></span> <span data-ttu-id="2cae8-244">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="2cae8-244">For example:</span></span>

```console
> dotnet-counters monitor --process-id 37016 Microsoft.AspNetCore.Http.Connections

Press p to pause, r to resume, q to quit.
    Status: Running
[Microsoft.AspNetCore.Http.Connections]
    Average Connection Duration (ms)       16,040.56
    Current Connections                         1
    Total Connections Started                   8
    Total Connections Stopped                   7
    Total Connections Timed Out                 0
```

## <a name="additional-resources"></a><span data-ttu-id="2cae8-245">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2cae8-245">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
