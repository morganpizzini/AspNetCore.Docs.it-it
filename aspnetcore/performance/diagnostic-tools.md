---
title: Strumenti di diagnostica delle prestazioni
author: mjrousos
description: Strumenti utili per la diagnosi dei problemi di prestazioni nelle app ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
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
uid: performance/diagnostic-tools
ms.openlocfilehash: 5f3daaf132b903898e756160a459d4df5f421c11
ms.sourcegitcommit: dd0e87abf2bb50ee992d9185bb256ed79d48f545
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88746546"
---
# <a name="performance-diagnostic-tools"></a><span data-ttu-id="3ba58-103">Strumenti di diagnostica prestazioni</span><span class="sxs-lookup"><span data-stu-id="3ba58-103">Performance Diagnostic Tools</span></span>

<span data-ttu-id="3ba58-104">Di [Mike risveglia](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="3ba58-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="3ba58-105">In questo articolo vengono elencati gli strumenti per la diagnosi dei problemi di prestazioni in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3ba58-105">This article lists tools for diagnosing performance issues in ASP.NET Core.</span></span>

## <a name="visual-studio-diagnostic-tools"></a><span data-ttu-id="3ba58-106">Strumenti di diagnostica di Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ba58-106">Visual Studio Diagnostic Tools</span></span>

<span data-ttu-id="3ba58-107">Gli [strumenti di profilatura e diagnostica](/visualstudio/profiling) incorporati in Visual Studio sono un valido punto di partenza per analizzare i problemi relativi alle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="3ba58-107">The [profiling and diagnostic tools](/visualstudio/profiling) built into Visual Studio are a good place to start investigating performance issues.</span></span> <span data-ttu-id="3ba58-108">Questi strumenti sono potenti e pratici da usare nell'ambiente di sviluppo di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3ba58-108">These tools are powerful and convenient to use from the Visual Studio development environment.</span></span> <span data-ttu-id="3ba58-109">Gli strumenti consentono di analizzare l'utilizzo della CPU, l'utilizzo della memoria e gli eventi di prestazioni nelle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3ba58-109">The tooling allows analysis of CPU usage, memory usage, and performance events in ASP.NET Core apps.</span></span> <span data-ttu-id="3ba58-110">L'incorporamento semplifica la profilatura in fase di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="3ba58-110">Being built-in makes profiling easy at development time.</span></span>

<span data-ttu-id="3ba58-111">Ulteriori informazioni sono disponibili nella [documentazione di Visual Studio](/visualstudio/profiling/profiling-overview).</span><span class="sxs-lookup"><span data-stu-id="3ba58-111">More information is available in [Visual Studio documentation](/visualstudio/profiling/profiling-overview).</span></span>

## <a name="application-insights"></a><span data-ttu-id="3ba58-112">Application Insights</span><span class="sxs-lookup"><span data-stu-id="3ba58-112">Application Insights</span></span>

<span data-ttu-id="3ba58-113">[Application Insights](/azure/application-insights/app-insights-overview) fornisce dati sulle prestazioni approfonditi per l'app.</span><span class="sxs-lookup"><span data-stu-id="3ba58-113">[Application Insights](/azure/application-insights/app-insights-overview) provides in-depth performance data for your app.</span></span> <span data-ttu-id="3ba58-114">Application Insights raccoglie automaticamente i dati sulle frequenze di risposta, sulle percentuali di errore, sui tempi di risposta delle dipendenze e altro ancora.</span><span class="sxs-lookup"><span data-stu-id="3ba58-114">Application Insights automatically collects data on response rates, failure rates, dependency response times, and more.</span></span> <span data-ttu-id="3ba58-115">Application Insights supporta la registrazione di metriche e eventi personalizzati specifici per l'app.</span><span class="sxs-lookup"><span data-stu-id="3ba58-115">Application Insights supports logging custom events and metrics specific to your app.</span></span>

<span data-ttu-id="3ba58-116">Applicazione Azure Insights offre diversi modi per ottenere informazioni dettagliate sulle app monitorate:</span><span class="sxs-lookup"><span data-stu-id="3ba58-116">Azure Application Insights provides multiple ways to give insights on monitored apps:</span></span>

- <span data-ttu-id="3ba58-117">[Mappa delle applicazioni](/azure/application-insights/app-insights-app-map) : consente di individuare i colli di bottiglia delle prestazioni o le aree sensibili agli errori in tutti i componenti delle app distribuite.</span><span class="sxs-lookup"><span data-stu-id="3ba58-117">[Application Map](/azure/application-insights/app-insights-app-map) – helps spot performance bottlenecks or failure hot-spots across all components of distributed apps.</span></span>
- <span data-ttu-id="3ba58-118">[Azure Esplora metriche](/azure/azure-monitor/platform/metrics-getting-started) è un componente del portale di Microsoft Azure che consente di tracciare grafici, correlare visivamente le tendenze e analizzare picchi e flessioni nei valori delle metriche.</span><span class="sxs-lookup"><span data-stu-id="3ba58-118">[Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) is a component of the Microsoft Azure portal that allows plotting charts, visually correlating trends, and investigating spikes and dips in metrics' values.</span></span>
- <span data-ttu-id="3ba58-119">Pannello [prestazioni nel portale Application Insights](/azure/application-insights/app-insights-tutorial-performance):</span><span class="sxs-lookup"><span data-stu-id="3ba58-119">[Performance blade in Application Insights portal](/azure/application-insights/app-insights-tutorial-performance):</span></span>

  - <span data-ttu-id="3ba58-120">Mostra i dettagli sulle prestazioni per diverse operazioni nell'app monitorata.</span><span class="sxs-lookup"><span data-stu-id="3ba58-120">Shows performance details for different operations in the monitored app.</span></span>
  - <span data-ttu-id="3ba58-121">Consente di eseguire il drill-through in un'unica operazione per controllare tutte le parti/dipendenze che contribuiscono a una durata prolungata.</span><span class="sxs-lookup"><span data-stu-id="3ba58-121">Allows drilling into a single operation to check all parts/dependencies that contribute to a long duration.</span></span>
  - <span data-ttu-id="3ba58-122">Il profiler può essere richiamato da qui per raccogliere le tracce delle prestazioni su richiesta.</span><span class="sxs-lookup"><span data-stu-id="3ba58-122">Profiler can be invoked from here to collect performance traces on-demand.</span></span>

- <span data-ttu-id="3ba58-123">[Applicazione Azure Insights Profiler](/azure/azure-monitor/app/profiler) consente la profilatura normale e su richiesta delle app .NET.</span><span class="sxs-lookup"><span data-stu-id="3ba58-123">[Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) allows regular and on-demand profiling of .NET apps.</span></span>  <span data-ttu-id="3ba58-124">Portale di Azure Mostra le tracce delle prestazioni acquisite con stack di chiamate e percorsi sensibili.</span><span class="sxs-lookup"><span data-stu-id="3ba58-124">Azure portal shows captured performance traces with call stacks and hot paths.</span></span> <span data-ttu-id="3ba58-125">È anche possibile scaricare i file di traccia per un'analisi più approfondita usando PerfView.</span><span class="sxs-lookup"><span data-stu-id="3ba58-125">The trace files can also be downloaded for deeper analysis using PerfView.</span></span>

<span data-ttu-id="3ba58-126">Application Insights può essere usato in ambienti diversi:</span><span class="sxs-lookup"><span data-stu-id="3ba58-126">Application Insights can be used in a variety environments:</span></span>

- <span data-ttu-id="3ba58-127">Ottimizzato per il funzionamento in Azure.</span><span class="sxs-lookup"><span data-stu-id="3ba58-127">Optimized to work in Azure.</span></span>
- <span data-ttu-id="3ba58-128">Funziona in produzione, sviluppo e gestione temporanea.</span><span class="sxs-lookup"><span data-stu-id="3ba58-128">Works in production, development, and staging.</span></span>
- <span data-ttu-id="3ba58-129">Funziona localmente da [Visual Studio](/azure/application-insights/app-insights-visual-studio) o in altri ambienti host.</span><span class="sxs-lookup"><span data-stu-id="3ba58-129">Works locally from [Visual Studio](/azure/application-insights/app-insights-visual-studio) or in other hosting environments.</span></span>

<span data-ttu-id="3ba58-130">Per altre informazioni, vedere [Application Insights per ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="3ba58-130">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="perfview"></a><span data-ttu-id="3ba58-131">PerfView</span><span class="sxs-lookup"><span data-stu-id="3ba58-131">PerfView</span></span>

<span data-ttu-id="3ba58-132">[PerfView](https://github.com/Microsoft/perfview) è uno strumento di analisi delle prestazioni creato dal team .NET specifico per la diagnosi dei problemi di prestazioni .NET.</span><span class="sxs-lookup"><span data-stu-id="3ba58-132">[PerfView](https://github.com/Microsoft/perfview) is a performance analysis tool created by the .NET team specifically for diagnosing .NET performance issues.</span></span> <span data-ttu-id="3ba58-133">PerfView consente di analizzare l'utilizzo della CPU, il comportamento della memoria e del Garbage Collector, gli eventi delle prestazioni e il tempo di clock.</span><span class="sxs-lookup"><span data-stu-id="3ba58-133">PerfView allows analysis of CPU usage, memory and GC behavior, performance events, and wall clock time.</span></span>

<span data-ttu-id="3ba58-134">Altre informazioni su PerfView e su come iniziare a usare le [esercitazioni video di PerfView](https://channel9.msdn.com/Series/PerfView-Tutorial) o leggere la guida dell'utente disponibile nello strumento o [in GitHub](https://github.com/Microsoft/perfview).</span><span class="sxs-lookup"><span data-stu-id="3ba58-134">You can learn more about PerfView and how to get started with [PerfView video tutorials](https://channel9.msdn.com/Series/PerfView-Tutorial) or by reading the user's guide available in the tool or [on GitHub](https://github.com/Microsoft/perfview).</span></span>

## <a name="windows-performance-toolkit"></a><span data-ttu-id="3ba58-135">Windows Performance Toolkit</span><span class="sxs-lookup"><span data-stu-id="3ba58-135">Windows Performance Toolkit</span></span>

<span data-ttu-id="3ba58-136">[Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) è costituito da due componenti: Windows Performance Recorder (WPR) e Windows Performance Analyzer (WPA).</span><span class="sxs-lookup"><span data-stu-id="3ba58-136">[Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) consists of two components: Windows Performance Recorder (WPR) and Windows Performance Analyzer (WPA).</span></span> <span data-ttu-id="3ba58-137">Gli strumenti producono profili di prestazioni approfonditi dei sistemi operativi Windows e delle app.</span><span class="sxs-lookup"><span data-stu-id="3ba58-137">The tools produce in-depth performance profiles of Windows operating systems and apps.</span></span> <span data-ttu-id="3ba58-138">WPT offre modi più avanzati per visualizzare i dati, ma la raccolta dei dati è meno potente rispetto a PerfView.</span><span class="sxs-lookup"><span data-stu-id="3ba58-138">WPT has richer ways of visualizing data, but its data collecting is less powerful than PerfView's.</span></span>

## <a name="perfcollect"></a><span data-ttu-id="3ba58-139">PerfCollect</span><span class="sxs-lookup"><span data-stu-id="3ba58-139">PerfCollect</span></span>

<span data-ttu-id="3ba58-140">Anche se PerfView è uno strumento di analisi delle prestazioni utile per gli scenari .NET, viene eseguito solo in Windows e non può essere usato per raccogliere tracce da app ASP.NET Core in esecuzione in ambienti Linux.</span><span class="sxs-lookup"><span data-stu-id="3ba58-140">While PerfView is a useful performance analysis tool for .NET scenarios, it only runs on Windows so you can't use it to collect traces from ASP.NET Core apps running in Linux environments.</span></span>

<span data-ttu-id="3ba58-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) è uno script bash che usa gli strumenti di profilatura Linux nativi ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) e [LTTng](https://lttng.org/)) per raccogliere tracce in Linux che possono essere analizzate da PerfView.</span><span class="sxs-lookup"><span data-stu-id="3ba58-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) is a bash script that uses native Linux profiling tools ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) and [LTTng](https://lttng.org/)) to collect traces on Linux that can be analyzed by PerfView.</span></span> <span data-ttu-id="3ba58-142">PerfCollect è utile quando i problemi di prestazioni vengono visualizzati in ambienti Linux in cui non è possibile usare direttamente PerfView.</span><span class="sxs-lookup"><span data-stu-id="3ba58-142">PerfCollect is useful when performance problems show up in Linux environments where PerfView can't be used directly.</span></span> <span data-ttu-id="3ba58-143">PerfCollect può invece raccogliere tracce da app .NET Core che vengono quindi analizzate in un computer Windows usando PerfView.</span><span class="sxs-lookup"><span data-stu-id="3ba58-143">Instead, PerfCollect can collect traces from .NET Core apps that are then analyzed on a Windows computer using PerfView.</span></span>

<span data-ttu-id="3ba58-144">Altre informazioni su come installare e iniziare a usare PerfCollect sono disponibili [su GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span><span class="sxs-lookup"><span data-stu-id="3ba58-144">More information about how to install and get started with PerfCollect is available [on GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span></span>

## <a name="other-third-party-performance-tools"></a><span data-ttu-id="3ba58-145">Altri strumenti per le prestazioni di terze parti</span><span class="sxs-lookup"><span data-stu-id="3ba58-145">Other Third-party Performance Tools</span></span>

<span data-ttu-id="3ba58-146">Di seguito sono elencati alcuni strumenti per le prestazioni di terze parti utili per l'analisi delle prestazioni delle applicazioni .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3ba58-146">The following lists some third-party performance tools that are useful in performance investigation of .NET Core applications.</span></span>

- [<span data-ttu-id="3ba58-147">MiniProfiler</span><span class="sxs-lookup"><span data-stu-id="3ba58-147">MiniProfiler</span></span>](https://miniprofiler.com/)
- <span data-ttu-id="3ba58-148">[dotTrace](https://www.jetbrains.com/profiler/) e [dotMemory](https://www.jetbrains.com/dotmemory/) da [JetBrains](https://www.jetbrains.com/)</span><span class="sxs-lookup"><span data-stu-id="3ba58-148">[dotTrace](https://www.jetbrains.com/profiler/) and [dotMemory](https://www.jetbrains.com/dotmemory/) from [JetBrains](https://www.jetbrains.com/)</span></span>
- <span data-ttu-id="3ba58-149">[VTune](https://software.intel.com/content/www/us/en/develop/tools/vtune-profiler.html) da Intel</span><span class="sxs-lookup"><span data-stu-id="3ba58-149">[VTune](https://software.intel.com/content/www/us/en/develop/tools/vtune-profiler.html) from Intel</span></span>
