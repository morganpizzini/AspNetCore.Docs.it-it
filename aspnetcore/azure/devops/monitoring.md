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
# <a name="monitor-and-debug"></a><span data-ttu-id="37d42-103">Monitorare ed eseguire il debug</span><span class="sxs-lookup"><span data-stu-id="37d42-103">Monitor and debug</span></span>

<span data-ttu-id="37d42-104">Dopo aver distribuito l'app e creato una pipeline DevOps, è importante comprendere come monitorare e risolvere i problemi dell'app.</span><span class="sxs-lookup"><span data-stu-id="37d42-104">Having deployed the app and built a DevOps pipeline, it's important to understand how to monitor and troubleshoot the app.</span></span>

<span data-ttu-id="37d42-105">In questa sezione verranno completate le attività seguenti:In this section, you'll complete the following tasks:</span><span class="sxs-lookup"><span data-stu-id="37d42-105">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="37d42-106">Trovare i dati di base di monitoraggio e risoluzione dei problemi nel portale di AzureFind basic monitoring and troubleshooting data in the Azure portal</span><span class="sxs-lookup"><span data-stu-id="37d42-106">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="37d42-107">Informazioni su come Monitoraggio di Azure offre uno sguardo più approfondito alle metriche in tutti i servizi di AzureLearn how Azure Monitor provides a deeper look at metrics across all Azure services</span><span class="sxs-lookup"><span data-stu-id="37d42-107">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="37d42-108">Connettere l'app Web con Application Insights per la profilatura dell'appConnect the web app with Application Insights for app profiling</span><span class="sxs-lookup"><span data-stu-id="37d42-108">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="37d42-109">Attivare la registrazione e scoprire dove scaricare i log</span><span class="sxs-lookup"><span data-stu-id="37d42-109">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="37d42-110">Streaming dei log in tempo reale</span><span class="sxs-lookup"><span data-stu-id="37d42-110">Stream logs in real time</span></span>
* <span data-ttu-id="37d42-111">Scopri dove configurare gli avvisi</span><span class="sxs-lookup"><span data-stu-id="37d42-111">Learn where to set up alerts</span></span>
* <span data-ttu-id="37d42-112">Informazioni sul debug remoto delle app Web del servizio app di Azure.Learn about remote debugging Azure App web apps.</span><span class="sxs-lookup"><span data-stu-id="37d42-112">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="basic-monitoring-and-troubleshooting"></a><span data-ttu-id="37d42-113">Monitoraggio di base e risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="37d42-113">Basic monitoring and troubleshooting</span></span>

<span data-ttu-id="37d42-114">Le app Web del Servizio app sono facilmente monitorate in tempo reale.</span><span class="sxs-lookup"><span data-stu-id="37d42-114">App Service web apps are easily monitored in real time.</span></span> <span data-ttu-id="37d42-115">Il portale di Azure esegue il rendering delle metriche in grafici e grafici di facile comprensione.</span><span class="sxs-lookup"><span data-stu-id="37d42-115">The Azure portal renders metrics in easy-to-understand charts and graphs.</span></span>

1. <span data-ttu-id="37d42-116">Aprire il portale di [Azure](https://portal.azure.com)e quindi passare al servizio App *unique_number\<\> mywebapp.*</span><span class="sxs-lookup"><span data-stu-id="37d42-116">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>

1. <span data-ttu-id="37d42-117">La scheda **Panoramica** visualizza utili informazioni "a colpo d'occhio", inclusi i grafici che visualizzano metriche recenti.</span><span class="sxs-lookup"><span data-stu-id="37d42-117">The **Overview** tab displays useful "at-a-glance" information, including graphs displaying recent metrics.</span></span>

    ![Schermata che mostra il pannello Panoramica](./media/monitoring/overview.png)

    * <span data-ttu-id="37d42-119">**Http 5xx**: Conteggio degli errori sul lato server, in genere eccezioni nel codice ASP.NET Core.Http 5xx : Count of server-side errors, usually exceptions in ASP.NET Core code.</span><span class="sxs-lookup"><span data-stu-id="37d42-119">**Http 5xx**: Count of server-side errors, usually exceptions in ASP.NET Core code.</span></span>
    * <span data-ttu-id="37d42-120">**Dati in**ingresso: ingresso dati nell'app Web.</span><span class="sxs-lookup"><span data-stu-id="37d42-120">**Data In**: Data ingress coming into your web app.</span></span>
    * <span data-ttu-id="37d42-121">**Data Out**: I dati escono dall'app Web ai client.</span><span class="sxs-lookup"><span data-stu-id="37d42-121">**Data Out**: Data egress from your web app to clients.</span></span>
    * <span data-ttu-id="37d42-122">**Richieste**: Numero di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="37d42-122">**Requests**: Count of HTTP requests.</span></span>
    * <span data-ttu-id="37d42-123">**Tempo medio di risposta**: Tempo medio di risposta dell'app Web alle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="37d42-123">**Average Response Time**: Average time for the web app to respond to HTTP requests.</span></span>

    <span data-ttu-id="37d42-124">In questa pagina sono disponibili anche diversi strumenti self-service per la risoluzione dei problemi e l'ottimizzazione.</span><span class="sxs-lookup"><span data-stu-id="37d42-124">Several self-service tools for troubleshooting and optimization are also found on this page.</span></span>

    ![Schermata che mostra gli strumenti self-service](./media/monitoring/wizards.png)

    * <span data-ttu-id="37d42-126">**La diagnosi e la risoluzione dei problemi** sono uno strumento di risoluzione dei problemi self-service.</span><span class="sxs-lookup"><span data-stu-id="37d42-126">**Diagnose and solve problems** is a self-service troubleshooter.</span></span>
    * <span data-ttu-id="37d42-127">**Application Insights** è per la profilatura delle prestazioni e del comportamento dell'app e viene illustrato più avanti in questa sezione.</span><span class="sxs-lookup"><span data-stu-id="37d42-127">**Application Insights** is for profiling performance and app behavior, and is discussed later in this section.</span></span>
    * <span data-ttu-id="37d42-128">**App Service Advisor** fornisce consigli per ottimizzare l'esperienza dell'app.</span><span class="sxs-lookup"><span data-stu-id="37d42-128">**App Service Advisor** makes recommendations to tune your app experience.</span></span>

## <a name="advanced-monitoring"></a><span data-ttu-id="37d42-129">Monitoraggio avanzato</span><span class="sxs-lookup"><span data-stu-id="37d42-129">Advanced monitoring</span></span>

<span data-ttu-id="37d42-130">[Monitoraggio di Azure](/azure/monitoring-and-diagnostics/) è il servizio centralizzato per il monitoraggio di tutte le metriche e l'impostazione di avvisi tra i servizi di Azure.Azure Monitor is the centralized service for monitoring all metrics and setting alerts across Azure services.</span><span class="sxs-lookup"><span data-stu-id="37d42-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) is the centralized service for monitoring all metrics and setting alerts across Azure services.</span></span> <span data-ttu-id="37d42-131">In Monitoraggio di Azure, gli amministratori possono monitorare in modo granulare le prestazioni e identificare le tendenze.</span><span class="sxs-lookup"><span data-stu-id="37d42-131">Within Azure Monitor, administrators can granularly track performance and identify trends.</span></span> <span data-ttu-id="37d42-132">Ogni servizio di Azure offre il proprio [set di metriche](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) a Monitoraggio di Azure.Each Azure service offers its own set of metrics to Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="37d42-132">Each Azure service offers its own [set of metrics](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) to Azure Monitor.</span></span>

## <a name="profile-with-application-insights"></a><span data-ttu-id="37d42-133">Profilo con Application Insights</span><span class="sxs-lookup"><span data-stu-id="37d42-133">Profile with Application Insights</span></span>

<span data-ttu-id="37d42-134">[Application Insights](/azure/application-insights/app-insights-overview) è un servizio di Azure per l'analisi delle prestazioni e della stabilità delle app Web e del modo in cui gli utenti le usano.</span><span class="sxs-lookup"><span data-stu-id="37d42-134">[Application Insights](/azure/application-insights/app-insights-overview) is an Azure service for analyzing the performance and stability of web apps and how users use them.</span></span> <span data-ttu-id="37d42-135">I dati di Application Insights sono più ampi e profondi di quelli di Monitoraggio di Azure.The data from Application Insights is broader and deeper than that of Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="37d42-135">The data from Application Insights is broader and deeper than that of Azure Monitor.</span></span> <span data-ttu-id="37d42-136">I dati possono fornire agli sviluppatori e agli amministratori informazioni chiave per migliorare le app.</span><span class="sxs-lookup"><span data-stu-id="37d42-136">The data can provide developers and administrators with key information for improving apps.</span></span> <span data-ttu-id="37d42-137">Application Insights può essere aggiunto a una risorsa del servizio app di Azure senza modifiche al codice.</span><span class="sxs-lookup"><span data-stu-id="37d42-137">Application Insights can be added to an Azure App Service resource without code changes.</span></span>

1. <span data-ttu-id="37d42-138">Aprire il portale di [Azure](https://portal.azure.com)e quindi passare al servizio App *unique_number\<\> mywebapp.*</span><span class="sxs-lookup"><span data-stu-id="37d42-138">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="37d42-139">Nella scheda **Panoramica** fare clic sul riquadro **Application Insights.From** the Overview tab, click the Application Insights tile.</span><span class="sxs-lookup"><span data-stu-id="37d42-139">From the **Overview** tab, click the **Application Insights** tile.</span></span>

    ![Riquadro Application Insights](./media/monitoring/app-insights.png)

1. <span data-ttu-id="37d42-141">Selezionare il pulsante di opzione **Crea nuova risorsa.**</span><span class="sxs-lookup"><span data-stu-id="37d42-141">Select the **Create new resource** radio button.</span></span> <span data-ttu-id="37d42-142">Usare il nome della risorsa predefinito e selezionare il percorso per la risorsa Application Insights.Use the default resource name, and select the location for the Application Insights resource.</span><span class="sxs-lookup"><span data-stu-id="37d42-142">Use the default resource name, and select the location for the Application Insights resource.</span></span> <span data-ttu-id="37d42-143">Non è necessario che il percorso corrisponda a quello dell'app Web.</span><span class="sxs-lookup"><span data-stu-id="37d42-143">The location doesn't need to match that of your web app.</span></span>

    ![Configurazione di Application Insights](./media/monitoring/new-app-insights.png)

1. <span data-ttu-id="37d42-145">Per **Runtime/Framework**, selezionare **ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="37d42-145">For **Runtime/Framework**, select **ASP.NET Core**.</span></span> <span data-ttu-id="37d42-146">Accettare le impostazioni predefinite.</span><span class="sxs-lookup"><span data-stu-id="37d42-146">Accept the default settings.</span></span>
1. <span data-ttu-id="37d42-147">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="37d42-147">Select **OK**.</span></span> <span data-ttu-id="37d42-148">Se viene richiesto di confermare, selezionare **Continua**.</span><span class="sxs-lookup"><span data-stu-id="37d42-148">If prompted to confirm, select **Continue**.</span></span>
1. <span data-ttu-id="37d42-149">Dopo aver creato la risorsa, fare clic sul nome della risorsa Application Insights per passare direttamente alla pagina Application Insights.After the resource has been created, click the name of Application Insights resource to navigate directly to the Application Insights page.</span><span class="sxs-lookup"><span data-stu-id="37d42-149">After the resource has been created, click the name of Application Insights resource to navigate directly to the Application Insights page.</span></span>

    ![Nuova risorsa di Application Insights pronta](./media/monitoring/new-app-insights-done.png)

<span data-ttu-id="37d42-151">Quando l'app viene usata, i dati si accumulano.</span><span class="sxs-lookup"><span data-stu-id="37d42-151">As the app is used, data accumulates.</span></span> <span data-ttu-id="37d42-152">Selezionare **Aggiorna** per ricaricare il pannello con nuovi dati.</span><span class="sxs-lookup"><span data-stu-id="37d42-152">Select **Refresh** to reload the blade with new data.</span></span>

![Scheda Panoramica di Application Insights](./media/monitoring/app-insights-overview.png)

<span data-ttu-id="37d42-154">Application Insights fornisce informazioni utili sul lato server senza alcuna configurazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="37d42-154">Application Insights provides useful server-side information with no additional configuration.</span></span> <span data-ttu-id="37d42-155">Per ottenere il massimo valore da Application Insights, [instrumentare l'app con Application Insights SDK.](/azure/application-insights/app-insights-asp-net-core)</span><span class="sxs-lookup"><span data-stu-id="37d42-155">To get the most value from Application Insights, [instrument your app with the Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span></span> <span data-ttu-id="37d42-156">Se configurato correttamente, il servizio fornisce il monitoraggio end-to-end sul server Web e sul browser, incluse le prestazioni lato client.</span><span class="sxs-lookup"><span data-stu-id="37d42-156">When properly configured, the service provides end-to-end monitoring across the web server and browser, including client-side performance.</span></span> <span data-ttu-id="37d42-157">Per ulteriori informazioni, vedere la [documentazione di Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="37d42-157">For more information, see the [Application Insights documentation](/azure/application-insights/app-insights-overview).</span></span>

## <a name="logging"></a><span data-ttu-id="37d42-158">Registrazione</span><span class="sxs-lookup"><span data-stu-id="37d42-158">Logging</span></span>

<span data-ttu-id="37d42-159">Web server and app logs are disabled by default in Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="37d42-159">Web server and app logs are disabled by default in Azure App Service.</span></span> <span data-ttu-id="37d42-160">Abilitare i registri con la seguente procedura:</span><span class="sxs-lookup"><span data-stu-id="37d42-160">Enable the logs with the following steps:</span></span>

1. <span data-ttu-id="37d42-161">Aprire il [portale di Azure](https://portal.azure.com)e passare al servizio App unique_number *\<\> mywebapp.*</span><span class="sxs-lookup"><span data-stu-id="37d42-161">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="37d42-162">Nel menu a sinistra, scorri verso il basso fino alla sezione **Monitoraggio.**</span><span class="sxs-lookup"><span data-stu-id="37d42-162">In the menu to the left, scroll down to the **Monitoring** section.</span></span> <span data-ttu-id="37d42-163">Selezionare **Registri di diagnostica**.</span><span class="sxs-lookup"><span data-stu-id="37d42-163">Select **Diagnostics logs**.</span></span>

    ![Collegamento registri di diagnostica](./media/monitoring/logging.png)

1. <span data-ttu-id="37d42-165">Attivare **Registrazione applicazioni (filesystem)**.</span><span class="sxs-lookup"><span data-stu-id="37d42-165">Turn on **Application Logging (Filesystem)**.</span></span> <span data-ttu-id="37d42-166">Se richiesto, fare clic sulla casella per installare le estensioni per abilitare la registrazione dell'app nell'app Web.</span><span class="sxs-lookup"><span data-stu-id="37d42-166">If prompted, click the box to install the extensions to enable app logging in the web app.</span></span>
1. <span data-ttu-id="37d42-167">Impostare **la registrazione del server Web su** File **system**.</span><span class="sxs-lookup"><span data-stu-id="37d42-167">Set **Web server logging** to **File System**.</span></span>
1. <span data-ttu-id="37d42-168">Immettere il **periodo di conservazione** in giorni.</span><span class="sxs-lookup"><span data-stu-id="37d42-168">Enter the **Retention Period** in days.</span></span> <span data-ttu-id="37d42-169">Ad esempio, 30.</span><span class="sxs-lookup"><span data-stu-id="37d42-169">For example, 30.</span></span>
1. <span data-ttu-id="37d42-170">Fare clic su **Salva**.</span><span class="sxs-lookup"><span data-stu-id="37d42-170">Click **Save**.</span></span>

<span data-ttu-id="37d42-171">ASP.NET i log di base e del server Web (servizio app) vengono generati per l'app Web.</span><span class="sxs-lookup"><span data-stu-id="37d42-171">ASP.NET Core and web server (App Service) logs are generated for the web app.</span></span> <span data-ttu-id="37d42-172">Possono essere scaricati utilizzando le informazioni FTP/FTPS visualizzate.</span><span class="sxs-lookup"><span data-stu-id="37d42-172">They can be downloaded using the FTP/FTPS information displayed.</span></span> <span data-ttu-id="37d42-173">La password è uguale alle credenziali di distribuzione create in precedenza in questa guida.</span><span class="sxs-lookup"><span data-stu-id="37d42-173">The password is the same as the deployment credentials created earlier in this guide.</span></span> <span data-ttu-id="37d42-174">I log possono essere [trasmessi direttamente al computer locale con PowerShell o l'interfaccia della](/azure/app-service/web-sites-enable-diagnostic-log#download)riga di comando di Azure.</span><span class="sxs-lookup"><span data-stu-id="37d42-174">The logs can be [streamed directly to your local machine with PowerShell or Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span></span> <span data-ttu-id="37d42-175">I log possono essere [visualizzati](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights)anche in Application Insights .</span><span class="sxs-lookup"><span data-stu-id="37d42-175">Logs can also be [viewed in Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span></span>

## <a name="log-streaming"></a><span data-ttu-id="37d42-176">Streaming dei log</span><span class="sxs-lookup"><span data-stu-id="37d42-176">Log streaming</span></span>

<span data-ttu-id="37d42-177">I log dell'app e del server Web possono essere trasmessi in tempo reale tramite il portale.</span><span class="sxs-lookup"><span data-stu-id="37d42-177">App and web server logs can be streamed in real time through the portal.</span></span>

1. <span data-ttu-id="37d42-178">Aprire il [portale di Azure](https://portal.azure.com)e passare al servizio App unique_number *\<\> mywebapp.*</span><span class="sxs-lookup"><span data-stu-id="37d42-178">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="37d42-179">Nel menu a sinistra, scorri verso il basso fino alla sezione **Monitoraggio** e seleziona **Registra flusso**.</span><span class="sxs-lookup"><span data-stu-id="37d42-179">In the menu to the left, scroll down to the **Monitoring** section and select **Log stream**.</span></span>

    ![Schermata che mostra il collegamento del flusso di log](./media/monitoring/log-stream.png)

<span data-ttu-id="37d42-181">I log possono anche essere [trasmessi tramite l'interfaccia della riga di comando di Azure o Azure PowerShell,](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs)anche tramite Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="37d42-181">Logs can also be [streamed via Azure CLI or Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), including through the Cloud Shell.</span></span>

## <a name="alerts"></a><span data-ttu-id="37d42-182">Avvisi</span><span class="sxs-lookup"><span data-stu-id="37d42-182">Alerts</span></span>

<span data-ttu-id="37d42-183">Monitoraggio azure fornisce anche avvisi in [tempo reale](/azure/monitoring-and-diagnostics/insights-alerts-portal) in base a metriche, eventi amministrativi e altri criteri.</span><span class="sxs-lookup"><span data-stu-id="37d42-183">Azure Monitor also provides [real time alerts](/azure/monitoring-and-diagnostics/insights-alerts-portal) based on metrics, administrative events, and other criteria.</span></span>

> <span data-ttu-id="37d42-184">*Nota: attualmente gli avvisi sulle metriche delle app Web sono disponibili solo nel servizio Avvisi (classico).*</span><span class="sxs-lookup"><span data-stu-id="37d42-184">*Note: Currently alerting on web app metrics is only available in the Alerts (classic) service.*</span></span>

<span data-ttu-id="37d42-185">Il [servizio Avvisi (classico)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) è disponibile in Monitoraggio di Azure o nella sezione **Monitoraggio** delle impostazioni del servizio app.</span><span class="sxs-lookup"><span data-stu-id="37d42-185">The [Alerts (classic) service](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) can be found in Azure Monitor or under the **Monitoring** section of the App Service settings.</span></span>

![Collegamento Avvisi (classico)](./media/monitoring/alerts.png)

## <a name="live-debugging"></a><span data-ttu-id="37d42-187">Debug in attivo</span><span class="sxs-lookup"><span data-stu-id="37d42-187">Live debugging</span></span>

<span data-ttu-id="37d42-188">Il servizio app di Azure può essere [sottoposto a debug in remoto con Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) quando i log non forniscono informazioni sufficienti.</span><span class="sxs-lookup"><span data-stu-id="37d42-188">Azure App Service can be [debugged remotely with Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) when logs don't provide enough information.</span></span> <span data-ttu-id="37d42-189">Tuttavia, il debug remoto richiede che l'app venga compilata con simboli di debug.</span><span class="sxs-lookup"><span data-stu-id="37d42-189">However, remote debugging requires the app to be compiled with debug symbols.</span></span> <span data-ttu-id="37d42-190">Il debug non deve essere eseguito nell'ambiente di produzione, ad eccezione di ultima risorsa.</span><span class="sxs-lookup"><span data-stu-id="37d42-190">Debugging shouldn't be done in production, except as a last resort.</span></span>

## <a name="conclusion"></a><span data-ttu-id="37d42-191">Conclusioni</span><span class="sxs-lookup"><span data-stu-id="37d42-191">Conclusion</span></span>

<span data-ttu-id="37d42-192">In questa sezione sono state completate le attività seguenti:In this section, you completed the following tasks:</span><span class="sxs-lookup"><span data-stu-id="37d42-192">In this section, you completed the following tasks:</span></span>

* <span data-ttu-id="37d42-193">Trovare i dati di base di monitoraggio e risoluzione dei problemi nel portale di AzureFind basic monitoring and troubleshooting data in the Azure portal</span><span class="sxs-lookup"><span data-stu-id="37d42-193">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="37d42-194">Informazioni su come Monitoraggio di Azure offre uno sguardo più approfondito alle metriche in tutti i servizi di AzureLearn how Azure Monitor provides a deeper look at metrics across all Azure services</span><span class="sxs-lookup"><span data-stu-id="37d42-194">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="37d42-195">Connettere l'app Web con Application Insights per la profilatura dell'appConnect the web app with Application Insights for app profiling</span><span class="sxs-lookup"><span data-stu-id="37d42-195">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="37d42-196">Attivare la registrazione e scoprire dove scaricare i log</span><span class="sxs-lookup"><span data-stu-id="37d42-196">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="37d42-197">Streaming dei log in tempo reale</span><span class="sxs-lookup"><span data-stu-id="37d42-197">Stream logs in real time</span></span>
* <span data-ttu-id="37d42-198">Scopri dove configurare gli avvisi</span><span class="sxs-lookup"><span data-stu-id="37d42-198">Learn where to set up alerts</span></span>
* <span data-ttu-id="37d42-199">Informazioni sul debug remoto delle app Web del servizio app di Azure.Learn about remote debugging Azure App web apps.</span><span class="sxs-lookup"><span data-stu-id="37d42-199">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="37d42-200">Informazioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="37d42-200">Additional reading</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="37d42-201">Monitoraggio delle prestazioni dell'applicazione web di Azure</span><span class="sxs-lookup"><span data-stu-id="37d42-201">Monitor Azure web app performance with Application Insights</span></span>](/azure/application-insights/app-insights-azure-web-apps)
* [<span data-ttu-id="37d42-202">Abilitare la registrazione diagnostica per le app Web nel servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="37d42-202">Enable diagnostics logging for web apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)
* [<span data-ttu-id="37d42-203">Risoluzione dei problemi di un'app Web nel servizio app di Azure tramite Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37d42-203">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="37d42-204">Creare avvisi sulle metriche classici in Monitoraggio di Azure per i servizi di Azure: portale di Azure</span><span class="sxs-lookup"><span data-stu-id="37d42-204">Create classic metric alerts in Azure Monitor for Azure services - Azure portal</span></span>](/azure/monitoring-and-diagnostics/insights-alerts-portal)
