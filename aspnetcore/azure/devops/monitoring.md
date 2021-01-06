---
title: Monitorare ed eseguire il debug di DevOps con ASP.NET Core e Azure
author: CamSoper
description: Monitoraggio e debug del codice come parte di una soluzione DevOps con ASP.NET Core e Azure
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 07/10/2019
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
uid: azure/devops/monitor
ms.openlocfilehash: 74e789828bf5d54e3457f235657f8ed7086df80d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "93056751"
---
# <a name="monitor-and-debug"></a>Monitorare ed eseguire il debug

Dopo aver distribuito l'app e creato una pipeline DevOps, è importante comprendere come monitorare e risolvere i problemi dell'app.

In questa sezione verranno completate le attività seguenti:

* Trovare i dati di monitoraggio e risoluzione dei problemi di base nel portale di Azure
* Informazioni su come monitoraggio di Azure fornisce un'occhiata più approfondita alle metriche in tutti i servizi di Azure
* Connettere l'app Web con Application Insights per la profilatura dell'app
* Attivare la registrazione e scoprire dove scaricare i log
* Eseguire lo streaming dei log in tempo reale
* Informazioni su dove configurare gli avvisi
* Informazioni sul debug remoto app Azure le app Web del servizio.

## <a name="basic-monitoring-and-troubleshooting"></a>Monitoraggio e risoluzione dei problemi di base

Le app Web del servizio app sono facilmente monitorate in tempo reale. Il portale di Azure esegue il rendering delle metriche in grafici e grafici di facile comprensione.

1. Aprire il [portale di Azure](https://portal.azure.com), quindi passare al servizio app *MyWebApp \<unique_number\>* .

1. Nella scheda **Panoramica** sono visualizzate informazioni utili, inclusi i grafici che visualizzano le metriche recenti.

    ![Screenshot che mostra il pannello Panoramica](./media/monitoring/overview.png)

    * **Http 5xx**: numero di errori sul lato server, in genere eccezioni nel codice ASP.NET Core.
    * **Dati in**: dati in ingresso nell'app Web.
    * **Dati in uscita**: dati in uscita dall'app Web ai client.
    * **Richieste**: numero di richieste HTTP.
    * **Tempo medio di risposta**: tempo medio per l'app Web per rispondere alle richieste HTTP.

    In questa pagina sono disponibili anche diversi strumenti self-service per la risoluzione dei problemi e l'ottimizzazione.

    ![Screenshot che Mostra gli strumenti self-service](./media/monitoring/wizards.png)

    * **Diagnosticare e risolvere i problemi** è uno strumento di risoluzione dei problemi self-service.
    * **Application Insights** è per la profilatura delle prestazioni e il comportamento dell'app e viene illustrato più avanti in questa sezione.
    * **App Service Advisor** apporta consigli per ottimizzare l'esperienza dell'app.

## <a name="advanced-monitoring"></a>Monitoraggio avanzato

[Monitoraggio di Azure](/azure/monitoring-and-diagnostics/) è il servizio centralizzato per il monitoraggio di tutte le metriche e l'impostazione degli avvisi nei servizi di Azure. In monitoraggio di Azure, gli amministratori possono tenere traccia in dettaglio delle prestazioni e identificare le tendenze. Ogni servizio di Azure offre un proprio [set di metriche](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) in monitoraggio di Azure.

## <a name="profile-with-application-insights"></a>Profilo con Application Insights

[Application Insights](/azure/application-insights/app-insights-overview) è un servizio di Azure per analizzare le prestazioni e la stabilità delle app Web e il modo in cui gli utenti le usano. I dati di Application Insights sono più ampi e più profondi rispetto a quelli di monitoraggio di Azure. I dati possono fornire a sviluppatori e amministratori informazioni chiave per migliorare le app. Application Insights possono essere aggiunti a una risorsa del servizio app Azure senza modifiche al codice.

1. Aprire il [portale di Azure](https://portal.azure.com), quindi passare al servizio app *MyWebApp \<unique_number\>* .
1. Nella scheda **Panoramica** fare clic sul riquadro **Application Insights** .

    ![Riquadro Application Insights](./media/monitoring/app-insights.png)

1. Selezionare il pulsante di opzione **Crea nuova risorsa** . Usare il nome predefinito della risorsa e selezionare il percorso per la risorsa Application Insights. Non è necessario che la località corrisponda a quella dell'app Web.

    ![Installazione di Application Insights](./media/monitoring/new-app-insights.png)

1. Per **Runtime/Framework**, selezionare **ASP.NET Core**. Accettare le impostazioni predefinite.
1. Selezionare **OK**. Se viene richiesto di confermare, selezionare **continua**.
1. Dopo aver creato la risorsa, fare clic sul nome della risorsa Application Insights per passare direttamente alla pagina Application Insights.

    ![La nuova risorsa Application Insights è pronta](./media/monitoring/new-app-insights-done.png)

Quando si usa l'app, i dati si accumulano. Selezionare **Aggiorna** per ricaricare il pannello con nuovi dati.

![Scheda Panoramica Application Insights](./media/monitoring/app-insights-overview.png)

Application Insights fornisce informazioni utili sul lato server senza alcuna configurazione aggiuntiva. Per ottenere il massimo valore da Application Insights, [instrumentare l'app con l'SDK di Application Insights](/azure/application-insights/app-insights-asp-net-core). Se configurato correttamente, il servizio fornisce il monitoraggio end-to-end nel server Web e nel browser, incluse le prestazioni lato client. Per ulteriori informazioni, vedere la [documentazione Application Insights](/azure/application-insights/app-insights-overview).

## <a name="logging"></a>Registrazione

Il server Web e i log delle app sono disabilitati per impostazione predefinita nel servizio app Azure. Abilitare i log con i passaggi seguenti:

1. Aprire il [portale di Azure](https://portal.azure.com)e passare al servizio app *MyWebApp \<unique_number\>* .
1. Nel menu a sinistra scorrere verso il basso fino alla sezione **monitoraggio** . Selezionare **log di diagnostica**.

    ![Collegamento log di diagnostica](./media/monitoring/logging.png)

1. Attivare la **registrazione delle applicazioni (filesystem)**. Se richiesto, fare clic sulla casella per installare le estensioni per abilitare la registrazione delle app nell'app Web.
1. Impostare la **registrazione del server Web** sul **file System**.
1. Immettere il **periodo di conservazione** in giorni. Ad esempio, 30.
1. Fare clic su **Salva**.

ASP.NET Core e i log del server Web (servizio app) vengono generati per l'app Web. Possono essere scaricati usando le informazioni FTP/FTPS visualizzate. La password è identica a quella delle credenziali di distribuzione create in precedenza in questa guida. I log possono essere [trasmessi direttamente al computer locale con PowerShell o l'interfaccia della riga di comando di Azure](/azure/app-service/web-sites-enable-diagnostic-log#download). I log possono essere [visualizzati anche in Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).

## <a name="log-streaming"></a>Streaming dei log

I log del server Web e dell'app possono essere trasmessi in tempo reale tramite il portale.

1. Aprire il [portale di Azure](https://portal.azure.com)e passare al servizio app *MyWebApp \<unique_number\>* .
1. Nel menu a sinistra scorrere verso il basso fino alla sezione **monitoraggio** e selezionare **flusso di registrazione**.

    ![Screenshot che mostra il collegamento al flusso di log](./media/monitoring/log-stream.png)

I log possono essere trasmessi anche tramite l'interfaccia della riga di comando di [Azure o Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), anche tramite l'cloud Shell.

## <a name="alerts"></a>Avvisi

Monitoraggio di Azure fornisce anche [avvisi in tempo reale](/azure/monitoring-and-diagnostics/insights-alerts-portal) in base a metriche, eventi amministrativi e altri criteri.

> *Nota: attualmente gli avvisi sulle metriche dell'app Web sono disponibili solo nel servizio avvisi (versione classica).*

Il [servizio avvisi (versione classica)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) si trova in monitoraggio di Azure o nella sezione **monitoraggio** delle impostazioni del servizio app.

![Collegamento avvisi (versione classica)](./media/monitoring/alerts.png)

## <a name="live-debugging"></a>Debug in tempo reale

App Azure servizio può essere sottoposto [a debug in remoto con Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) quando i log non forniscono informazioni sufficienti. Tuttavia, il debug remoto richiede che l'app venga compilata con i simboli di debug. Il debug non deve essere eseguito nell'ambiente di produzione, tranne che come ultima risorsa.

## <a name="conclusion"></a>Conclusione

In questa sezione sono state completate le attività seguenti:

* Trovare i dati di monitoraggio e risoluzione dei problemi di base nel portale di Azure
* Informazioni su come monitoraggio di Azure fornisce un'occhiata più approfondita alle metriche in tutti i servizi di Azure
* Connettere l'app Web con Application Insights per la profilatura dell'app
* Attivare la registrazione e scoprire dove scaricare i log
* Eseguire lo streaming dei log in tempo reale
* Informazioni su dove configurare gli avvisi
* Informazioni sul debug remoto app Azure le app Web del servizio.

## <a name="additional-reading"></a>Altre letture

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Monitoraggio delle prestazioni dell'applicazione web di Azure](/azure/application-insights/app-insights-azure-web-apps)
* [Abilitare la registrazione diagnostica per le app Web nel servizio app di Azure](/azure/app-service/web-sites-enable-diagnostic-log)
* [Risoluzione dei problemi di un'app Web nel servizio app di Azure tramite Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Creare avvisi sulle metriche classici in Monitoraggio di Azure per i servizi di Azure: portale di Azure](/azure/monitoring-and-diagnostics/insights-alerts-portal)
