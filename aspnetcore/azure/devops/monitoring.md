---
title: Monitorare ed eseguire il debug di DevOps con ASP.NET Core e Azure
author: CamSoper
description: Monitoraggio e debug del codice come parte di una soluzione DevOps con ASP.NET Core e Azure
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 07/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/monitor
ms.openlocfilehash: 3af36a37124968e13952e8bf5de1b643265a4a5b
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766888"
---
# <a name="monitor-and-debug"></a><span data-ttu-id="e07c4-103">Monitorare ed eseguire il debug</span><span class="sxs-lookup"><span data-stu-id="e07c4-103">Monitor and debug</span></span>

<span data-ttu-id="e07c4-104">Dopo aver distribuito l'app e creato una pipeline DevOps, è importante comprendere come monitorare e risolvere i problemi dell'app.</span><span class="sxs-lookup"><span data-stu-id="e07c4-104">Having deployed the app and built a DevOps pipeline, it's important to understand how to monitor and troubleshoot the app.</span></span>

<span data-ttu-id="e07c4-105">In questa sezione verranno completate le attività seguenti:</span><span class="sxs-lookup"><span data-stu-id="e07c4-105">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="e07c4-106">Trovare i dati di monitoraggio e risoluzione dei problemi di base nel portale di Azure</span><span class="sxs-lookup"><span data-stu-id="e07c4-106">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="e07c4-107">Informazioni su come monitoraggio di Azure fornisce un'occhiata più approfondita alle metriche in tutti i servizi di Azure</span><span class="sxs-lookup"><span data-stu-id="e07c4-107">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="e07c4-108">Connettere l'app Web con Application Insights per la profilatura dell'app</span><span class="sxs-lookup"><span data-stu-id="e07c4-108">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="e07c4-109">Attivare la registrazione e scoprire dove scaricare i log</span><span class="sxs-lookup"><span data-stu-id="e07c4-109">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="e07c4-110">Streaming dei log in tempo reale</span><span class="sxs-lookup"><span data-stu-id="e07c4-110">Stream logs in real time</span></span>
* <span data-ttu-id="e07c4-111">Informazioni su dove configurare gli avvisi</span><span class="sxs-lookup"><span data-stu-id="e07c4-111">Learn where to set up alerts</span></span>
* <span data-ttu-id="e07c4-112">Informazioni sul debug remoto app Azure le app Web del servizio.</span><span class="sxs-lookup"><span data-stu-id="e07c4-112">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="basic-monitoring-and-troubleshooting"></a><span data-ttu-id="e07c4-113">Monitoraggio e risoluzione dei problemi di base</span><span class="sxs-lookup"><span data-stu-id="e07c4-113">Basic monitoring and troubleshooting</span></span>

<span data-ttu-id="e07c4-114">Le app Web del servizio app sono facilmente monitorate in tempo reale.</span><span class="sxs-lookup"><span data-stu-id="e07c4-114">App Service web apps are easily monitored in real time.</span></span> <span data-ttu-id="e07c4-115">Il portale di Azure esegue il rendering delle metriche in grafici e grafici di facile comprensione.</span><span class="sxs-lookup"><span data-stu-id="e07c4-115">The Azure portal renders metrics in easy-to-understand charts and graphs.</span></span>

1. <span data-ttu-id="e07c4-116">Aprire il [portale di Azure](https://portal.azure.com), quindi passare al servizio app \*MyWebApp\<unique_number\> \* .</span><span class="sxs-lookup"><span data-stu-id="e07c4-116">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>

1. <span data-ttu-id="e07c4-117">Nella scheda **Panoramica** sono visualizzate informazioni utili, inclusi i grafici che visualizzano le metriche recenti.</span><span class="sxs-lookup"><span data-stu-id="e07c4-117">The **Overview** tab displays useful "at-a-glance" information, including graphs displaying recent metrics.</span></span>

    ![Screenshot che mostra il pannello Panoramica](./media/monitoring/overview.png)

    * <span data-ttu-id="e07c4-119">**Http 5xx**: numero di errori sul lato server, in genere eccezioni nel codice ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e07c4-119">**Http 5xx**: Count of server-side errors, usually exceptions in ASP.NET Core code.</span></span>
    * <span data-ttu-id="e07c4-120">**Dati in**: dati in ingresso nell'app Web.</span><span class="sxs-lookup"><span data-stu-id="e07c4-120">**Data In**: Data ingress coming into your web app.</span></span>
    * <span data-ttu-id="e07c4-121">**Dati in uscita**: dati in uscita dall'app Web ai client.</span><span class="sxs-lookup"><span data-stu-id="e07c4-121">**Data Out**: Data egress from your web app to clients.</span></span>
    * <span data-ttu-id="e07c4-122">**Richieste**: numero di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="e07c4-122">**Requests**: Count of HTTP requests.</span></span>
    * <span data-ttu-id="e07c4-123">**Tempo medio di risposta**: tempo medio per l'app Web per rispondere alle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="e07c4-123">**Average Response Time**: Average time for the web app to respond to HTTP requests.</span></span>

    <span data-ttu-id="e07c4-124">In questa pagina sono disponibili anche diversi strumenti self-service per la risoluzione dei problemi e l'ottimizzazione.</span><span class="sxs-lookup"><span data-stu-id="e07c4-124">Several self-service tools for troubleshooting and optimization are also found on this page.</span></span>

    ![Screenshot che Mostra gli strumenti self-service](./media/monitoring/wizards.png)

    * <span data-ttu-id="e07c4-126">**Diagnosticare e risolvere i problemi** è uno strumento di risoluzione dei problemi self-service.</span><span class="sxs-lookup"><span data-stu-id="e07c4-126">**Diagnose and solve problems** is a self-service troubleshooter.</span></span>
    * <span data-ttu-id="e07c4-127">**Application Insights** è per la profilatura delle prestazioni e il comportamento dell'app e viene illustrato più avanti in questa sezione.</span><span class="sxs-lookup"><span data-stu-id="e07c4-127">**Application Insights** is for profiling performance and app behavior, and is discussed later in this section.</span></span>
    * <span data-ttu-id="e07c4-128">**App Service Advisor** apporta consigli per ottimizzare l'esperienza dell'app.</span><span class="sxs-lookup"><span data-stu-id="e07c4-128">**App Service Advisor** makes recommendations to tune your app experience.</span></span>

## <a name="advanced-monitoring"></a><span data-ttu-id="e07c4-129">Monitoraggio avanzato</span><span class="sxs-lookup"><span data-stu-id="e07c4-129">Advanced monitoring</span></span>

<span data-ttu-id="e07c4-130">[Monitoraggio di Azure](/azure/monitoring-and-diagnostics/) è il servizio centralizzato per il monitoraggio di tutte le metriche e l'impostazione degli avvisi nei servizi di Azure.</span><span class="sxs-lookup"><span data-stu-id="e07c4-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) is the centralized service for monitoring all metrics and setting alerts across Azure services.</span></span> <span data-ttu-id="e07c4-131">In monitoraggio di Azure, gli amministratori possono tenere traccia in dettaglio delle prestazioni e identificare le tendenze.</span><span class="sxs-lookup"><span data-stu-id="e07c4-131">Within Azure Monitor, administrators can granularly track performance and identify trends.</span></span> <span data-ttu-id="e07c4-132">Ogni servizio di Azure offre un proprio [set di metriche](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) in monitoraggio di Azure.</span><span class="sxs-lookup"><span data-stu-id="e07c4-132">Each Azure service offers its own [set of metrics](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) to Azure Monitor.</span></span>

## <a name="profile-with-application-insights"></a><span data-ttu-id="e07c4-133">Profilo con Application Insights</span><span class="sxs-lookup"><span data-stu-id="e07c4-133">Profile with Application Insights</span></span>

<span data-ttu-id="e07c4-134">[Application Insights](/azure/application-insights/app-insights-overview) è un servizio di Azure per analizzare le prestazioni e la stabilità delle app Web e il modo in cui gli utenti le usano.</span><span class="sxs-lookup"><span data-stu-id="e07c4-134">[Application Insights](/azure/application-insights/app-insights-overview) is an Azure service for analyzing the performance and stability of web apps and how users use them.</span></span> <span data-ttu-id="e07c4-135">I dati di Application Insights sono più ampi e più profondi rispetto a quelli di monitoraggio di Azure.</span><span class="sxs-lookup"><span data-stu-id="e07c4-135">The data from Application Insights is broader and deeper than that of Azure Monitor.</span></span> <span data-ttu-id="e07c4-136">I dati possono fornire a sviluppatori e amministratori informazioni chiave per migliorare le app.</span><span class="sxs-lookup"><span data-stu-id="e07c4-136">The data can provide developers and administrators with key information for improving apps.</span></span> <span data-ttu-id="e07c4-137">Application Insights possono essere aggiunti a una risorsa del servizio app Azure senza modifiche al codice.</span><span class="sxs-lookup"><span data-stu-id="e07c4-137">Application Insights can be added to an Azure App Service resource without code changes.</span></span>

1. <span data-ttu-id="e07c4-138">Aprire il [portale di Azure](https://portal.azure.com), quindi passare al servizio app \*MyWebApp\<unique_number\> \* .</span><span class="sxs-lookup"><span data-stu-id="e07c4-138">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="e07c4-139">Nella scheda **Panoramica** fare clic sul riquadro **Application Insights** .</span><span class="sxs-lookup"><span data-stu-id="e07c4-139">From the **Overview** tab, click the **Application Insights** tile.</span></span>

    ![Riquadro Application Insights](./media/monitoring/app-insights.png)

1. <span data-ttu-id="e07c4-141">Selezionare il pulsante di opzione **Crea nuova risorsa** .</span><span class="sxs-lookup"><span data-stu-id="e07c4-141">Select the **Create new resource** radio button.</span></span> <span data-ttu-id="e07c4-142">Usare il nome predefinito della risorsa e selezionare il percorso per la risorsa Application Insights.</span><span class="sxs-lookup"><span data-stu-id="e07c4-142">Use the default resource name, and select the location for the Application Insights resource.</span></span> <span data-ttu-id="e07c4-143">Non è necessario che la località corrisponda a quella dell'app Web.</span><span class="sxs-lookup"><span data-stu-id="e07c4-143">The location doesn't need to match that of your web app.</span></span>

    ![Installazione di Application Insights](./media/monitoring/new-app-insights.png)

1. <span data-ttu-id="e07c4-145">Per **Runtime/Framework**, selezionare **ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="e07c4-145">For **Runtime/Framework**, select **ASP.NET Core**.</span></span> <span data-ttu-id="e07c4-146">Accettare le impostazioni predefinite.</span><span class="sxs-lookup"><span data-stu-id="e07c4-146">Accept the default settings.</span></span>
1. <span data-ttu-id="e07c4-147">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="e07c4-147">Select **OK**.</span></span> <span data-ttu-id="e07c4-148">Se viene richiesto di confermare, selezionare **continua**.</span><span class="sxs-lookup"><span data-stu-id="e07c4-148">If prompted to confirm, select **Continue**.</span></span>
1. <span data-ttu-id="e07c4-149">Dopo aver creato la risorsa, fare clic sul nome della risorsa Application Insights per passare direttamente alla pagina Application Insights.</span><span class="sxs-lookup"><span data-stu-id="e07c4-149">After the resource has been created, click the name of Application Insights resource to navigate directly to the Application Insights page.</span></span>

    ![La nuova risorsa Application Insights è pronta](./media/monitoring/new-app-insights-done.png)

<span data-ttu-id="e07c4-151">Quando si usa l'app, i dati si accumulano.</span><span class="sxs-lookup"><span data-stu-id="e07c4-151">As the app is used, data accumulates.</span></span> <span data-ttu-id="e07c4-152">Selezionare **Aggiorna** per ricaricare il pannello con nuovi dati.</span><span class="sxs-lookup"><span data-stu-id="e07c4-152">Select **Refresh** to reload the blade with new data.</span></span>

![Scheda Panoramica Application Insights](./media/monitoring/app-insights-overview.png)

<span data-ttu-id="e07c4-154">Application Insights fornisce informazioni utili sul lato server senza alcuna configurazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="e07c4-154">Application Insights provides useful server-side information with no additional configuration.</span></span> <span data-ttu-id="e07c4-155">Per ottenere il massimo valore da Application Insights, [instrumentare l'app con l'SDK di Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="e07c4-155">To get the most value from Application Insights, [instrument your app with the Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span></span> <span data-ttu-id="e07c4-156">Se configurato correttamente, il servizio fornisce il monitoraggio end-to-end nel server Web e nel browser, incluse le prestazioni lato client.</span><span class="sxs-lookup"><span data-stu-id="e07c4-156">When properly configured, the service provides end-to-end monitoring across the web server and browser, including client-side performance.</span></span> <span data-ttu-id="e07c4-157">Per ulteriori informazioni, vedere la [documentazione Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="e07c4-157">For more information, see the [Application Insights documentation](/azure/application-insights/app-insights-overview).</span></span>

## <a name="logging"></a><span data-ttu-id="e07c4-158">Registrazione</span><span class="sxs-lookup"><span data-stu-id="e07c4-158">Logging</span></span>

<span data-ttu-id="e07c4-159">Il server Web e i log delle app sono disabilitati per impostazione predefinita nel servizio app Azure.</span><span class="sxs-lookup"><span data-stu-id="e07c4-159">Web server and app logs are disabled by default in Azure App Service.</span></span> <span data-ttu-id="e07c4-160">Abilitare i log con i passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="e07c4-160">Enable the logs with the following steps:</span></span>

1. <span data-ttu-id="e07c4-161">Aprire il [portale di Azure](https://portal.azure.com)e passare al servizio app \*MyWebApp\<unique_number\> \* .</span><span class="sxs-lookup"><span data-stu-id="e07c4-161">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="e07c4-162">Nel menu a sinistra scorrere verso il basso fino alla sezione **monitoraggio** .</span><span class="sxs-lookup"><span data-stu-id="e07c4-162">In the menu to the left, scroll down to the **Monitoring** section.</span></span> <span data-ttu-id="e07c4-163">Selezionare **log di diagnostica**.</span><span class="sxs-lookup"><span data-stu-id="e07c4-163">Select **Diagnostics logs**.</span></span>

    ![Collegamento log di diagnostica](./media/monitoring/logging.png)

1. <span data-ttu-id="e07c4-165">Attivare la **registrazione delle applicazioni (filesystem)**.</span><span class="sxs-lookup"><span data-stu-id="e07c4-165">Turn on **Application Logging (Filesystem)**.</span></span> <span data-ttu-id="e07c4-166">Se richiesto, fare clic sulla casella per installare le estensioni per abilitare la registrazione delle app nell'app Web.</span><span class="sxs-lookup"><span data-stu-id="e07c4-166">If prompted, click the box to install the extensions to enable app logging in the web app.</span></span>
1. <span data-ttu-id="e07c4-167">Impostare la **registrazione del server Web** sul **file System**.</span><span class="sxs-lookup"><span data-stu-id="e07c4-167">Set **Web server logging** to **File System**.</span></span>
1. <span data-ttu-id="e07c4-168">Immettere il **periodo di conservazione** in giorni.</span><span class="sxs-lookup"><span data-stu-id="e07c4-168">Enter the **Retention Period** in days.</span></span> <span data-ttu-id="e07c4-169">Ad esempio, 30.</span><span class="sxs-lookup"><span data-stu-id="e07c4-169">For example, 30.</span></span>
1. <span data-ttu-id="e07c4-170">Fare clic su **Salva**.</span><span class="sxs-lookup"><span data-stu-id="e07c4-170">Click **Save**.</span></span>

<span data-ttu-id="e07c4-171">ASP.NET Core e i log del server Web (servizio app) vengono generati per l'app Web.</span><span class="sxs-lookup"><span data-stu-id="e07c4-171">ASP.NET Core and web server (App Service) logs are generated for the web app.</span></span> <span data-ttu-id="e07c4-172">Possono essere scaricati usando le informazioni FTP/FTPS visualizzate.</span><span class="sxs-lookup"><span data-stu-id="e07c4-172">They can be downloaded using the FTP/FTPS information displayed.</span></span> <span data-ttu-id="e07c4-173">La password è identica a quella delle credenziali di distribuzione create in precedenza in questa guida.</span><span class="sxs-lookup"><span data-stu-id="e07c4-173">The password is the same as the deployment credentials created earlier in this guide.</span></span> <span data-ttu-id="e07c4-174">I log possono essere [trasmessi direttamente al computer locale con PowerShell o l'interfaccia della riga di comando di Azure](/azure/app-service/web-sites-enable-diagnostic-log#download).</span><span class="sxs-lookup"><span data-stu-id="e07c4-174">The logs can be [streamed directly to your local machine with PowerShell or Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span></span> <span data-ttu-id="e07c4-175">I log possono essere [visualizzati anche in Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span><span class="sxs-lookup"><span data-stu-id="e07c4-175">Logs can also be [viewed in Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span></span>

## <a name="log-streaming"></a><span data-ttu-id="e07c4-176">Streaming dei log</span><span class="sxs-lookup"><span data-stu-id="e07c4-176">Log streaming</span></span>

<span data-ttu-id="e07c4-177">I log del server Web e dell'app possono essere trasmessi in tempo reale tramite il portale.</span><span class="sxs-lookup"><span data-stu-id="e07c4-177">App and web server logs can be streamed in real time through the portal.</span></span>

1. <span data-ttu-id="e07c4-178">Aprire il [portale di Azure](https://portal.azure.com)e passare al servizio app \*MyWebApp\<unique_number\> \* .</span><span class="sxs-lookup"><span data-stu-id="e07c4-178">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="e07c4-179">Nel menu a sinistra scorrere verso il basso fino alla sezione **monitoraggio** e selezionare **flusso di registrazione**.</span><span class="sxs-lookup"><span data-stu-id="e07c4-179">In the menu to the left, scroll down to the **Monitoring** section and select **Log stream**.</span></span>

    ![Screenshot che mostra il collegamento al flusso di log](./media/monitoring/log-stream.png)

<span data-ttu-id="e07c4-181">I log possono essere trasmessi anche tramite l'interfaccia della riga di comando di [Azure o Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), anche tramite l'cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="e07c4-181">Logs can also be [streamed via Azure CLI or Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), including through the Cloud Shell.</span></span>

## <a name="alerts"></a><span data-ttu-id="e07c4-182">Avvisi</span><span class="sxs-lookup"><span data-stu-id="e07c4-182">Alerts</span></span>

<span data-ttu-id="e07c4-183">Monitoraggio di Azure fornisce anche [avvisi in tempo reale](/azure/monitoring-and-diagnostics/insights-alerts-portal) in base a metriche, eventi amministrativi e altri criteri.</span><span class="sxs-lookup"><span data-stu-id="e07c4-183">Azure Monitor also provides [real time alerts](/azure/monitoring-and-diagnostics/insights-alerts-portal) based on metrics, administrative events, and other criteria.</span></span>

> <span data-ttu-id="e07c4-184">*Nota: attualmente gli avvisi sulle metriche dell'app Web sono disponibili solo nel servizio avvisi (versione classica).*</span><span class="sxs-lookup"><span data-stu-id="e07c4-184">*Note: Currently alerting on web app metrics is only available in the Alerts (classic) service.*</span></span>

<span data-ttu-id="e07c4-185">Il [servizio avvisi (versione classica)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) si trova in monitoraggio di Azure o nella sezione **monitoraggio** delle impostazioni del servizio app.</span><span class="sxs-lookup"><span data-stu-id="e07c4-185">The [Alerts (classic) service](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) can be found in Azure Monitor or under the **Monitoring** section of the App Service settings.</span></span>

![Collegamento avvisi (versione classica)](./media/monitoring/alerts.png)

## <a name="live-debugging"></a><span data-ttu-id="e07c4-187">Debug in tempo reale</span><span class="sxs-lookup"><span data-stu-id="e07c4-187">Live debugging</span></span>

<span data-ttu-id="e07c4-188">App Azure servizio può essere sottoposto [a debug in remoto con Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) quando i log non forniscono informazioni sufficienti.</span><span class="sxs-lookup"><span data-stu-id="e07c4-188">Azure App Service can be [debugged remotely with Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) when logs don't provide enough information.</span></span> <span data-ttu-id="e07c4-189">Tuttavia, il debug remoto richiede che l'app venga compilata con i simboli di debug.</span><span class="sxs-lookup"><span data-stu-id="e07c4-189">However, remote debugging requires the app to be compiled with debug symbols.</span></span> <span data-ttu-id="e07c4-190">Il debug non deve essere eseguito nell'ambiente di produzione, tranne che come ultima risorsa.</span><span class="sxs-lookup"><span data-stu-id="e07c4-190">Debugging shouldn't be done in production, except as a last resort.</span></span>

## <a name="conclusion"></a><span data-ttu-id="e07c4-191">Conclusioni</span><span class="sxs-lookup"><span data-stu-id="e07c4-191">Conclusion</span></span>

<span data-ttu-id="e07c4-192">In questa sezione sono state completate le attività seguenti:</span><span class="sxs-lookup"><span data-stu-id="e07c4-192">In this section, you completed the following tasks:</span></span>

* <span data-ttu-id="e07c4-193">Trovare i dati di monitoraggio e risoluzione dei problemi di base nel portale di Azure</span><span class="sxs-lookup"><span data-stu-id="e07c4-193">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="e07c4-194">Informazioni su come monitoraggio di Azure fornisce un'occhiata più approfondita alle metriche in tutti i servizi di Azure</span><span class="sxs-lookup"><span data-stu-id="e07c4-194">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="e07c4-195">Connettere l'app Web con Application Insights per la profilatura dell'app</span><span class="sxs-lookup"><span data-stu-id="e07c4-195">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="e07c4-196">Attivare la registrazione e scoprire dove scaricare i log</span><span class="sxs-lookup"><span data-stu-id="e07c4-196">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="e07c4-197">Streaming dei log in tempo reale</span><span class="sxs-lookup"><span data-stu-id="e07c4-197">Stream logs in real time</span></span>
* <span data-ttu-id="e07c4-198">Informazioni su dove configurare gli avvisi</span><span class="sxs-lookup"><span data-stu-id="e07c4-198">Learn where to set up alerts</span></span>
* <span data-ttu-id="e07c4-199">Informazioni sul debug remoto app Azure le app Web del servizio.</span><span class="sxs-lookup"><span data-stu-id="e07c4-199">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="e07c4-200">Informazioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="e07c4-200">Additional reading</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="e07c4-201">Monitoraggio delle prestazioni dell'applicazione web di Azure</span><span class="sxs-lookup"><span data-stu-id="e07c4-201">Monitor Azure web app performance with Application Insights</span></span>](/azure/application-insights/app-insights-azure-web-apps)
* [<span data-ttu-id="e07c4-202">Abilitare la registrazione diagnostica per le app Web nel servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="e07c4-202">Enable diagnostics logging for web apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)
* [<span data-ttu-id="e07c4-203">Risoluzione dei problemi di un'app Web nel servizio app di Azure tramite Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e07c4-203">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="e07c4-204">Creare avvisi sulle metriche classici in Monitoraggio di Azure per i servizi di Azure: portale di Azure</span><span class="sxs-lookup"><span data-stu-id="e07c4-204">Create classic metric alerts in Azure Monitor for Azure services - Azure portal</span></span>](/azure/monitoring-and-diagnostics/insights-alerts-portal)
