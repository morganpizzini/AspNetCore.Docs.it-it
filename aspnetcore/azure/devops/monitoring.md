---
title: Monitorare ed eseguire il debug - DevOps con ASP.NET Core e AzureMonitor and debug - DevOps with ASP.NET Core and Azure
author: CamSoper
description: Monitoraggio e debug del codice come parte di una soluzione DevOps con ASP.NET Core e AzureMonitoring and debugging your code as part of a DevOps solution with ASP.NET Core and Azure
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 07/10/2019
uid: azure/devops/monitor
ms.openlocfilehash: 1d8ed99f4387dbc99929164c558cc2ce14bd9ea0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659502"
---
# <a name="monitor-and-debug"></a>Monitorare ed eseguire il debug

Dopo aver distribuito l'app e creato una pipeline DevOps, è importante comprendere come monitorare e risolvere i problemi dell'app.

In questa sezione verranno completate le attività seguenti:In this section, you'll complete the following tasks:

* Trovare i dati di base di monitoraggio e risoluzione dei problemi nel portale di AzureFind basic monitoring and troubleshooting data in the Azure portal
* Informazioni su come Monitoraggio di Azure offre uno sguardo più approfondito alle metriche in tutti i servizi di AzureLearn how Azure Monitor provides a deeper look at metrics across all Azure services
* Connettere l'app Web con Application Insights per la profilatura dell'appConnect the web app with Application Insights for app profiling
* Attivare la registrazione e scoprire dove scaricare i log
* Streaming dei log in tempo reale
* Scopri dove configurare gli avvisi
* Informazioni sul debug remoto delle app Web del servizio app di Azure.Learn about remote debugging Azure App web apps.

## <a name="basic-monitoring-and-troubleshooting"></a>Monitoraggio di base e risoluzione dei problemi

Le app Web del Servizio app sono facilmente monitorate in tempo reale. Il portale di Azure esegue il rendering delle metriche in grafici e grafici di facile comprensione.

1. Aprire il portale di [Azure](https://portal.azure.com)e quindi passare al servizio App *unique_number\<\> mywebapp.*

1. La scheda **Panoramica** visualizza utili informazioni "a colpo d'occhio", inclusi i grafici che visualizzano metriche recenti.

    ![Schermata che mostra il pannello Panoramica](./media/monitoring/overview.png)

    * **Http 5xx**: Conteggio degli errori sul lato server, in genere eccezioni nel codice ASP.NET Core.Http 5xx : Count of server-side errors, usually exceptions in ASP.NET Core code.
    * **Dati in**ingresso: ingresso dati nell'app Web.
    * **Data Out**: I dati escono dall'app Web ai client.
    * **Richieste**: Numero di richieste HTTP.
    * **Tempo medio di risposta**: Tempo medio di risposta dell'app Web alle richieste HTTP.

    In questa pagina sono disponibili anche diversi strumenti self-service per la risoluzione dei problemi e l'ottimizzazione.

    ![Schermata che mostra gli strumenti self-service](./media/monitoring/wizards.png)

    * **La diagnosi e la risoluzione dei problemi** sono uno strumento di risoluzione dei problemi self-service.
    * **Application Insights** è per la profilatura delle prestazioni e del comportamento dell'app e viene illustrato più avanti in questa sezione.
    * **App Service Advisor** fornisce consigli per ottimizzare l'esperienza dell'app.

## <a name="advanced-monitoring"></a>Monitoraggio avanzato

[Monitoraggio di Azure](/azure/monitoring-and-diagnostics/) è il servizio centralizzato per il monitoraggio di tutte le metriche e l'impostazione di avvisi tra i servizi di Azure.Azure Monitor is the centralized service for monitoring all metrics and setting alerts across Azure services. In Monitoraggio di Azure, gli amministratori possono monitorare in modo granulare le prestazioni e identificare le tendenze. Ogni servizio di Azure offre il proprio [set di metriche](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) a Monitoraggio di Azure.Each Azure service offers its own set of metrics to Azure Monitor.

## <a name="profile-with-application-insights"></a>Profilo con Application Insights

[Application Insights](/azure/application-insights/app-insights-overview) è un servizio di Azure per l'analisi delle prestazioni e della stabilità delle app Web e del modo in cui gli utenti le usano. I dati di Application Insights sono più ampi e profondi di quelli di Monitoraggio di Azure.The data from Application Insights is broader and deeper than that of Azure Monitor. I dati possono fornire agli sviluppatori e agli amministratori informazioni chiave per migliorare le app. Application Insights può essere aggiunto a una risorsa del servizio app di Azure senza modifiche al codice.

1. Aprire il portale di [Azure](https://portal.azure.com)e quindi passare al servizio App *unique_number\<\> mywebapp.*
1. Nella scheda **Panoramica** fare clic sul riquadro **Application Insights.From** the Overview tab, click the Application Insights tile.

    ![Riquadro Application Insights](./media/monitoring/app-insights.png)

1. Selezionare il pulsante di opzione **Crea nuova risorsa.** Usare il nome della risorsa predefinito e selezionare il percorso per la risorsa Application Insights.Use the default resource name, and select the location for the Application Insights resource. Non è necessario che il percorso corrisponda a quello dell'app Web.

    ![Configurazione di Application Insights](./media/monitoring/new-app-insights.png)

1. Per **Runtime/Framework**, selezionare **ASP.NET Core**. Accettare le impostazioni predefinite.
1. Selezionare **OK**. Se viene richiesto di confermare, selezionare **Continua**.
1. Dopo aver creato la risorsa, fare clic sul nome della risorsa Application Insights per passare direttamente alla pagina Application Insights.After the resource has been created, click the name of Application Insights resource to navigate directly to the Application Insights page.

    ![Nuova risorsa di Application Insights pronta](./media/monitoring/new-app-insights-done.png)

Quando l'app viene usata, i dati si accumulano. Selezionare **Aggiorna** per ricaricare il pannello con nuovi dati.

![Scheda Panoramica di Application Insights](./media/monitoring/app-insights-overview.png)

Application Insights fornisce informazioni utili sul lato server senza alcuna configurazione aggiuntiva. Per ottenere il massimo valore da Application Insights, [instrumentare l'app con Application Insights SDK.](/azure/application-insights/app-insights-asp-net-core) Se configurato correttamente, il servizio fornisce il monitoraggio end-to-end sul server Web e sul browser, incluse le prestazioni lato client. Per ulteriori informazioni, vedere la [documentazione di Application Insights](/azure/application-insights/app-insights-overview).

## <a name="logging"></a>Registrazione

Web server and app logs are disabled by default in Azure App Service. Abilitare i registri con la seguente procedura:

1. Aprire il [portale di Azure](https://portal.azure.com)e passare al servizio App unique_number *\<\> mywebapp.*
1. Nel menu a sinistra, scorri verso il basso fino alla sezione **Monitoraggio.** Selezionare **Registri di diagnostica**.

    ![Collegamento registri di diagnostica](./media/monitoring/logging.png)

1. Attivare **Registrazione applicazioni (filesystem)**. Se richiesto, fare clic sulla casella per installare le estensioni per abilitare la registrazione dell'app nell'app Web.
1. Impostare **la registrazione del server Web su** File **system**.
1. Immettere il **periodo di conservazione** in giorni. Ad esempio, 30.
1. Fare clic su **Salva**.

ASP.NET i log di base e del server Web (servizio app) vengono generati per l'app Web. Possono essere scaricati utilizzando le informazioni FTP/FTPS visualizzate. La password è uguale alle credenziali di distribuzione create in precedenza in questa guida. I log possono essere [trasmessi direttamente al computer locale con PowerShell o l'interfaccia della](/azure/app-service/web-sites-enable-diagnostic-log#download)riga di comando di Azure. I log possono essere [visualizzati](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights)anche in Application Insights .

## <a name="log-streaming"></a>Streaming dei log

I log dell'app e del server Web possono essere trasmessi in tempo reale tramite il portale.

1. Aprire il [portale di Azure](https://portal.azure.com)e passare al servizio App unique_number *\<\> mywebapp.*
1. Nel menu a sinistra, scorri verso il basso fino alla sezione **Monitoraggio** e seleziona **Registra flusso**.

    ![Schermata che mostra il collegamento del flusso di log](./media/monitoring/log-stream.png)

I log possono anche essere [trasmessi tramite l'interfaccia della riga di comando di Azure o Azure PowerShell,](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs)anche tramite Cloud Shell.

## <a name="alerts"></a>Avvisi

Monitoraggio azure fornisce anche avvisi in [tempo reale](/azure/monitoring-and-diagnostics/insights-alerts-portal) in base a metriche, eventi amministrativi e altri criteri.

> *Nota: attualmente gli avvisi sulle metriche delle app Web sono disponibili solo nel servizio Avvisi (classico).*

Il [servizio Avvisi (classico)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) è disponibile in Monitoraggio di Azure o nella sezione **Monitoraggio** delle impostazioni del servizio app.

![Collegamento Avvisi (classico)](./media/monitoring/alerts.png)

## <a name="live-debugging"></a>Debug in attivo

Il servizio app di Azure può essere [sottoposto a debug in remoto con Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) quando i log non forniscono informazioni sufficienti. Tuttavia, il debug remoto richiede che l'app venga compilata con simboli di debug. Il debug non deve essere eseguito nell'ambiente di produzione, ad eccezione di ultima risorsa.

## <a name="conclusion"></a>Conclusioni

In questa sezione sono state completate le attività seguenti:In this section, you completed the following tasks:

* Trovare i dati di base di monitoraggio e risoluzione dei problemi nel portale di AzureFind basic monitoring and troubleshooting data in the Azure portal
* Informazioni su come Monitoraggio di Azure offre uno sguardo più approfondito alle metriche in tutti i servizi di AzureLearn how Azure Monitor provides a deeper look at metrics across all Azure services
* Connettere l'app Web con Application Insights per la profilatura dell'appConnect the web app with Application Insights for app profiling
* Attivare la registrazione e scoprire dove scaricare i log
* Streaming dei log in tempo reale
* Scopri dove configurare gli avvisi
* Informazioni sul debug remoto delle app Web del servizio app di Azure.Learn about remote debugging Azure App web apps.

## <a name="additional-reading"></a>Informazioni aggiuntive

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Monitoraggio delle prestazioni dell'applicazione web di Azure](/azure/application-insights/app-insights-azure-web-apps)
* [Abilitare la registrazione diagnostica per le app Web nel servizio app di Azure](/azure/app-service/web-sites-enable-diagnostic-log)
* [Risoluzione dei problemi di un'app Web nel servizio app di Azure tramite Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Creare avvisi sulle metriche classici in Monitoraggio di Azure per i servizi di Azure: portale di Azure](/azure/monitoring-and-diagnostics/insights-alerts-portal)
