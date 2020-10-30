---
title: 'Integrazione e distribuzione continue: DevOps con ASP.NET Core e Azure'
author: CamSoper
description: Integrazione e distribuzione continue in DevOps con ASP.NET Core e Azure
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: devx-track-csharp, mvc, seodec18
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: azure/devops/cicd
ms.openlocfilehash: eddd7034bf1860fb35cf00eefb7a11a408869700
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052643"
---
# <a name="continuous-integration-and-deployment"></a><span data-ttu-id="a7ff0-103">Integrazione e distribuzione continua</span><span class="sxs-lookup"><span data-stu-id="a7ff0-103">Continuous integration and deployment</span></span>

<span data-ttu-id="a7ff0-104">Nel capitolo precedente è stato creato un repository git locale per la semplice app Reader di feed.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-104">In the previous chapter, you created a local Git repository for the Simple Feed Reader app.</span></span> <span data-ttu-id="a7ff0-105">In questo capitolo si pubblicherà il codice in un repository GitHub e si creerà una pipeline Azure DevOps Services usando Azure Pipelines.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-105">In this chapter, you'll publish that code to a GitHub repository and construct an Azure DevOps Services pipeline using Azure Pipelines.</span></span> <span data-ttu-id="a7ff0-106">La pipeline consente le compilazioni continue e le distribuzioni dell'app.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-106">The pipeline enables continuous builds and deployments of the app.</span></span> <span data-ttu-id="a7ff0-107">Qualsiasi commit nel repository GitHub attiva una compilazione e una distribuzione nello slot di staging dell'app Web di Azure.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-107">Any commit to the GitHub repository triggers a build and a deployment to the Azure Web App's staging slot.</span></span>

<span data-ttu-id="a7ff0-108">In questa sezione verranno completate le attività seguenti:</span><span class="sxs-lookup"><span data-stu-id="a7ff0-108">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="a7ff0-109">Pubblicare il codice dell'app in GitHub</span><span class="sxs-lookup"><span data-stu-id="a7ff0-109">Publish the app's code to GitHub</span></span>
* <span data-ttu-id="a7ff0-110">Disconnettere la distribuzione git locale</span><span class="sxs-lookup"><span data-stu-id="a7ff0-110">Disconnect local Git deployment</span></span>
* <span data-ttu-id="a7ff0-111">Creare un'organizzazione Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="a7ff0-111">Create an Azure DevOps organization</span></span>
* <span data-ttu-id="a7ff0-112">Creare un progetto team in Azure DevOps Services</span><span class="sxs-lookup"><span data-stu-id="a7ff0-112">Create a team project in Azure DevOps Services</span></span>
* <span data-ttu-id="a7ff0-113">Creare una definizione di compilazione</span><span class="sxs-lookup"><span data-stu-id="a7ff0-113">Create a build definition</span></span>
* <span data-ttu-id="a7ff0-114">Creare una pipeline di versione</span><span class="sxs-lookup"><span data-stu-id="a7ff0-114">Create a release pipeline</span></span>
* <span data-ttu-id="a7ff0-115">Eseguire il commit delle modifiche in GitHub e la distribuzione automatica in Azure</span><span class="sxs-lookup"><span data-stu-id="a7ff0-115">Commit changes to GitHub and automatically deploy to Azure</span></span>
* <span data-ttu-id="a7ff0-116">Esaminare la pipeline Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="a7ff0-116">Examine the Azure Pipelines pipeline</span></span>

## <a name="publish-the-apps-code-to-github"></a><span data-ttu-id="a7ff0-117">Pubblicare il codice dell'app in GitHub</span><span class="sxs-lookup"><span data-stu-id="a7ff0-117">Publish the app's code to GitHub</span></span>

1. <span data-ttu-id="a7ff0-118">Aprire una finestra del browser e passare a `https://github.com` .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-118">Open a browser window, and navigate to `https://github.com`.</span></span>
1. <span data-ttu-id="a7ff0-119">Fare clic sull' **+** elenco a discesa nell'intestazione e selezionare **nuovo repository** :</span><span class="sxs-lookup"><span data-stu-id="a7ff0-119">Click the **+** drop-down in the header, and select **New repository** :</span></span>

    ![Opzione del nuovo repository GitHub](media/cicd/github-new-repo.png)

1. <span data-ttu-id="a7ff0-121">Selezionare l'account nell'elenco a discesa **proprietario** e immettere *Simple-Feed-Reader* nella casella di testo **nome repository** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-121">Select your account in the **Owner** drop-down, and enter *simple-feed-reader* in the **Repository name** textbox.</span></span>
1. <span data-ttu-id="a7ff0-122">Fare clic sul pulsante **Crea repository** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-122">Click the **Create repository** button.</span></span>
1. <span data-ttu-id="a7ff0-123">Aprire la shell dei comandi del computer locale.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-123">Open your local machine's command shell.</span></span> <span data-ttu-id="a7ff0-124">Passare alla directory in cui è archiviato il repository git *Simple-Feed-Reader* .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-124">Navigate to the directory in which the *simple-feed-reader* Git repository is stored.</span></span>
1. <span data-ttu-id="a7ff0-125">Rinominare l' *origine* esistente remote in *upstream* .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-125">Rename the existing *origin* remote to *upstream* .</span></span> <span data-ttu-id="a7ff0-126">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="a7ff0-126">Execute the following command:</span></span>

    ```console
    git remote rename origin upstream
    ```

1. <span data-ttu-id="a7ff0-127">Aggiungere una nuova *origine* remota che punta alla copia del repository in GitHub.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-127">Add a new *origin* remote pointing to your copy of the repository on GitHub.</span></span> <span data-ttu-id="a7ff0-128">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="a7ff0-128">Execute the following command:</span></span>

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. <span data-ttu-id="a7ff0-129">Pubblicare il repository git locale nel repository GitHub appena creato.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-129">Publish your local Git repository to the newly created GitHub repository.</span></span> <span data-ttu-id="a7ff0-130">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="a7ff0-130">Execute the following command:</span></span>

    ```console
    git push -u origin master
    ```

1. <span data-ttu-id="a7ff0-131">Aprire una finestra del browser e passare a `https://github.com/<GitHub_username>/simple-feed-reader/` .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-131">Open a browser window, and navigate to `https://github.com/<GitHub_username>/simple-feed-reader/`.</span></span> <span data-ttu-id="a7ff0-132">Verificare che il codice venga visualizzato nel repository GitHub.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-132">Validate that your code appears in the GitHub repository.</span></span>

## <a name="disconnect-local-git-deployment"></a><span data-ttu-id="a7ff0-133">Disconnettere la distribuzione git locale</span><span class="sxs-lookup"><span data-stu-id="a7ff0-133">Disconnect local Git deployment</span></span>

<span data-ttu-id="a7ff0-134">Rimuovere la distribuzione git locale con i passaggi seguenti.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-134">Remove the local Git deployment with the following steps.</span></span> <span data-ttu-id="a7ff0-135">Azure Pipelines (un servizio Azure DevOps) sostituisce e potenzia tale funzionalità.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-135">Azure Pipelines (an Azure DevOps service) both replaces and augments that functionality.</span></span>

1. <span data-ttu-id="a7ff0-136">Aprire il [portale di Azure](https://portal.azure.com/)e passare all'app Web di *staging (MyWebApp \<unique_number\> /staging)* .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-136">Open the [Azure portal](https://portal.azure.com/), and navigate to the *staging (mywebapp\<unique_number\>/staging)* Web App.</span></span> <span data-ttu-id="a7ff0-137">L'app Web può essere individuata rapidamente immettendo *staging* nella casella di ricerca del portale:</span><span class="sxs-lookup"><span data-stu-id="a7ff0-137">The Web App can be quickly located by entering *staging* in the portal's search box:</span></span>

    ![termine di ricerca dell'app Web di staging](media/cicd/portal-search-box.png)

1. <span data-ttu-id="a7ff0-139">Fare clic su **centro distribuzione** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-139">Click **Deployment Center** .</span></span> <span data-ttu-id="a7ff0-140">Verrà visualizzato un nuovo pannello.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-140">A new panel appears.</span></span> <span data-ttu-id="a7ff0-141">Fare clic su **Disconnetti** per rimuovere la configurazione del controllo del codice sorgente git locale aggiunta nel capitolo precedente.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-141">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="a7ff0-142">Confermare l'operazione di rimozione facendo clic sul pulsante **Sì** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-142">Confirm the removal operation by clicking the **Yes** button.</span></span>
1. <span data-ttu-id="a7ff0-143">Passare alla *<mywebapp unique_number>* servizio app.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-143">Navigate to the *mywebapp<unique_number>* App Service.</span></span> <span data-ttu-id="a7ff0-144">Come promemoria, è possibile usare la casella di ricerca del portale per individuare rapidamente il servizio app.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-144">As a reminder, the portal's search box can be used to quickly locate the App Service.</span></span>
1. <span data-ttu-id="a7ff0-145">Fare clic su **centro distribuzione** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-145">Click **Deployment Center** .</span></span> <span data-ttu-id="a7ff0-146">Verrà visualizzato un nuovo pannello.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-146">A new panel appears.</span></span> <span data-ttu-id="a7ff0-147">Fare clic su **Disconnetti** per rimuovere la configurazione del controllo del codice sorgente git locale aggiunta nel capitolo precedente.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-147">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="a7ff0-148">Confermare l'operazione di rimozione facendo clic sul pulsante **Sì** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-148">Confirm the removal operation by clicking the **Yes** button.</span></span>

## <a name="create-an-azure-devops-organization"></a><span data-ttu-id="a7ff0-149">Creare un'organizzazione Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="a7ff0-149">Create an Azure DevOps organization</span></span>

1. <span data-ttu-id="a7ff0-150">Aprire un browser e passare alla pagina di [creazione dell'organizzazione DevOps di Azure](https://go.microsoft.com/fwlink/?LinkId=307137).</span><span class="sxs-lookup"><span data-stu-id="a7ff0-150">Open a browser, and navigate to the [Azure DevOps organization creation page](https://go.microsoft.com/fwlink/?LinkId=307137).</span></span>
1. <span data-ttu-id="a7ff0-151">Digitare un nome univoco nella casella di testo **selezionare un nome memorabile** per creare l'URL per l'accesso all'organizzazione DevOps di Azure.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-151">Type a unique name into the **Pick a memorable name** textbox to form the URL for accessing your Azure DevOps organization.</span></span>
1. <span data-ttu-id="a7ff0-152">Selezionare il pulsante di opzione **git** , perché il codice è ospitato in un repository GitHub.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-152">Select the **Git** radio button, since the code is hosted in a GitHub repository.</span></span>
1. <span data-ttu-id="a7ff0-153">Fare clic sul pulsante **Continue** (Continue).</span><span class="sxs-lookup"><span data-stu-id="a7ff0-153">Click the **Continue** button.</span></span> <span data-ttu-id="a7ff0-154">Dopo una breve attesa, vengono creati un account e un progetto team, denominato *MyFirstProject* .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-154">After a short wait, an account and a team project, named *MyFirstProject* , are created.</span></span>

    ![Pagina di creazione dell'organizzazione DevOps di Azure](media/cicd/vsts-account-creation.png)

1. <span data-ttu-id="a7ff0-156">Aprire il messaggio di posta elettronica di conferma che indica che l'organizzazione e il progetto di Azure DevOps sono pronti per l'uso.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-156">Open the confirmation email indicating that the Azure DevOps organization and project are ready for use.</span></span> <span data-ttu-id="a7ff0-157">Fare clic sul pulsante **Avvia progetto** :</span><span class="sxs-lookup"><span data-stu-id="a7ff0-157">Click the **Start your project** button:</span></span>

    ![Pulsante Avvia progetto](media/cicd/vsts-start-project.png)

1. <span data-ttu-id="a7ff0-159">Verrà aperto un browser a *\<account_name\> . VisualStudio.com* .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-159">A browser opens to *\<account_name\>.visualstudio.com* .</span></span> <span data-ttu-id="a7ff0-160">Fare clic sul collegamento *MyFirstProject* per avviare la configurazione della pipeline DevOps del progetto.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-160">Click the *MyFirstProject* link to begin configuring the project's DevOps pipeline.</span></span>

## <a name="configure-the-azure-pipelines-pipeline"></a><span data-ttu-id="a7ff0-161">Configurare la pipeline di Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="a7ff0-161">Configure the Azure Pipelines pipeline</span></span>

<span data-ttu-id="a7ff0-162">Sono disponibili tre passaggi distinti per il completamento.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-162">There are three distinct steps to complete.</span></span> <span data-ttu-id="a7ff0-163">Il completamento dei passaggi nelle tre sezioni seguenti restituisce una pipeline DevOps operativa.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-163">Completing the steps in the following three sections results in an operational DevOps pipeline.</span></span>

### <a name="grant-azure-devops-access-to-the-github-repository"></a><span data-ttu-id="a7ff0-164">Concedere ad Azure DevOps l'accesso al repository GitHub</span><span class="sxs-lookup"><span data-stu-id="a7ff0-164">Grant Azure DevOps access to the GitHub repository</span></span>

1. <span data-ttu-id="a7ff0-165">Espandere il **codice di compilazione o da una Accordion del repository esterno** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-165">Expand the **or build code from an external repository** accordion.</span></span> <span data-ttu-id="a7ff0-166">Fare clic sul pulsante **Configura compilazione** :</span><span class="sxs-lookup"><span data-stu-id="a7ff0-166">Click the **Setup Build** button:</span></span>

    ![Pulsante Configura compilazione](media/cicd/vsts-setup-build.png)

1. <span data-ttu-id="a7ff0-168">Selezionare l'opzione **GitHub** nella sezione **selezionare un'origine** :</span><span class="sxs-lookup"><span data-stu-id="a7ff0-168">Select the **GitHub** option from the **Select a source** section:</span></span>

    ![Selezionare un'origine-GitHub](media/cicd/vsts-select-source.png)

1. <span data-ttu-id="a7ff0-170">L'autorizzazione è obbligatoria prima che Azure DevOps possa accedere al repository GitHub.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-170">Authorization is required before Azure DevOps can access your GitHub repository.</span></span> <span data-ttu-id="a7ff0-171">Immettere *<GitHub_username> connessione GitHub* nella casella di testo **nome connessione** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-171">Enter *<GitHub_username> GitHub connection* in the **Connection name** textbox.</span></span> <span data-ttu-id="a7ff0-172">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a7ff0-172">For example:</span></span>

    ![Nome della connessione GitHub](media/cicd/vsts-repo-authz.png)

1. <span data-ttu-id="a7ff0-174">Se l'autenticazione a due fattori è abilitata nell'account GitHub, è necessario un token di accesso personale.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-174">If two-factor authentication is enabled on your GitHub account, a personal access token is required.</span></span> <span data-ttu-id="a7ff0-175">In tal caso, fare clic sul collegamento **autorizza con un token di accesso personale GitHub** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-175">In that case, click the **Authorize with a GitHub personal access token** link.</span></span> <span data-ttu-id="a7ff0-176">Vedere le istruzioni per la creazione di un [token di accesso personale GitHub ufficiale](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) per la guida.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-176">See the [official GitHub personal access token creation instructions](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) for help.</span></span> <span data-ttu-id="a7ff0-177">È necessario solo l'ambito del *repository* delle autorizzazioni.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-177">Only the *repo* scope of permissions is needed.</span></span> <span data-ttu-id="a7ff0-178">In caso contrario, fare clic sul pulsante **autorizza tramite OAuth** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-178">Otherwise, click the **Authorize using OAuth** button.</span></span>
1. <span data-ttu-id="a7ff0-179">Quando richiesto, accedere al proprio account GitHub.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-179">When prompted, sign in to your GitHub account.</span></span> <span data-ttu-id="a7ff0-180">Selezionare quindi autorizza per concedere l'accesso all'organizzazione DevOps di Azure.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-180">Then select Authorize to grant access to your Azure DevOps organization.</span></span> <span data-ttu-id="a7ff0-181">Se l'esito è positivo, viene creato un nuovo endpoint del servizio.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-181">If successful, a new service endpoint is created.</span></span>
1. <span data-ttu-id="a7ff0-182">Fare clic sul pulsante con i puntini di sospensione accanto al pulsante **repository** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-182">Click the ellipsis button next to the **Repository** button.</span></span> <span data-ttu-id="a7ff0-183">Selezionare il *<GitHub_username>repository/Simple-Feed-Reader* dall'elenco.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-183">Select the *<GitHub_username>/simple-feed-reader* repository from the list.</span></span> <span data-ttu-id="a7ff0-184">Fare clic sul pulsante **Seleziona** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-184">Click the **Select** button.</span></span>
1. <span data-ttu-id="a7ff0-185">Selezionare il ramo *Master* dal **ramo predefinito per le compilazioni manuali e pianificate** a discesa.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-185">Select the *master* branch from the **Default branch for manual and scheduled builds** drop-down.</span></span> <span data-ttu-id="a7ff0-186">Fare clic sul pulsante **Continue** (Continue).</span><span class="sxs-lookup"><span data-stu-id="a7ff0-186">Click the **Continue** button.</span></span> <span data-ttu-id="a7ff0-187">Verrà visualizzata la pagina Selezione modello.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-187">The template selection page appears.</span></span>

### <a name="create-the-build-definition"></a><span data-ttu-id="a7ff0-188">Creare la definizione di compilazione</span><span class="sxs-lookup"><span data-stu-id="a7ff0-188">Create the build definition</span></span>

1. <span data-ttu-id="a7ff0-189">Nella pagina Selezione modello immettere *ASP.NET Core* nella casella di ricerca:</span><span class="sxs-lookup"><span data-stu-id="a7ff0-189">From the template selection page, enter *ASP.NET Core* in the search box:</span></span>

    ![ASP.NET Core ricerca nella pagina modello](media/cicd/vsts-template-selection.png)

1. <span data-ttu-id="a7ff0-191">Verranno visualizzati i risultati della ricerca del modello.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-191">The template search results appear.</span></span> <span data-ttu-id="a7ff0-192">Passare il puntatore del mouse sul modello di **ASP.NET Core** , quindi fare clic sul pulsante **applica** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-192">Hover over the **ASP.NET Core** template, and click the **Apply** button.</span></span>
1. <span data-ttu-id="a7ff0-193">Verrà visualizzata la scheda **attività** della definizione di compilazione.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-193">The **Tasks** tab of the build definition appears.</span></span> <span data-ttu-id="a7ff0-194">Fare clic sulla scheda **trigger** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-194">Click the **Triggers** tab.</span></span>
1. <span data-ttu-id="a7ff0-195">Selezionare la casella **Abilita integrazione continua** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-195">Check the **Enable continuous integration** box.</span></span> <span data-ttu-id="a7ff0-196">Nella sezione **filtri ramo** verificare che l'elenco a discesa **tipo** sia impostato su *Includi* .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-196">Under the **Branch filters** section, confirm that the **Type** drop-down is set to *Include* .</span></span> <span data-ttu-id="a7ff0-197">Impostare l'elenco a discesa **specifica ramo** su *Master* .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-197">Set the **Branch specification** drop-down to *master* .</span></span>

    ![Abilita impostazioni di integrazione continua](media/cicd/vsts-enable-ci.png)

    <span data-ttu-id="a7ff0-199">Queste impostazioni causano l'attivazione di una compilazione quando viene effettuato il push di una modifica nel ramo *Master* del repository GitHub.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-199">These settings cause a build to trigger when any change is pushed to the *master* branch of the GitHub repository.</span></span> <span data-ttu-id="a7ff0-200">L'integrazione continua viene testata nella sezione [commit delle modifiche in GitHub e distribuzione automatica in Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-200">Continuous integration is tested in the [Commit changes to GitHub and automatically deploy to Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) section.</span></span>

1. <span data-ttu-id="a7ff0-201">Fare clic sul pulsante **salva & coda** e selezionare l'opzione **Salva** :</span><span class="sxs-lookup"><span data-stu-id="a7ff0-201">Click the **Save & queue** button, and select the **Save** option:</span></span>

    ![Pulsante Salva](media/cicd/vsts-save-build.png)

1. <span data-ttu-id="a7ff0-203">Viene visualizzata la finestra di dialogo modale seguente:</span><span class="sxs-lookup"><span data-stu-id="a7ff0-203">The following modal dialog appears:</span></span>

    ![Salva definizione di compilazione-finestra di dialogo modale](media/cicd/vsts-save-modal.png)

    <span data-ttu-id="a7ff0-205">Utilizzare la cartella predefinita *\\* , quindi fare clic sul pulsante **Salva** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-205">Use the default folder of *\\* , and click the **Save** button.</span></span>

### <a name="create-the-release-pipeline"></a><span data-ttu-id="a7ff0-206">Creare la pipeline di versione</span><span class="sxs-lookup"><span data-stu-id="a7ff0-206">Create the release pipeline</span></span>

1. <span data-ttu-id="a7ff0-207">Fare clic sulla scheda **versioni** del progetto team.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-207">Click the **Releases** tab of your team project.</span></span> <span data-ttu-id="a7ff0-208">Fare clic sul pulsante **nuova pipeline** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-208">Click the **New pipeline** button.</span></span>

    ![Scheda versioni-pulsante nuova definizione](media/cicd/vsts-new-release-definition.png)

    <span data-ttu-id="a7ff0-210">Viene visualizzato il riquadro di selezione del modello.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-210">The template selection pane appears.</span></span>

1. <span data-ttu-id="a7ff0-211">Nella pagina Selezione modello immettere *servizio app* nella casella di ricerca:</span><span class="sxs-lookup"><span data-stu-id="a7ff0-211">From the template selection page, enter *App Service* in the search box:</span></span>

    ![Casella di ricerca modello pipeline di rilascio](media/cicd/vsts-release-template-search.png)

1. <span data-ttu-id="a7ff0-213">Verranno visualizzati i risultati della ricerca del modello.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-213">The template search results appear.</span></span> <span data-ttu-id="a7ff0-214">Passare il puntatore del mouse sulla **distribuzione del servizio app Azure con il modello di slot** , quindi fare clic sul pulsante **applica** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-214">Hover over the **Azure App Service Deployment with Slot** template, and click the **Apply** button.</span></span> <span data-ttu-id="a7ff0-215">Verrà visualizzata la scheda **pipeline** della pipeline di rilascio.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-215">The **Pipeline** tab of the release pipeline appears.</span></span>

    ![Scheda pipeline della pipeline di rilascio](media/cicd/vsts-release-definition-pipeline.png)

1. <span data-ttu-id="a7ff0-217">Fare clic sul pulsante **Aggiungi** nella casella **artefatti** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-217">Click the **Add** button in the **Artifacts** box.</span></span> <span data-ttu-id="a7ff0-218">Viene visualizzato il pannello **Aggiungi elemento** :</span><span class="sxs-lookup"><span data-stu-id="a7ff0-218">The **Add artifact** panel appears:</span></span>

    ![Pipeline di rilascio-Aggiungi pannello artefatto](media/cicd/vsts-release-add-artifact.png)

1. <span data-ttu-id="a7ff0-220">Selezionare il riquadro **Compila** dalla sezione **tipo di origine** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-220">Select the **Build** tile from the **Source type** section.</span></span> <span data-ttu-id="a7ff0-221">Questo tipo consente il collegamento della pipeline di rilascio alla definizione di compilazione.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-221">This type allows for the linking of the release pipeline to the build definition.</span></span>
1. <span data-ttu-id="a7ff0-222">Selezionare *MyFirstProject* dall'elenco a discesa **progetto** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-222">Select *MyFirstProject* from the **Project** drop-down.</span></span>
1. <span data-ttu-id="a7ff0-223">Selezionare il nome della definizione di compilazione, *MyFirstProject-ASP.NET Core-ci* , dall'elenco a discesa **origine (definizione di compilazione)** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-223">Select the build definition name, *MyFirstProject-ASP.NET Core-CI* , from the **Source (Build definition)** drop-down.</span></span>
1. <span data-ttu-id="a7ff0-224">Selezionare *più recente* dall'elenco a discesa **versione predefinita** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-224">Select *Latest* from the **Default version** drop-down.</span></span> <span data-ttu-id="a7ff0-225">Questa opzione Compila gli artefatti prodotti dall'ultima esecuzione della definizione di compilazione.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-225">This option builds the artifacts produced by the latest run of the build definition.</span></span>
1. <span data-ttu-id="a7ff0-226">Sostituire il testo nella casella di testo **alias di origine** con *Drop* .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-226">Replace the text in the **Source alias** textbox with *Drop* .</span></span>
1. <span data-ttu-id="a7ff0-227">Fare clic su **Add** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-227">Click the **Add** button.</span></span> <span data-ttu-id="a7ff0-228">La sezione **artefatti** viene aggiornata per visualizzare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-228">The **Artifacts** section updates to display the changes.</span></span>
1. <span data-ttu-id="a7ff0-229">Fare clic sull'icona del fulmine per abilitare le distribuzioni continue:</span><span class="sxs-lookup"><span data-stu-id="a7ff0-229">Click the lightning bolt icon to enable continuous deployments:</span></span>

    ![Elementi della pipeline di rilascio-icona del fulmine](media/cicd/vsts-artifacts-lightning-bolt.png)

    <span data-ttu-id="a7ff0-231">Se questa opzione è abilitata, viene eseguita una distribuzione ogni volta che è disponibile una nuova compilazione.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-231">With this option enabled, a deployment occurs each time a new build is available.</span></span>
1. <span data-ttu-id="a7ff0-232">A destra viene visualizzato un pannello **trigger di distribuzione continua** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-232">A **Continuous deployment trigger** panel appears to the right.</span></span> <span data-ttu-id="a7ff0-233">Per abilitare la funzionalità, fare clic sull'interruttore.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-233">Click the toggle button to enable the feature.</span></span> <span data-ttu-id="a7ff0-234">Non è necessario abilitare il **trigger della richiesta pull** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-234">It isn't necessary to enable the **Pull request trigger** .</span></span>
1. <span data-ttu-id="a7ff0-235">Fare clic sull'elenco a discesa **Aggiungi** nella sezione **filtri del ramo di compilazione** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-235">Click the **Add** drop-down in the **Build branch filters** section.</span></span> <span data-ttu-id="a7ff0-236">Scegliere l'opzione relativa al **ramo predefinito della definizione di compilazione** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-236">Choose the **Build Definition's default branch** option.</span></span> <span data-ttu-id="a7ff0-237">Questo filtro determina l'attivazione della versione solo per una compilazione dal ramo *Master* del repository GitHub.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-237">This filter causes the release to trigger only for a build from the GitHub repository's *master* branch.</span></span>
1. <span data-ttu-id="a7ff0-238">Fare clic sul pulsante **Salva** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-238">Click the **Save** button.</span></span> <span data-ttu-id="a7ff0-239">Fare clic sul pulsante **OK** nella finestra di dialogo **Salva** modale risultante.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-239">Click the **OK** button in the resulting **Save** modal dialog.</span></span>
1. <span data-ttu-id="a7ff0-240">Fare clic sulla casella **ambiente 1** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-240">Click the **Environment 1** box.</span></span> <span data-ttu-id="a7ff0-241">A destra viene visualizzato un pannello **dell'ambiente** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-241">An **Environment** panel appears to the right.</span></span> <span data-ttu-id="a7ff0-242">Modificare il testo dell' *ambiente 1* nella casella di testo **nome ambiente** in *produzione* .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-242">Change the *Environment 1* text in the **Environment name** textbox to *Production* .</span></span>

   ![Pipeline di rilascio-casella di testo nome ambiente](media/cicd/vsts-environment-name-textbox.png)

1. <span data-ttu-id="a7ff0-244">Fare clic sul collegamento **1 fase, 2 attività** nella casella **produzione** :</span><span class="sxs-lookup"><span data-stu-id="a7ff0-244">Click the **1 phase, 2 tasks** link in the **Production** box:</span></span>

    ![Pipeline di versione: ambiente di produzione link.png](media/cicd/vsts-production-link.png)

    <span data-ttu-id="a7ff0-246">Verrà visualizzata la scheda **attività** dell'ambiente.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-246">The **Tasks** tab of the environment appears.</span></span>
1. <span data-ttu-id="a7ff0-247">Fare clic sull'attività **Distribuisci servizio app Azure a slot** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-247">Click the **Deploy Azure App Service to Slot** task.</span></span> <span data-ttu-id="a7ff0-248">Le impostazioni vengono visualizzate in un pannello a destra.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-248">Its settings appear in a panel to the right.</span></span>
1. <span data-ttu-id="a7ff0-249">Selezionare la sottoscrizione di Azure associata al servizio app dall'elenco a discesa **sottoscrizione di Azure** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-249">Select the Azure subscription associated with the App Service from the **Azure subscription** drop-down.</span></span> <span data-ttu-id="a7ff0-250">Una volta selezionata, fare clic sul pulsante **autorizza** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-250">Once selected, click the **Authorize** button.</span></span>
1. <span data-ttu-id="a7ff0-251">Selezionare *app Web* dall'elenco a discesa **tipo di app** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-251">Select *Web App* from the **App type** drop-down.</span></span>
1. <span data-ttu-id="a7ff0-252">Selezionare *MyWebApp/<unique_number/>* dall'elenco a discesa **nome servizio app** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-252">Select *mywebapp/<unique_number/>* from the **App service name** drop-down.</span></span>
1. <span data-ttu-id="a7ff0-253">Selezionare *AzureTutorial* nell'elenco a discesa **gruppo di risorse** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-253">Select *AzureTutorial* from the **Resource group** drop-down.</span></span>
1. <span data-ttu-id="a7ff0-254">Selezionare *staging* dall'elenco a discesa **slot** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-254">Select *staging* from the **Slot** drop-down.</span></span>
1. <span data-ttu-id="a7ff0-255">Fare clic sul pulsante **Salva** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-255">Click the **Save** button.</span></span>
1. <span data-ttu-id="a7ff0-256">Passare il puntatore del mouse sul nome della pipeline di versione predefinita.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-256">Hover over the default release pipeline name.</span></span> <span data-ttu-id="a7ff0-257">Fare clic sull'icona della matita per modificarla.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-257">Click the pencil icon to edit it.</span></span> <span data-ttu-id="a7ff0-258">Usare *MyFirstProject-ASP.NET Core-CD* come nome.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-258">Use *MyFirstProject-ASP.NET Core-CD* as the name.</span></span>

    ![Nome della pipeline di versione](media/cicd/vsts-release-definition-name.png)

1. <span data-ttu-id="a7ff0-260">Fare clic sul pulsante **Salva** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-260">Click the **Save** button.</span></span>

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a><span data-ttu-id="a7ff0-261">Eseguire il commit delle modifiche in GitHub e la distribuzione automatica in Azure</span><span class="sxs-lookup"><span data-stu-id="a7ff0-261">Commit changes to GitHub and automatically deploy to Azure</span></span>

1. <span data-ttu-id="a7ff0-262">Aprire *SimpleFeedReader. sln* in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-262">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
1. <span data-ttu-id="a7ff0-263">In Esplora soluzioni aprire *Pages\Index.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-263">In Solution Explorer, open *Pages\Index.cshtml* .</span></span> <span data-ttu-id="a7ff0-264">Cambiare `<h2>Simple Feed Reader - V3</h2>` in `<h2>Simple Feed Reader - V4</h2>`.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-264">Change `<h2>Simple Feed Reader - V3</h2>` to `<h2>Simple Feed Reader - V4</h2>`.</span></span>
1. <span data-ttu-id="a7ff0-265">Premere **CTRL** + **MAIUSC** + **B** per compilare l'app.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-265">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
1. <span data-ttu-id="a7ff0-266">Eseguire il commit del file nel repository GitHub.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-266">Commit the file to the GitHub repository.</span></span> <span data-ttu-id="a7ff0-267">Utilizzare la pagina **modifiche** nella scheda *Team Explorer* di Visual Studio o eseguire le operazioni seguenti utilizzando la shell dei comandi del computer locale:</span><span class="sxs-lookup"><span data-stu-id="a7ff0-267">Use either the **Changes** page in Visual Studio's *Team Explorer* tab, or execute the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. <span data-ttu-id="a7ff0-268">Eseguire il push della modifica nel ramo *Master* nell' *origine* remota del repository GitHub:</span><span class="sxs-lookup"><span data-stu-id="a7ff0-268">Push the change in the *master* branch to the *origin* remote of your GitHub repository:</span></span>

    ```console
    git push origin master
    ```

    <span data-ttu-id="a7ff0-269">Il commit viene visualizzato nel ramo *Master* del repository GitHub:</span><span class="sxs-lookup"><span data-stu-id="a7ff0-269">The commit appears in the GitHub repository's *master* branch:</span></span>

    ![Commit di GitHub nel ramo master](media/cicd/github-commit.png)

    <span data-ttu-id="a7ff0-271">La compilazione viene attivata perché l'integrazione continua è abilitata nella scheda **trigger** della definizione di compilazione:</span><span class="sxs-lookup"><span data-stu-id="a7ff0-271">The build is triggered, since continuous integration is enabled in the build definition's **Triggers** tab:</span></span>

    ![Abilita integrazione continua](media/cicd/enable-ci.png)

1. <span data-ttu-id="a7ff0-273">Passare alla scheda in **coda** della **Azure Pipelines**  >  pagina **compilazioni** Azure Pipelines in Azure DevOps Services.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-273">Navigate to the **Queued** tab of the **Azure Pipelines** > **Builds** page in Azure DevOps Services.</span></span> <span data-ttu-id="a7ff0-274">La compilazione in coda Mostra il ramo e il commit che ha attivato la compilazione:</span><span class="sxs-lookup"><span data-stu-id="a7ff0-274">The queued build shows the branch and commit that triggered the build:</span></span>

    ![compilazione in coda](media/cicd/build-queued.png)

1. <span data-ttu-id="a7ff0-276">Una volta completata la compilazione, viene eseguita una distribuzione in Azure.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-276">Once the build succeeds, a deployment to Azure occurs.</span></span> <span data-ttu-id="a7ff0-277">Passare all'app nel browser.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-277">Navigate to the app in the browser.</span></span> <span data-ttu-id="a7ff0-278">Si noti che il testo "V4" viene visualizzato nell'intestazione:</span><span class="sxs-lookup"><span data-stu-id="a7ff0-278">Notice that the "V4" text appears in the heading:</span></span>

    ![App aggiornata](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a><span data-ttu-id="a7ff0-280">Esaminare la pipeline Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="a7ff0-280">Examine the Azure Pipelines pipeline</span></span>

### <a name="build-definition"></a><span data-ttu-id="a7ff0-281">Definizione di compilazione</span><span class="sxs-lookup"><span data-stu-id="a7ff0-281">Build definition</span></span>

<span data-ttu-id="a7ff0-282">È stata creata una definizione di compilazione con il nome *MyFirstProject-ASP.NET Core-ci* .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-282">A build definition was created with the name *MyFirstProject-ASP.NET Core-CI* .</span></span> <span data-ttu-id="a7ff0-283">Al termine, la compilazione produce un file con *estensione zip* che include gli asset da pubblicare.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-283">Upon completion, the build produces a *.zip* file including the assets to be published.</span></span> <span data-ttu-id="a7ff0-284">La pipeline di rilascio distribuisce tali asset in Azure.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-284">The release pipeline deploys those assets to Azure.</span></span>

<span data-ttu-id="a7ff0-285">Nella scheda **attività** della definizione di compilazione sono elencati i singoli passaggi utilizzati.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-285">The build definition's **Tasks** tab lists the individual steps being used.</span></span> <span data-ttu-id="a7ff0-286">Sono disponibili cinque attività di compilazione.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-286">There are five build tasks.</span></span>

![attività definizione di compilazione](media/cicd/build-definition-tasks.png)

1. <span data-ttu-id="a7ff0-288">**Ripristino** &mdash; di Esegue il `dotnet restore` comando per ripristinare i pacchetti NuGet dell'app.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-288">**Restore** &mdash; Executes the `dotnet restore` command to restore the app's NuGet packages.</span></span> <span data-ttu-id="a7ff0-289">Il feed del pacchetto predefinito usato è nuget.org.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-289">The default package feed used is nuget.org.</span></span>
1. <span data-ttu-id="a7ff0-290">**Compilazione** &mdash; di Esegue il `dotnet build --configuration release` comando per compilare il codice dell'app.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-290">**Build** &mdash; Executes the `dotnet build --configuration release` command to compile the app's code.</span></span> <span data-ttu-id="a7ff0-291">Questa `--configuration` opzione viene usata per produrre una versione ottimizzata del codice, adatto per la distribuzione in un ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-291">This `--configuration` option is used to produce an optimized version of the code, which is suitable for deployment to a production environment.</span></span> <span data-ttu-id="a7ff0-292">Modificare la variabile *BuildConfiguration* nella scheda **variabili** della definizione di compilazione se, ad esempio, è necessaria una configurazione di debug.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-292">Modify the *BuildConfiguration* variable on the build definition's **Variables** tab if, for example, a debug configuration is needed.</span></span>
1. <span data-ttu-id="a7ff0-293">Esegui **test** &mdash; Esegue il `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` comando per eseguire gli unit test dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-293">**Test** &mdash; Executes the `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` command to run the app's unit tests.</span></span> <span data-ttu-id="a7ff0-294">Gli unit test vengono eseguiti in qualsiasi progetto C# corrispondente al `**/*Tests/*.csproj` modello glob.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-294">Unit tests are executed within any C# project matching the `**/*Tests/*.csproj` glob pattern.</span></span> <span data-ttu-id="a7ff0-295">I risultati dei test vengono salvati in un file con *estensione TRX* nel percorso specificato dall' `--results-directory` opzione.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-295">Test results are saved in a *.trx* file at the location specified by the `--results-directory` option.</span></span> <span data-ttu-id="a7ff0-296">Se i test hanno esito negativo, la compilazione non riesce e non viene distribuita.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-296">If any tests fail, the build fails and isn't deployed.</span></span>

    > [!NOTE]
    > <span data-ttu-id="a7ff0-297">Per verificare il funzionamento degli unit test, modificare *SimpleFeedReader. Tests\Services\NewsServiceTests.cs* per interrompere intenzionalmente uno dei test.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-297">To verify the unit tests work, modify *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* to purposefully break one of the tests.</span></span> <span data-ttu-id="a7ff0-298">Modificare ad esempio `Assert.True(result.Count > 0);` `Assert.False(result.Count > 0);` in nel `Returns_News_Stories_Given_Valid_Uri` metodo.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-298">For example, change `Assert.True(result.Count > 0);` to `Assert.False(result.Count > 0);` in the `Returns_News_Stories_Given_Valid_Uri` method.</span></span> <span data-ttu-id="a7ff0-299">Eseguire il commit e il push della modifica in GitHub.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-299">Commit and push the change to GitHub.</span></span> <span data-ttu-id="a7ff0-300">La compilazione viene attivata e ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-300">The build is triggered and fails.</span></span> <span data-ttu-id="a7ff0-301">Lo stato della pipeline di compilazione diventa **non riuscito** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-301">The build pipeline status changes to **failed** .</span></span> <span data-ttu-id="a7ff0-302">Ripristinare la modifica, il commit e il push.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-302">Revert the change, commit, and push again.</span></span> <span data-ttu-id="a7ff0-303">La compilazione ha esito positivo.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-303">The build succeeds.</span></span>

1. <span data-ttu-id="a7ff0-304">**Pubblica** &mdash; Esegue il `dotnet publish --configuration release --output <local_path_on_build_agent>` comando per produrre un file con *estensione zip* con gli elementi da distribuire.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-304">**Publish** &mdash; Executes the `dotnet publish --configuration release --output <local_path_on_build_agent>` command to produce a *.zip* file with the artifacts to be deployed.</span></span> <span data-ttu-id="a7ff0-305">L' `--output` opzione specifica il percorso di pubblicazione del file con *estensione zip* .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-305">The `--output` option specifies the publish location of the *.zip* file.</span></span> <span data-ttu-id="a7ff0-306">Tale percorso viene specificato passando una [variabile predefinita](/azure/devops/pipelines/build/variables) denominata `$(build.artifactstagingdirectory)` .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-306">That location is specified by passing a [predefined variable](/azure/devops/pipelines/build/variables) named `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="a7ff0-307">Tale variabile si espande in un percorso locale, ad esempio *c:\agent \_ work\1\a* , nell'agente di compilazione.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-307">That variable expands to a local path, such as *c:\agent\_work\1\a* , on the build agent.</span></span>
1. <span data-ttu-id="a7ff0-308">**Pubblica artefatto** &mdash; Pubblica il file *zip* prodotto dall'attività **Publish** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-308">**Publish Artifact** &mdash; Publishes the *.zip* file produced by the **Publish** task.</span></span> <span data-ttu-id="a7ff0-309">L'attività accetta il percorso del file con *estensione zip* come parametro, ovvero la variabile predefinita `$(build.artifactstagingdirectory)` .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-309">The task accepts the *.zip* file location as a parameter, which is the predefined variable `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="a7ff0-310">Il file *zip* viene pubblicato come cartella denominata *Drop* .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-310">The *.zip* file is published as a folder named *drop* .</span></span>

<span data-ttu-id="a7ff0-311">Fare clic sul collegamento **Riepilogo** definizione di compilazione per visualizzare una cronologia delle compilazioni con la definizione:</span><span class="sxs-lookup"><span data-stu-id="a7ff0-311">Click the build definition's **Summary** link to view a history of builds with the definition:</span></span>

![Screenshot che mostra la cronologia delle definizioni di compilazione](media/cicd/build-definition-summary.png)

<span data-ttu-id="a7ff0-313">Nella pagina risultante fare clic sul collegamento corrispondente al numero di build univoco:</span><span class="sxs-lookup"><span data-stu-id="a7ff0-313">On the resulting page, click the link corresponding to the unique build number:</span></span>

![Screenshot che mostra la pagina di riepilogo delle definizioni di compilazione](media/cicd/build-definition-completed.png)

<span data-ttu-id="a7ff0-315">Viene visualizzato un riepilogo di questa compilazione specifica.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-315">A summary of this specific build is displayed.</span></span> <span data-ttu-id="a7ff0-316">Fare clic sulla scheda **artefatti** e notare che la cartella di *ricezione* generata dalla compilazione è elencata:</span><span class="sxs-lookup"><span data-stu-id="a7ff0-316">Click the **Artifacts** tab, and notice the *drop* folder produced by the build is listed:</span></span>

![Screenshot che Mostra gli artefatti delle definizioni di compilazione-cartella di ricezione](media/cicd/build-definition-artifacts.png)

<span data-ttu-id="a7ff0-318">Usare i collegamenti **Scarica** ed **Esplora** per esaminare gli artefatti pubblicati.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-318">Use the **Download** and **Explore** links to inspect the published artifacts.</span></span>

### <a name="release-pipeline"></a><span data-ttu-id="a7ff0-319">Pipeline di versione</span><span class="sxs-lookup"><span data-stu-id="a7ff0-319">Release pipeline</span></span>

<span data-ttu-id="a7ff0-320">È stata creata una pipeline di versione con il nome *MyFirstProject-ASP.NET Core-CD* :</span><span class="sxs-lookup"><span data-stu-id="a7ff0-320">A release pipeline was created with the name *MyFirstProject-ASP.NET Core-CD* :</span></span>

![Screenshot che mostra la panoramica della pipeline di rilascio](media/cicd/release-definition-overview.png)

<span data-ttu-id="a7ff0-322">I due componenti principali della pipeline di rilascio sono gli **elementi** e gli **ambienti** .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-322">The two major components of the release pipeline are the **Artifacts** and the **Environments** .</span></span> <span data-ttu-id="a7ff0-323">Facendo clic sulla casella nella sezione **elementi** viene riportato il pannello seguente:</span><span class="sxs-lookup"><span data-stu-id="a7ff0-323">Clicking the box in the **Artifacts** section reveals the following panel:</span></span>

![Screenshot che Mostra gli artefatti della pipeline di rilascio](media/cicd/release-definition-artifacts.png)

<span data-ttu-id="a7ff0-325">Il valore di **origine (definizione di compilazione)** rappresenta la definizione di compilazione a cui è collegata questa pipeline di rilascio.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-325">The **Source (Build definition)** value represents the build definition to which this release pipeline is linked.</span></span> <span data-ttu-id="a7ff0-326">Il file con *estensione zip* prodotto da un'esecuzione corretta della definizione di compilazione viene fornito all'ambiente di *produzione* per la distribuzione in Azure.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-326">The *.zip* file produced by a successful run of the build definition is provided to the *Production* environment for deployment to Azure.</span></span> <span data-ttu-id="a7ff0-327">Fare clic sul collegamento *1 fase, 2 attività* nella casella ambiente di *produzione* per visualizzare le attività della pipeline di rilascio:</span><span class="sxs-lookup"><span data-stu-id="a7ff0-327">Click the *1 phase, 2 tasks* link in the *Production* environment box to view the release pipeline tasks:</span></span>

![Screenshot che illustra le attività della pipeline di rilascio](media/cicd/release-definition-tasks.png)

<span data-ttu-id="a7ff0-329">La pipeline di versione è costituita da due attività: *distribuisci app Azure servizio in slot* e *Gestisci app Azure scambio di slot di servizio* .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-329">The release pipeline consists of two tasks: *Deploy Azure App Service to Slot* and *Manage Azure App Service - Slot Swap* .</span></span> <span data-ttu-id="a7ff0-330">Se si fa clic sulla prima attività, viene visualizzata la configurazione dell'attività seguente:</span><span class="sxs-lookup"><span data-stu-id="a7ff0-330">Clicking the first task reveals the following task configuration:</span></span>

![Screenshot che mostra l'attività di distribuzione della pipeline di rilascio](media/cicd/release-definition-task1.png)

<span data-ttu-id="a7ff0-332">La sottoscrizione di Azure, il tipo di servizio, il nome dell'app Web, il gruppo di risorse e lo slot di distribuzione sono definiti nell'attività di distribuzione.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-332">The Azure subscription, service type, web app name, resource group, and deployment slot are defined in the deployment task.</span></span> <span data-ttu-id="a7ff0-333">La casella di testo **pacchetto o cartella** include il percorso del file con *estensione zip* da estrarre e distribuire nello slot di *staging* dell'app Web *MyWebApp \<unique_number\>* .</span><span class="sxs-lookup"><span data-stu-id="a7ff0-333">The **Package or folder** textbox holds the *.zip* file path to be extracted and deployed to the *staging* slot of the *mywebapp\<unique_number\>* web app.</span></span>

<span data-ttu-id="a7ff0-334">Se si fa clic sull'attività swap slot, viene visualizzata la configurazione dell'attività seguente:</span><span class="sxs-lookup"><span data-stu-id="a7ff0-334">Clicking the slot swap task reveals the following task configuration:</span></span>

![Screenshot che mostra l'attività di scambio slot della pipeline di rilascio](media/cicd/release-definition-task2.png)

<span data-ttu-id="a7ff0-336">Vengono forniti i dettagli relativi a sottoscrizione, gruppo di risorse, tipo di servizio, nome dell'app Web e slot di distribuzione.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-336">The subscription, resource group, service type, web app name, and deployment slot details are provided.</span></span> <span data-ttu-id="a7ff0-337">La casella **di controllo scambia con produzione** è selezionata.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-337">The **Swap with Production** check box is checked.</span></span> <span data-ttu-id="a7ff0-338">Di conseguenza, i bit distribuiti nello slot di *staging* vengono scambiati nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="a7ff0-338">Consequently, the bits deployed to the *staging* slot are swapped into the production environment.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="a7ff0-339">Altre letture</span><span class="sxs-lookup"><span data-stu-id="a7ff0-339">Additional reading</span></span>

* [<span data-ttu-id="a7ff0-340">Creare la prima pipeline con Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="a7ff0-340">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="a7ff0-341">Compilazione e progetto .NET Core</span><span class="sxs-lookup"><span data-stu-id="a7ff0-341">Build and .NET Core project</span></span>](/azure/devops/pipelines/languages/dotnet-core)
* [<span data-ttu-id="a7ff0-342">Distribuire un'app Web con Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="a7ff0-342">Deploy a web app with Azure Pipelines</span></span>](/azure/devops/pipelines/targets/webapp)
