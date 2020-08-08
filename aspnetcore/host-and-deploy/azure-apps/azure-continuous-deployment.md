---
title: Distribuzione continua in Azure con Visual Studio e Git con ASP.NET Core
author: rick-anderson
description: Informazioni su come creare un'app Web ASP.NET Core tramite Visual Studio e distribuirla nel Servizio app di Azure usando Git per la distribuzione continua.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: af983a2bc8a2802f9be07b30ab733ed2cfb7933c
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016089"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a><span data-ttu-id="7d516-103">Distribuzione continua in Azure con Visual Studio e Git con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7d516-103">Continuous deployment to Azure with Visual Studio and Git with ASP.NET Core</span></span>

<span data-ttu-id="7d516-104">Di [Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="7d516-104">By [Erik Reitan](https://github.com/Erikre)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="7d516-105">Questa esercitazione mostra come creare un'app Web ASP.NET Core tramite Visual Studio e distribuirla da Visual Studio nel Servizio app di Azure, usando Git la distribuzione continua.</span><span class="sxs-lookup"><span data-stu-id="7d516-105">This tutorial shows how to create an ASP.NET Core web app using Visual Studio and deploy it from Visual Studio to Azure App Service using continuous deployment.</span></span>

<span data-ttu-id="7d516-106">Vedere anche l'articolo [Creare la prima pipeline con Azure Pipelines](/azure/devops/pipelines/get-started-yaml), che illustra come configurare un flusso di lavoro di recapito continuo per il [Servizio app di Azure](/azure/app-service/app-service-web-overview) usando Azure DevOps Services.</span><span class="sxs-lookup"><span data-stu-id="7d516-106">See also [Create your first pipeline with Azure Pipelines](/azure/devops/pipelines/get-started-yaml), which shows how to configure a continuous delivery (CD) workflow for [Azure App Service](/azure/app-service/app-service-web-overview) using Azure DevOps Services.</span></span> <span data-ttu-id="7d516-107">Azure Pipelines (un servizio di Azure DevOps Services) semplifica la configurazione di una pipeline di distribuzione solida per pubblicare gli aggiornamenti per le app ospitate nel Servizio app di Azure.</span><span class="sxs-lookup"><span data-stu-id="7d516-107">Azure Pipelines (an Azure DevOps Services service) simplifies setting up a robust deployment pipeline to publish updates for apps hosted in Azure App Service.</span></span> <span data-ttu-id="7d516-108">È possibile configurare la pipeline dal portale di Azure per compilare, eseguire i test, distribuire in uno slot di staging e quindi distribuire nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="7d516-108">The pipeline can be configured from the Azure portal to build, run tests, deploy to a staging slot, and then deploy to production.</span></span>

> [!NOTE]
> <span data-ttu-id="7d516-109">Per completare questa esercitazione, è necessario un account di Microsoft Azure.</span><span class="sxs-lookup"><span data-stu-id="7d516-109">To complete this tutorial, a Microsoft Azure account is required.</span></span> <span data-ttu-id="7d516-110">Per ottenere un account, [attivare i benefici per il sottoscrittore MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) o [iscriversi per una versione di valutazione gratuita](https://azure.microsoft.com/free/dotnet/?WT.mc_id=A261C142F).</span><span class="sxs-lookup"><span data-stu-id="7d516-110">To obtain an account, [activate MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) or [sign up for a free trial](https://azure.microsoft.com/free/dotnet/?WT.mc_id=A261C142F).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7d516-111">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="7d516-111">Prerequisites</span></span>

<span data-ttu-id="7d516-112">In questa esercitazione si presuppone che sia stato installato il software seguente:</span><span class="sxs-lookup"><span data-stu-id="7d516-112">This tutorial assumes the following software is installed:</span></span>

* [<span data-ttu-id="7d516-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d516-113">Visual Studio</span></span>](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* <span data-ttu-id="7d516-114">[Git](https://git-scm.com/downloads) per Windows</span><span class="sxs-lookup"><span data-stu-id="7d516-114">[Git](https://git-scm.com/downloads) for Windows</span></span>

## <a name="create-an-aspnet-core-web-app"></a><span data-ttu-id="7d516-115">Creare un'app Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7d516-115">Create an ASP.NET Core web app</span></span>

1. <span data-ttu-id="7d516-116">Avviare Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7d516-116">Start Visual Studio.</span></span>

1. <span data-ttu-id="7d516-117">Scegliere **Nuovo** > **Progetto** dal menu **File**.</span><span class="sxs-lookup"><span data-stu-id="7d516-117">From the **File** menu, select **New** > **Project**.</span></span>

1. <span data-ttu-id="7d516-118">Selezionare il modello di progetto **Applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="7d516-118">Select the **ASP.NET Core Web Application** project template.</span></span> <span data-ttu-id="7d516-119">Viene visualizzato in **Installed**  >  **modelli**installati  >  **Visual C#**  >  **.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="7d516-119">It appears under **Installed** > **Templates** > **Visual C#** > **.NET Core**.</span></span> <span data-ttu-id="7d516-120">Assegnare al progetto il nome `SampleWebAppDemo`.</span><span class="sxs-lookup"><span data-stu-id="7d516-120">Name the project `SampleWebAppDemo`.</span></span> <span data-ttu-id="7d516-121">Selezionare l'opzione **Crea nuovo repository Git** e fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="7d516-121">Select the **Create new Git repository** option and click **OK**.</span></span>

   ![Finestra di dialogo Nuovo progetto](azure-continuous-deployment/_static/01-new-project.png)

1. <span data-ttu-id="7d516-123">Nella finestra di dialogo **Nuovo progetto ASP.NET Core** selezionare il modello ASP.NET Core **Vuoto** e quindi fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="7d516-123">In the **New ASP.NET Core Project** dialog, select the ASP.NET Core **Empty** template, then click **OK**.</span></span>

   ![Finestra di dialogo Nuovo progetto ASP.NET Core](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> <span data-ttu-id="7d516-125">La versione più recente di .NET Core è la 2.0.</span><span class="sxs-lookup"><span data-stu-id="7d516-125">The most recent release of .NET Core is 2.0.</span></span>

### <a name="running-the-web-app-locally"></a><span data-ttu-id="7d516-126">Esecuzione dell'applicazione Web in locale</span><span class="sxs-lookup"><span data-stu-id="7d516-126">Running the web app locally</span></span>

1. <span data-ttu-id="7d516-127">Al termine della creazione dell'app da parte di Visual Studio, eseguire l'app selezionando **debug**  >  **Avvia debug**.</span><span class="sxs-lookup"><span data-stu-id="7d516-127">Once Visual Studio finishes creating the app, run the app by selecting **Debug** > **Start Debugging**.</span></span> <span data-ttu-id="7d516-128">In alternativa, premere **F5**.</span><span class="sxs-lookup"><span data-stu-id="7d516-128">As an alternative, press **F5**.</span></span>

   <span data-ttu-id="7d516-129">L'inizializzazione di Visual Studio e della nuova app potrebbe richiedere tempo.</span><span class="sxs-lookup"><span data-stu-id="7d516-129">It may take time to initialize Visual Studio and the new app.</span></span> <span data-ttu-id="7d516-130">Una volta completata, il browser visualizzerà l'app in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="7d516-130">Once it's complete, the browser shows the running app.</span></span>

   ![La finestra del browser mostra l'applicazione in esecuzione che visualizza "Hello World!"](azure-continuous-deployment/_static/04-browser-runapp.png)

1. <span data-ttu-id="7d516-132">Dopo aver revisionato l'app Web in esecuzione, chiudere il browser e selezionare l'icona "Interrompi l'esecuzione del debug" nella barra degli strumenti di Visual Studio per arrestare l'app.</span><span class="sxs-lookup"><span data-stu-id="7d516-132">After reviewing the running Web app, close the browser and select the "Stop Debugging" icon in the toolbar of Visual Studio to stop the app.</span></span>

## <a name="create-a-web-app-in-the-azure-portal"></a><span data-ttu-id="7d516-133">Creazione di un'app Web nel portale di Azure ##</span><span class="sxs-lookup"><span data-stu-id="7d516-133">Create a web app in the Azure Portal</span></span>

<span data-ttu-id="7d516-134">Eseguire i passaggi seguenti per creare un'app Web nel portale di Azure:</span><span class="sxs-lookup"><span data-stu-id="7d516-134">The following steps create a web app in the Azure Portal:</span></span>

1. <span data-ttu-id="7d516-135">Accedere al portale di [Azure](https://portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="7d516-135">Log in to the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="7d516-136">Selezionare **NUOVO** nella parte superiore sinistra dell'interfaccia del portale.</span><span class="sxs-lookup"><span data-stu-id="7d516-136">Select **NEW** at the top left of the portal interface.</span></span>

1. <span data-ttu-id="7d516-137">Selezionare **Web e dispositivi mobili**  >  **app Web**.</span><span class="sxs-lookup"><span data-stu-id="7d516-137">Select **Web + Mobile** > **Web App**.</span></span>

   ![Portale di Microsoft Azure: pulsante Nuovo: Web + Dispositivi mobili in Marketplace: pulsante Web App in App in primo piano](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. <span data-ttu-id="7d516-139">Nel pannello **App Web** immettere un valore univoco per il **Nome del servizio App**.</span><span class="sxs-lookup"><span data-stu-id="7d516-139">In the **Web App** blade, enter a unique value for the **App Service Name**.</span></span>

   ![Pannello App Web](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > <span data-ttu-id="7d516-141">Il nome **Nome del servizio app** deve essere univoco.</span><span class="sxs-lookup"><span data-stu-id="7d516-141">The **App Service Name** name must be unique.</span></span> <span data-ttu-id="7d516-142">Il portale applica questa regola quando viene specificato il nome.</span><span class="sxs-lookup"><span data-stu-id="7d516-142">The portal enforces this rule when the name is provided.</span></span> <span data-ttu-id="7d516-143">Se si specifica un valore diverso, sostituire tale valore per ogni occorrenza di **SampleWebAppDemo** in questa esercitazione.</span><span class="sxs-lookup"><span data-stu-id="7d516-143">If providing a different value, substitute that value for each occurrence of **SampleWebAppDemo** in this tutorial.</span></span>

   <span data-ttu-id="7d516-144">Anche nel pannello **App Web**, selezionare una **Posizione/piano di servizio App** o crearne uno nuovo.</span><span class="sxs-lookup"><span data-stu-id="7d516-144">Also in the **Web App** blade, select an existing **App Service Plan/Location** or create a new one.</span></span> <span data-ttu-id="7d516-145">Se si crea un nuovo piano, selezionare il piano tariffario, la posizione e altre opzioni.</span><span class="sxs-lookup"><span data-stu-id="7d516-145">If creating a new plan, select the pricing tier, location, and other options.</span></span> <span data-ttu-id="7d516-146">Per altre informazioni sui piani di Servizio app, vedere [Panoramica di approfondimento dei piani del Servizio App di Azure](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span><span class="sxs-lookup"><span data-stu-id="7d516-146">For more information on App Service plans, see [Azure App Service plans in-depth overview](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span></span>

1. <span data-ttu-id="7d516-147">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="7d516-147">Select **Create**.</span></span> <span data-ttu-id="7d516-148">Azure eseguirà il provisioning e avvierà l'app Web.</span><span class="sxs-lookup"><span data-stu-id="7d516-148">Azure will provision and start the web app.</span></span>

   ![Portale di Azure: campione di pannello di Essentials Demo 01 di App Web](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a><span data-ttu-id="7d516-150">Abilitare la pubblicazione Git per la nuova app Web</span><span class="sxs-lookup"><span data-stu-id="7d516-150">Enable Git publishing for the new web app</span></span>

<span data-ttu-id="7d516-151">Git è un sistema di controllo della versione distribuito che è possibile usare per distribuire un'app Web di Servizio app di Azure.</span><span class="sxs-lookup"><span data-stu-id="7d516-151">Git is a distributed version control system that can be used to deploy an Azure App Service web app.</span></span> <span data-ttu-id="7d516-152">Il codice dell'app Web è archiviato in un repository Git locale e viene distribuito in Azure eseguendone il push in un repository remoto.</span><span class="sxs-lookup"><span data-stu-id="7d516-152">Web app code is stored in a local Git repository, and the code is deployed to Azure by pushing to a remote repository.</span></span>

1. <span data-ttu-id="7d516-153">Accedere al [portale di Azure](https://portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="7d516-153">Log into the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="7d516-154">Selezionare **Servizi app** per visualizzare un elenco dei servizi app associati alla sottoscrizione di Azure.</span><span class="sxs-lookup"><span data-stu-id="7d516-154">Select **App Services** to view a list of the app services associated with the Azure subscription.</span></span>

1. <span data-ttu-id="7d516-155">Selezionare l'app Web che è stata creata nella sezione precedente di questa esercitazione.</span><span class="sxs-lookup"><span data-stu-id="7d516-155">Select the web app created in the previous section of this tutorial.</span></span>

1. <span data-ttu-id="7d516-156">Nel pannello **Distribuzione** selezionare **Opzioni di distribuzione** > **Scegli origine** > **Repository Git locale**.</span><span class="sxs-lookup"><span data-stu-id="7d516-156">In the **Deployment** blade, select **Deployment options** > **Choose Source** > **Local Git Repository**.</span></span>

   ![Pannello delle impostazioni: pannello di origine della distribuzione: scegliere Pannello di origine](azure-continuous-deployment/_static/deployment-options.png)

1. <span data-ttu-id="7d516-158">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="7d516-158">Select **OK**.</span></span>

1. <span data-ttu-id="7d516-159">Se le credenziali di distribuzione per la pubblicazione di un'app Web o di altri servizi app Web non sono state precedentemente configurate, impostarle ora:</span><span class="sxs-lookup"><span data-stu-id="7d516-159">If deployment credentials for publishing a web app or other App Service app haven't previously been set up, set them up now:</span></span>

   * <span data-ttu-id="7d516-160">Selezionare **Impostazioni**  >  **credenziali**per la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="7d516-160">Select **Settings** > **Deployment credentials**.</span></span> <span data-ttu-id="7d516-161">Verrà visualizzato il pannello **Imposta credenziali di distribuzione**.</span><span class="sxs-lookup"><span data-stu-id="7d516-161">The **Set deployment credentials** blade is displayed.</span></span>
   * <span data-ttu-id="7d516-162">Creare un nome utente e una password.</span><span class="sxs-lookup"><span data-stu-id="7d516-162">Create a user name and password.</span></span> <span data-ttu-id="7d516-163">Salvare la password per un uso successivo durante la configurazione di Git.</span><span class="sxs-lookup"><span data-stu-id="7d516-163">Save the password for later use when setting up Git.</span></span>
   * <span data-ttu-id="7d516-164">Selezionare **Salva**.</span><span class="sxs-lookup"><span data-stu-id="7d516-164">Select **Save**.</span></span>

1. <span data-ttu-id="7d516-165">Nel pannello **App Web** selezionare **Impostazioni** > **Proprietà**.</span><span class="sxs-lookup"><span data-stu-id="7d516-165">In the **Web App** blade, select **Settings** > **Properties**.</span></span> <span data-ttu-id="7d516-166">L'URL del repository Git remoto che verrà distribuito è visualizzato in **URL GIT**.</span><span class="sxs-lookup"><span data-stu-id="7d516-166">The URL of the remote Git repository to deploy to is shown under **GIT URL**.</span></span>

1. <span data-ttu-id="7d516-167">Copiare il valore dell'opzione **URL GIT** , che sarà necessario più avanti nell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="7d516-167">Copy the **GIT URL** value for later use in the tutorial.</span></span>

   ![Portale di Azure: pannello Proprietà dell'applicazione](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a><span data-ttu-id="7d516-169">Pubblicare l'app Web nel servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="7d516-169">Publish the web app to Azure App Service</span></span>

<span data-ttu-id="7d516-170">In questa sezione si creerà un repository Git locale tramite Visual Studio e si eseguirà il push da tale repository in Azure per distribuire l'app Web.</span><span class="sxs-lookup"><span data-stu-id="7d516-170">In this section, create a local Git repository using Visual Studio and push from that repository to Azure to deploy the web app.</span></span> <span data-ttu-id="7d516-171">La procedura coinvolta include quanto segue:</span><span class="sxs-lookup"><span data-stu-id="7d516-171">The steps involved include the following:</span></span>

* <span data-ttu-id="7d516-172">Aggiungere l'impostazione del repository remoto tramite il valore di URL GIT, in modo da poter distribuire il repository locale in Azure.</span><span class="sxs-lookup"><span data-stu-id="7d516-172">Add the remote repository setting using the GIT URL value, so the local repository can be deployed to Azure.</span></span>
* <span data-ttu-id="7d516-173">Eseguire il commit delle modifiche del progetto.</span><span class="sxs-lookup"><span data-stu-id="7d516-173">Commit project changes.</span></span>
* <span data-ttu-id="7d516-174">Effettuare il push delle modifiche di progetto dal repository locale al repository remoto in Azure.</span><span class="sxs-lookup"><span data-stu-id="7d516-174">Push project changes from the local repository to the remote repository on Azure.</span></span>

1. <span data-ttu-id="7d516-175">In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **soluzione ' SampleWebAppDemo '** e selezionare **commit**.</span><span class="sxs-lookup"><span data-stu-id="7d516-175">In **Solution Explorer** right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="7d516-176">Viene visualizzato **Team Explorer**.</span><span class="sxs-lookup"><span data-stu-id="7d516-176">The **Team Explorer** is displayed.</span></span>

   ![Scheda di connessione Team Explorer](azure-continuous-deployment/_static/10-team-explorer.png)

1. <span data-ttu-id="7d516-178">In **Team Explorer** selezionare la **Home** (icona home) > **Impostazioni** > **Impostazioni del Repository**.</span><span class="sxs-lookup"><span data-stu-id="7d516-178">In **Team Explorer**, select the **Home** (home icon) > **Settings** > **Repository Settings**.</span></span>

1. <span data-ttu-id="7d516-179">Nella sezione **Elementi remoti** di **Impostazioni repository** selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="7d516-179">In the **Remotes** section of the **Repository Settings**, select **Add**.</span></span> <span data-ttu-id="7d516-180">Viene visualizzata la finestra di dialogo **Aggiungi elemento remoto**.</span><span class="sxs-lookup"><span data-stu-id="7d516-180">The **Add Remote** dialog box is displayed.</span></span>

1. <span data-ttu-id="7d516-181">Impostare il **Nome** del repository remoto da **Azure SampleApp**.</span><span class="sxs-lookup"><span data-stu-id="7d516-181">Set the **Name** of the remote to **Azure-SampleApp**.</span></span>

1. <span data-ttu-id="7d516-182">Impostare il valore per **Recupero** sull'**URL Git** precedentemente copiato da Azure in questa esercitazione.</span><span class="sxs-lookup"><span data-stu-id="7d516-182">Set the value for **Fetch** to the **Git URL** that copied from Azure earlier in this tutorial.</span></span> <span data-ttu-id="7d516-183">Si noti che questo è l'URL che termina con **.git**.</span><span class="sxs-lookup"><span data-stu-id="7d516-183">Note that this is the URL that ends with **.git**.</span></span>

   ![Modifica finestra di dialogo remota](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > <span data-ttu-id="7d516-185">In alternativa, specificare il repository remoto dalla **Finestra di comando** aprendo la **Finestra di comando**, passando alla directory del progetto e immettendo il comando.</span><span class="sxs-lookup"><span data-stu-id="7d516-185">As an alternative, specify the remote repository from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the command.</span></span> <span data-ttu-id="7d516-186">Esempio:</span><span class="sxs-lookup"><span data-stu-id="7d516-186">Example:</span></span>
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. <span data-ttu-id="7d516-187">Selezionare la **Home** (icona home) > **Impostazioni** > **Impostazioni globali**.</span><span class="sxs-lookup"><span data-stu-id="7d516-187">Select the **Home** (home icon) > **Settings** > **Global Settings**.</span></span> <span data-ttu-id="7d516-188">Assicurarsi che il nome e l'indirizzo di posta elettronica siano impostati.</span><span class="sxs-lookup"><span data-stu-id="7d516-188">Confirm that the name and email address are set.</span></span> <span data-ttu-id="7d516-189">Selezionare **Aggiorna**, se necessario.</span><span class="sxs-lookup"><span data-stu-id="7d516-189">Select **Update** if required.</span></span>

1. <span data-ttu-id="7d516-190">Selezionare **Home**  >  **modifiche** per tornare alla visualizzazione **modifiche** .</span><span class="sxs-lookup"><span data-stu-id="7d516-190">Select **Home** > **Changes** to return to the **Changes** view.</span></span>

1. <span data-ttu-id="7d516-191">Immettere un messaggio di commit, ad esempio **Push iniziale #1** e selezionare **Commit**.</span><span class="sxs-lookup"><span data-stu-id="7d516-191">Enter a commit message, such as **Initial Push #1** and select **Commit**.</span></span> <span data-ttu-id="7d516-192">Questa azione consente di creare un *commit* a livello locale.</span><span class="sxs-lookup"><span data-stu-id="7d516-192">This action creates a *commit* locally.</span></span>

   ![Scheda di connessione Team Explorer](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > <span data-ttu-id="7d516-194">In alternativa, eseguire il commit delle modifiche dalla **Finestra di comando** aprendo la **Finestra di comando**, passando alla directory del progetto e immettendo i comandi Git.</span><span class="sxs-lookup"><span data-stu-id="7d516-194">As an alternative, commit changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the git commands.</span></span> <span data-ttu-id="7d516-195">Esempio:</span><span class="sxs-lookup"><span data-stu-id="7d516-195">Example:</span></span>
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. <span data-ttu-id="7d516-196">Selezionare **Home**  >  **Sync**  >  **Actions**  >  **Apri prompt dei comandi**.</span><span class="sxs-lookup"><span data-stu-id="7d516-196">Select **Home** > **Sync** > **Actions** > **Open Command Prompt**.</span></span> <span data-ttu-id="7d516-197">Verrà aperto il prompt dei comandi nella directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="7d516-197">The command prompt opens to the project directory.</span></span>

1. <span data-ttu-id="7d516-198">Immettere il comando seguente nella finestra Comando:</span><span class="sxs-lookup"><span data-stu-id="7d516-198">Enter the following command in the command window:</span></span>

   `git push -u Azure-SampleApp master`

1. <span data-ttu-id="7d516-199">Immettere la password delle **credenziali di distribuzione** di Azure creata precedentemente in Azure.</span><span class="sxs-lookup"><span data-stu-id="7d516-199">Enter the Azure **deployment credentials** password created earlier in Azure.</span></span>

   <span data-ttu-id="7d516-200">Questo comando avvia il processo di esecuzione del push dei file di progetto locali in Azure.</span><span class="sxs-lookup"><span data-stu-id="7d516-200">This command starts the process of pushing the local project files to Azure.</span></span> <span data-ttu-id="7d516-201">L'output del comando precedente termina con un messaggio che indica che la distribuzione ha avuto esito positivo.</span><span class="sxs-lookup"><span data-stu-id="7d516-201">The output from the above command ends with a message that the deployment was successful.</span></span>

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > <span data-ttu-id="7d516-202">Se è necessaria la collaborazione sul progetto, valutare se eseguire il push in [GitHub](https://github.com) prima del push in Azure.</span><span class="sxs-lookup"><span data-stu-id="7d516-202">If collaboration on the project is required, consider pushing to [GitHub](https://github.com) before pushing to Azure.</span></span>
 
### <a name="verify-the-active-deployment"></a><span data-ttu-id="7d516-203">Verificare la distribuzione attiva</span><span class="sxs-lookup"><span data-stu-id="7d516-203">Verify the Active Deployment</span></span>

<span data-ttu-id="7d516-204">Verificare che il trasferimento dell'app Web dall'ambiente locale in Azure abbia esito positivo.</span><span class="sxs-lookup"><span data-stu-id="7d516-204">Verify that the web app transfer from the local environment to Azure is successful.</span></span>

<span data-ttu-id="7d516-205">Nel [portale di Azure](https://portal.azure.com) selezionare l'app Web.</span><span class="sxs-lookup"><span data-stu-id="7d516-205">In the [Azure Portal](https://portal.azure.com), select the web app.</span></span> <span data-ttu-id="7d516-206">Selezionare le opzioni di distribuzione della **distribuzione**  >  **Deployment options**.</span><span class="sxs-lookup"><span data-stu-id="7d516-206">Select **Deployment** > **Deployment options**.</span></span>

![Portale di Azure: pannello impostazioni: pannello di distribuzione che mostra la distribuzione con esito positivo](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a><span data-ttu-id="7d516-208">Eseguire l'app in Azure</span><span class="sxs-lookup"><span data-stu-id="7d516-208">Run the app in Azure</span></span>

<span data-ttu-id="7d516-209">Ora che l'app Web è distribuita in Azure, eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="7d516-209">Now that the web app is deployed to Azure, run the app.</span></span>

<span data-ttu-id="7d516-210">Questa operazione può essere eseguita in due modi:</span><span class="sxs-lookup"><span data-stu-id="7d516-210">This can be accomplished in two ways:</span></span>

* <span data-ttu-id="7d516-211">Nel portale di Azure individuare il pannello dell'app Web per l'app Web.</span><span class="sxs-lookup"><span data-stu-id="7d516-211">In the Azure Portal, locate the web app blade for the web app.</span></span> <span data-ttu-id="7d516-212">Selezionare **Sfoglia** per visualizzare l'app nel browser predefinito.</span><span class="sxs-lookup"><span data-stu-id="7d516-212">Select **Browse** to view the app in the default browser.</span></span>
* <span data-ttu-id="7d516-213">Aprire un browser e immettere l'URL per l'app Web.</span><span class="sxs-lookup"><span data-stu-id="7d516-213">Open a browser and enter the URL for the web app.</span></span> <span data-ttu-id="7d516-214">Esempio: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="7d516-214">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="update-the-web-app-and-republish"></a><span data-ttu-id="7d516-215">Aggiornare l'app Web e ripetere la pubblicazione</span><span class="sxs-lookup"><span data-stu-id="7d516-215">Update the web app and republish</span></span>

<span data-ttu-id="7d516-216">Dopo aver apportato modifiche al codice locale, ripetere la pubblicazione:</span><span class="sxs-lookup"><span data-stu-id="7d516-216">After making changes to the local code, republish:</span></span>

1. <span data-ttu-id="7d516-217">In **Esplora soluzioni** di Visual Studio aprire il file *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="7d516-217">In **Solution Explorer** of Visual Studio, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="7d516-218">Nel metodo `Configure` modificare il metodo `Response.WriteAsync` in modo che venga visualizzato nel seguente modo:</span><span class="sxs-lookup"><span data-stu-id="7d516-218">In the `Configure` method, modify the `Response.WriteAsync` method so that it appears as follows:</span></span>

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. <span data-ttu-id="7d516-219">Salvare le modifiche a *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="7d516-219">Save the changes to *Startup.cs*.</span></span>

1. <span data-ttu-id="7d516-220">In **Esplora soluzioni** fare clic con il tasto destro del mouse su **Soluzione "SampleWebAppDemo"** e selezionare **Commit**.</span><span class="sxs-lookup"><span data-stu-id="7d516-220">In **Solution Explorer**, right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="7d516-221">Viene visualizzato **Team Explorer**.</span><span class="sxs-lookup"><span data-stu-id="7d516-221">The **Team Explorer** is displayed.</span></span>

1. <span data-ttu-id="7d516-222">Immettere un messaggio per il commit, ad esempio `Update #2`.</span><span class="sxs-lookup"><span data-stu-id="7d516-222">Enter a commit message, such as `Update #2`.</span></span>

1. <span data-ttu-id="7d516-223">Premere il pulsante **Commit** per salvare le modifiche del progetto.</span><span class="sxs-lookup"><span data-stu-id="7d516-223">Press the **Commit** button to commit the project changes.</span></span>

1. <span data-ttu-id="7d516-224">Selezionare **Home**  >  **Sync**  >  **Actions**  >  **push**.</span><span class="sxs-lookup"><span data-stu-id="7d516-224">Select **Home** > **Sync** > **Actions** > **Push**.</span></span>

> [!NOTE]
> <span data-ttu-id="7d516-225">In alternativa, eseguire il push delle modifiche dalla **Finestra di comando** aprendo la **Finestra di comando**, passando alla directory del progetto e immettendo un comando Git.</span><span class="sxs-lookup"><span data-stu-id="7d516-225">As an alternative, push the changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering a git command.</span></span> <span data-ttu-id="7d516-226">Esempio:</span><span class="sxs-lookup"><span data-stu-id="7d516-226">Example:</span></span>
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a><span data-ttu-id="7d516-227">Visualizzare l'app web aggiornata in Azure</span><span class="sxs-lookup"><span data-stu-id="7d516-227">View the updated web app in Azure</span></span>

<span data-ttu-id="7d516-228">Visualizzare l'app Web aggiornata selezionando **Sfoglia** dal pannello dell'app Web nel portale di Azure o aprendo un browser e immettendo l'URL per l'app Web.</span><span class="sxs-lookup"><span data-stu-id="7d516-228">View the updated web app by selecting **Browse** from the web app blade in the Azure Portal or by opening a browser and entering the URL for the web app.</span></span> <span data-ttu-id="7d516-229">Esempio: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="7d516-229">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7d516-230">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="7d516-230">Additional resources</span></span>

* [<span data-ttu-id="7d516-231">Creare la prima pipeline con Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="7d516-231">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="7d516-232">Progetto Kudu</span><span class="sxs-lookup"><span data-stu-id="7d516-232">Project Kudu</span></span>](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
