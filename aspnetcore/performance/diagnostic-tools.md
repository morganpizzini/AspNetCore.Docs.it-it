---
title: Strumenti di diagnostica delle prestazioni
author: mjrousos
description: Strumenti utili per la diagnosi dei problemi di prestazioni nelle app ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/diagnostic-tools
ms.openlocfilehash: 631dedddecfe3b443e38c6f5a1a182f98c79f0ad
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399595"
---
# <a name="performance-diagnostic-tools"></a>Strumenti di diagnostica prestazioni

Di [Mike risveglia](https://github.com/mjrousos)

In questo articolo vengono elencati gli strumenti per la diagnosi dei problemi di prestazioni in ASP.NET Core.

## <a name="visual-studio-diagnostic-tools"></a>Strumenti di diagnostica di Visual Studio

Gli [strumenti di profilatura e diagnostica](/visualstudio/profiling) incorporati in Visual Studio sono un valido punto di partenza per analizzare i problemi relativi alle prestazioni. Questi strumenti sono potenti e pratici da usare nell'ambiente di sviluppo di Visual Studio. Gli strumenti consentono di analizzare l'utilizzo della CPU, l'utilizzo della memoria e gli eventi di prestazioni nelle app ASP.NET Core. L'incorporamento semplifica la profilatura in fase di sviluppo.

Ulteriori informazioni sono disponibili nella [documentazione di Visual Studio](/visualstudio/profiling/profiling-overview).

## <a name="application-insights"></a>Application Insights

[Application Insights](/azure/application-insights/app-insights-overview) fornisce dati sulle prestazioni approfonditi per l'app. Application Insights raccoglie automaticamente i dati sulle frequenze di risposta, sulle percentuali di errore, sui tempi di risposta delle dipendenze e altro ancora. Application Insights supporta la registrazione di metriche e eventi personalizzati specifici per l'app.

Applicazione Azure Insights offre diversi modi per ottenere informazioni dettagliate sulle app monitorate:

- [Mappa delle applicazioni](/azure/application-insights/app-insights-app-map) : consente di individuare i colli di bottiglia delle prestazioni o le aree sensibili agli errori in tutti i componenti delle app distribuite.
- [Azure Esplora metriche](/azure/azure-monitor/platform/metrics-getting-started) è un componente del portale di Microsoft Azure che consente di tracciare grafici, correlare visivamente le tendenze e analizzare picchi e flessioni nei valori delle metriche.
- Pannello [prestazioni nel portale Application Insights](/azure/application-insights/app-insights-tutorial-performance):

  - Mostra i dettagli sulle prestazioni per diverse operazioni nell'app monitorata.
  - Consente di eseguire il drill-through in un'unica operazione per controllare tutte le parti/dipendenze che contribuiscono a una durata prolungata.
  - Il profiler può essere richiamato da qui per raccogliere le tracce delle prestazioni su richiesta.

- [Applicazione Azure Insights Profiler](/azure/azure-monitor/app/profiler) consente la profilatura normale e su richiesta delle app .NET.  Portale di Azure Mostra le tracce delle prestazioni acquisite con stack di chiamate e percorsi sensibili. È anche possibile scaricare i file di traccia per un'analisi più approfondita usando PerfView.

Application Insights può essere usato in ambienti diversi:

- Ottimizzato per il funzionamento in Azure.
- Funziona in produzione, sviluppo e gestione temporanea.
- Funziona localmente da [Visual Studio](/azure/application-insights/app-insights-visual-studio) o in altri ambienti host.

Per altre informazioni, vedere [Application Insights per ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).

## <a name="perfview"></a>PerfView

[PerfView](https://github.com/Microsoft/perfview) è uno strumento di analisi delle prestazioni creato dal team .NET specifico per la diagnosi dei problemi di prestazioni .NET. PerfView consente di analizzare l'utilizzo della CPU, il comportamento della memoria e del Garbage Collector, gli eventi delle prestazioni e il tempo di clock.

Altre informazioni su PerfView e su come iniziare a usare le [esercitazioni video di PerfView](https://channel9.msdn.com/Series/PerfView-Tutorial) o leggere la guida dell'utente disponibile nello strumento o [in GitHub](https://github.com/Microsoft/perfview).

## <a name="windows-performance-toolkit"></a>Windows Performance Toolkit

[Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) è costituito da due componenti: Windows Performance Recorder (WPR) e Windows Performance Analyzer (WPA). Gli strumenti producono profili di prestazioni approfonditi dei sistemi operativi Windows e delle app. WPT offre modi più avanzati per visualizzare i dati, ma la raccolta dei dati è meno potente rispetto a PerfView.

## <a name="perfcollect"></a>PerfCollect

Anche se PerfView è uno strumento di analisi delle prestazioni utile per gli scenari .NET, viene eseguito solo in Windows e non può essere usato per raccogliere tracce da app ASP.NET Core in esecuzione in ambienti Linux.

[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) è uno script bash che usa gli strumenti di profilatura Linux nativi ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) e [LTTng](https://lttng.org/)) per raccogliere tracce in Linux che possono essere analizzate da PerfView. PerfCollect è utile quando i problemi di prestazioni vengono visualizzati in ambienti Linux in cui non è possibile usare direttamente PerfView. PerfCollect può invece raccogliere tracce da app .NET Core che vengono quindi analizzate in un computer Windows usando PerfView.

Altre informazioni su come installare e iniziare a usare PerfCollect sono disponibili [su GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).

## <a name="other-third-party-performance-tools"></a>Altri strumenti per le prestazioni di terze parti

Di seguito sono elencati alcuni strumenti per le prestazioni di terze parti utili per l'analisi delle prestazioni delle applicazioni .NET Core.

- [MiniProfiler](https://miniprofiler.com/)
- dotTrace e dotMemory da JetBrains
- VTune da Intel
