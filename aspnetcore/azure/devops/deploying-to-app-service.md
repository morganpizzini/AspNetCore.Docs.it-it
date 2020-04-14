---
title: Distribuire un'app al servizio app - DevOps con ASP.NET Core e AzureDeploy an app to App Service - DevOps with ASP.NET Core and Azure
author: CamSoper
description: Distribuire un'app ASP.NET Core nel servizio app di Azure, il primo passaggio per DevOps con ASP.NET Core e Azure.Deploy an ASP.NET Core app to Azure App Service, the first step for DevOps with ASP.NET Core and Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: d7ee3e42d320d35c2aaff6e097203c45289ec5b1
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228127"
---
# <a name="deploy-an-app-to-app-service"></a><span data-ttu-id="a2157-103">Distribuire un'app nel servizio appDeploy an app to App Service</span><span class="sxs-lookup"><span data-stu-id="a2157-103">Deploy an app to App Service</span></span>

<span data-ttu-id="a2157-104">[Il servizio app di Azure](/azure/app-service/) è la piattaforma di hosting Web di Azure.Azure App Service is Azure's web hosting platform.</span><span class="sxs-lookup"><span data-stu-id="a2157-104">[Azure App Service](/azure/app-service/) is Azure's web hosting platform.</span></span> <span data-ttu-id="a2157-105">La distribuzione di un'app Web nel servizio app di Azure può essere eseguita manualmente o tramite un processo automatizzato.</span><span class="sxs-lookup"><span data-stu-id="a2157-105">Deploying a web app to Azure App Service can be done manually or by an automated process.</span></span> <span data-ttu-id="a2157-106">In questa sezione della guida vengono illustrati i metodi di distribuzione che possono essere attivati manualmente o tramite script tramite la riga di comando o manualmente tramite Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a2157-106">This section of the guide discusses deployment methods that can be triggered manually or by script using the command line, or triggered manually using Visual Studio.</span></span>

<span data-ttu-id="a2157-107">In questa sezione verranno eseguite le attività seguenti:In this section, you'll accomplish the following tasks:</span><span class="sxs-lookup"><span data-stu-id="a2157-107">In this section, you'll accomplish the following tasks:</span></span>

* <span data-ttu-id="a2157-108">Scaricare e compilare l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="a2157-108">Download and build the sample app.</span></span>
* <span data-ttu-id="a2157-109">Creare un'app Web del servizio app di Azure usando Azure Cloud Shell.Create an Azure App Service Web App using the Azure Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="a2157-109">Create an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="a2157-110">Distribuire l'app di esempio in Azure usando Git.Deploy the sample app to Azure using Git.</span><span class="sxs-lookup"><span data-stu-id="a2157-110">Deploy the sample app to Azure using Git.</span></span>
* <span data-ttu-id="a2157-111">Distribuire una modifica all'app usando Visual Studio.Deploy a change to the app using Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a2157-111">Deploy a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="a2157-112">Aggiungere uno slot di gestione temporanea all'app Web.</span><span class="sxs-lookup"><span data-stu-id="a2157-112">Add a staging slot to the web app.</span></span>
* <span data-ttu-id="a2157-113">Distribuire un aggiornamento allo slot di gestione temporanea.</span><span class="sxs-lookup"><span data-stu-id="a2157-113">Deploy an update to the staging slot.</span></span>
* <span data-ttu-id="a2157-114">Scambiare gli slot di staging e di produzione.</span><span class="sxs-lookup"><span data-stu-id="a2157-114">Swap the staging and production slots.</span></span>

## <a name="download-and-test-the-app"></a><span data-ttu-id="a2157-115">Scarica e testa l'app</span><span class="sxs-lookup"><span data-stu-id="a2157-115">Download and test the app</span></span>

<span data-ttu-id="a2157-116">L'app utilizzata in questa guida è un'app ASP.NET Core predefinita, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span><span class="sxs-lookup"><span data-stu-id="a2157-116">The app used in this guide is a pre-built ASP.NET Core app, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span></span> <span data-ttu-id="a2157-117">Si tratta di un'app Razor Pages che usa l'API `Microsoft.SyndicationFeed.ReaderWriter` per recuperare un feed RSS/Atom e visualizzare le voci di notizie in un elenco.</span><span class="sxs-lookup"><span data-stu-id="a2157-117">It's a Razor Pages app that uses the `Microsoft.SyndicationFeed.ReaderWriter` API to retrieve an RSS/Atom feed and display the news items in a list.</span></span>

<span data-ttu-id="a2157-118">Sentitevi liberi di rivedere il codice, ma è importante capire che non c'è niente di speciale in questa applicazione.</span><span class="sxs-lookup"><span data-stu-id="a2157-118">Feel free to review the code, but it's important to understand that there's nothing special about this app.</span></span> <span data-ttu-id="a2157-119">È solo una semplice app ASP.NET Core a scopo illustrativo.</span><span class="sxs-lookup"><span data-stu-id="a2157-119">It's just a simple ASP.NET Core app for illustrative purposes.</span></span>

<span data-ttu-id="a2157-120">Da una shell dei comandi, scaricare il codice, compilare il progetto ed eseguirlo come segue.</span><span class="sxs-lookup"><span data-stu-id="a2157-120">From a command shell, download the code, build the project, and run it as follows.</span></span>

> <span data-ttu-id="a2157-121">*Nota: gli utenti Linux/macOS devono apportare le modifiche appropriate`/`per i percorsi, ad esempio utilizzando la barra ( ) anziché la barra rovesciata (`\`).*</span><span class="sxs-lookup"><span data-stu-id="a2157-121">*Note: Linux/macOS users should make appropriate changes for paths, e.g., using forward slash (`/`) rather than back slash (`\`).*</span></span>

1. <span data-ttu-id="a2157-122">Clonare il codice in una cartella nel computer locale.</span><span class="sxs-lookup"><span data-stu-id="a2157-122">Clone the code to a folder on your local machine.</span></span>

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. <span data-ttu-id="a2157-123">Modificare la cartella di lavoro per la cartella *simple-feed-reader* che è stata creata.</span><span class="sxs-lookup"><span data-stu-id="a2157-123">Change your working folder to the *simple-feed-reader* folder that was created.</span></span>

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. <span data-ttu-id="a2157-124">Ripristinare i pacchetti e compilare la soluzione.</span><span class="sxs-lookup"><span data-stu-id="a2157-124">Restore the packages, and build the solution.</span></span>

    ```dotnetcli
    dotnet build
    ```

4. <span data-ttu-id="a2157-125">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="a2157-125">Run the app.</span></span>

    ```dotnetcli
    dotnet run
    ```

    ![Il comando dotnet run ha esito positivo](./media/deploying-to-app-service/dotnet-run.png)

5. <span data-ttu-id="a2157-127">Aprire un browser e passare a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="a2157-127">Open a browser and navigate to `http://localhost:5000`.</span></span> <span data-ttu-id="a2157-128">L'applicazione consente di digitare o incollare l'URL di un feed di diffusione e visualizzare un elenco di notizie.</span><span class="sxs-lookup"><span data-stu-id="a2157-128">The app allows you to type or paste a syndication feed URL and view a list of news items.</span></span>

     ![L'app che visualizza il contenuto di un feed RSS](./media/deploying-to-app-service/app-in-browser.png)

6. <span data-ttu-id="a2157-130">Quando si è certi che l'app funzioni correttamente, arrestarla premendo **Ctrl**+**C** nella shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="a2157-130">Once you're satisfied the app is working correctly, shut it down by pressing **Ctrl**+**C** in the command shell.</span></span>

## <a name="create-the-azure-app-service-web-app"></a><span data-ttu-id="a2157-131">Creare l'app Web del servizio app di AzureCreate the Azure App Service Web App</span><span class="sxs-lookup"><span data-stu-id="a2157-131">Create the Azure App Service Web App</span></span>

<span data-ttu-id="a2157-132">Per distribuire l'app, è necessario creare [un'app Web](/azure/app-service/app-service-web-overview)del servizio app.</span><span class="sxs-lookup"><span data-stu-id="a2157-132">To deploy the app, you'll need to create an App Service [Web App](/azure/app-service/app-service-web-overview).</span></span> <span data-ttu-id="a2157-133">Dopo la creazione dell'app Web, verrà distribuito dal computer locale utilizzando Git.</span><span class="sxs-lookup"><span data-stu-id="a2157-133">After creation of the Web App, you'll deploy to it from your local machine using Git.</span></span>

1. <span data-ttu-id="a2157-134">Accedere ad [Azure Cloud Shell](https://shell.azure.com/bash).</span><span class="sxs-lookup"><span data-stu-id="a2157-134">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash).</span></span> <span data-ttu-id="a2157-135">Nota: quando si accede per la prima volta, Cloud Shell richiede di creare un account di archiviazione per i file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="a2157-135">Note: When you sign in for the first time, Cloud Shell prompts to create a storage account for configuration files.</span></span> <span data-ttu-id="a2157-136">Accettare le impostazioni predefinite o fornire un nome univoco.</span><span class="sxs-lookup"><span data-stu-id="a2157-136">Accept the defaults or provide a unique name.</span></span>

2. <span data-ttu-id="a2157-137">Utilizzare Cloud Shell per la procedura seguente.</span><span class="sxs-lookup"><span data-stu-id="a2157-137">Use the Cloud Shell for the following steps.</span></span>

    <span data-ttu-id="a2157-138">a.</span><span class="sxs-lookup"><span data-stu-id="a2157-138">a.</span></span> <span data-ttu-id="a2157-139">Dichiarare una variabile per archiviare il nome dell'app Web.</span><span class="sxs-lookup"><span data-stu-id="a2157-139">Declare a variable to store your web app's name.</span></span> <span data-ttu-id="a2157-140">Il nome deve essere univoco per essere utilizzato nell'URL predefinito.</span><span class="sxs-lookup"><span data-stu-id="a2157-140">The name must be unique to be used in the default URL.</span></span> <span data-ttu-id="a2157-141">L'utilizzo della `$RANDOM` funzione Bash per costruire il nome `webappname99999`garantisce l'univocità e restituisce il formato .</span><span class="sxs-lookup"><span data-stu-id="a2157-141">Using the `$RANDOM` Bash function to construct the name guarantees uniqueness and results in the format `webappname99999`.</span></span>

    ```console
    webappname=mywebapp$RANDOM
    ```

    <span data-ttu-id="a2157-142">b.</span><span class="sxs-lookup"><span data-stu-id="a2157-142">b.</span></span> <span data-ttu-id="a2157-143">Creare un gruppo di risorse.</span><span class="sxs-lookup"><span data-stu-id="a2157-143">Create a resource group.</span></span> <span data-ttu-id="a2157-144">I gruppi di risorse consentono di aggregare le risorse di Azure da gestire come gruppo.</span><span class="sxs-lookup"><span data-stu-id="a2157-144">Resource groups provide a means to aggregate Azure resources to be managed as a group.</span></span>

    ```azurecli
    az group create --location centralus --name AzureTutorial
    ```

    <span data-ttu-id="a2157-145">Il `az` comando richiama l'interfaccia della riga di comando di [Azure.](/cli/azure/)</span><span class="sxs-lookup"><span data-stu-id="a2157-145">The `az` command invokes the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="a2157-146">L'interfaccia della riga di comando può essere eseguita in locale, ma l'utilizzo in Cloud Shell consente di risparmiare tempo e configurazione.</span><span class="sxs-lookup"><span data-stu-id="a2157-146">The CLI can be run locally, but using it in the Cloud Shell saves time and configuration.</span></span>

    <span data-ttu-id="a2157-147">c.</span><span class="sxs-lookup"><span data-stu-id="a2157-147">c.</span></span> <span data-ttu-id="a2157-148">Creare un piano di servizio app nel livello S1.</span><span class="sxs-lookup"><span data-stu-id="a2157-148">Create an App Service plan in the S1 tier.</span></span> <span data-ttu-id="a2157-149">Un piano di servizio app è un raggruppamento di app Web che condividono lo stesso piano tariffario.</span><span class="sxs-lookup"><span data-stu-id="a2157-149">An App Service plan is a grouping of web apps that share the same pricing tier.</span></span> <span data-ttu-id="a2157-150">Il livello S1 non è gratuito, ma è necessario per la funzionalità di slot di gestione temporanea.</span><span class="sxs-lookup"><span data-stu-id="a2157-150">The S1 tier isn't free, but it's required for the staging slots feature.</span></span>

    ```azurecli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    <span data-ttu-id="a2157-151">d.</span><span class="sxs-lookup"><span data-stu-id="a2157-151">d.</span></span> <span data-ttu-id="a2157-152">Creare la risorsa dell'app Web usando il piano di servizio app nello stesso gruppo di risorse.</span><span class="sxs-lookup"><span data-stu-id="a2157-152">Create the web app resource using the App Service plan in the same resource group.</span></span>

    ```azurecli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    <span data-ttu-id="a2157-153">e.</span><span class="sxs-lookup"><span data-stu-id="a2157-153">e.</span></span> <span data-ttu-id="a2157-154">Impostare le credenziali di distribuzione.</span><span class="sxs-lookup"><span data-stu-id="a2157-154">Set the deployment credentials.</span></span> <span data-ttu-id="a2157-155">Queste credenziali di distribuzione si applicano a tutte le app Web nella sottoscrizione.</span><span class="sxs-lookup"><span data-stu-id="a2157-155">These deployment credentials apply to all the web apps in your subscription.</span></span> <span data-ttu-id="a2157-156">Non utilizzare caratteri speciali nel nome utente.</span><span class="sxs-lookup"><span data-stu-id="a2157-156">Don't use special characters in the user name.</span></span>

    ```azurecli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    <span data-ttu-id="a2157-157">f.</span><span class="sxs-lookup"><span data-stu-id="a2157-157">f.</span></span> <span data-ttu-id="a2157-158">Configurare l'app Web in modo che accetti le distribuzioni da Git locale e visualizzi *l'URL di distribuzione Git*.</span><span class="sxs-lookup"><span data-stu-id="a2157-158">Configure the web app to accept deployments from local Git and display the *Git deployment URL*.</span></span> <span data-ttu-id="a2157-159">**Prendere nota di questo URL per riferimento in un secondo momento**.</span><span class="sxs-lookup"><span data-stu-id="a2157-159">**Note this URL for reference later**.</span></span>

    ```azurecli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    <span data-ttu-id="a2157-160">g.</span><span class="sxs-lookup"><span data-stu-id="a2157-160">g.</span></span> <span data-ttu-id="a2157-161">Visualizzare *l'URL dell'app Web*.</span><span class="sxs-lookup"><span data-stu-id="a2157-161">Display the *web app URL*.</span></span> <span data-ttu-id="a2157-162">Passare a questo URL per visualizzare l'app Web vuota.</span><span class="sxs-lookup"><span data-stu-id="a2157-162">Browse to this URL to see the blank web app.</span></span> <span data-ttu-id="a2157-163">**Prendere nota di questo URL per riferimento in un secondo momento**.</span><span class="sxs-lookup"><span data-stu-id="a2157-163">**Note this URL for reference later**.</span></span>

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. <span data-ttu-id="a2157-164">Utilizzando una shell dei comandi nel computer locale, passare alla cartella `.\simple-feed-reader\SimpleFeedReader`del progetto dell'app Web, ad esempio .</span><span class="sxs-lookup"><span data-stu-id="a2157-164">Using a command shell on your local machine, navigate to the web app's project folder (for example, `.\simple-feed-reader\SimpleFeedReader`).</span></span> <span data-ttu-id="a2157-165">Eseguire i comandi seguenti per impostare Git per il push all'URL di distribuzione:</span><span class="sxs-lookup"><span data-stu-id="a2157-165">Execute the following commands to set up Git to push to the deployment URL:</span></span>

    <span data-ttu-id="a2157-166">a.</span><span class="sxs-lookup"><span data-stu-id="a2157-166">a.</span></span> <span data-ttu-id="a2157-167">Aggiungere l'URL remoto al repository locale.</span><span class="sxs-lookup"><span data-stu-id="a2157-167">Add the remote URL to the local repository.</span></span>

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    <span data-ttu-id="a2157-168">b.</span><span class="sxs-lookup"><span data-stu-id="a2157-168">b.</span></span> <span data-ttu-id="a2157-169">Eseguire il push del ramo *master* locale nel ramo *master* del telecomando *azure-prod.*</span><span class="sxs-lookup"><span data-stu-id="a2157-169">Push the local *master* branch to the *azure-prod* remote's *master* branch.</span></span>

    ```console
    git push azure-prod master
    ```

    <span data-ttu-id="a2157-170">Verranno richieste le credenziali di distribuzione create in precedenza.</span><span class="sxs-lookup"><span data-stu-id="a2157-170">You'll be prompted for the deployment credentials you created earlier.</span></span> <span data-ttu-id="a2157-171">Osservare l'output nella shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="a2157-171">Observe the output in the command shell.</span></span> <span data-ttu-id="a2157-172">Azure crea l'app ASP.NET Core in remoto.</span><span class="sxs-lookup"><span data-stu-id="a2157-172">Azure builds the ASP.NET Core app remotely.</span></span>

4. <span data-ttu-id="a2157-173">In un browser passare *all'URL dell'app Web* e prendere nota che l'app è stata compilata e distribuita.</span><span class="sxs-lookup"><span data-stu-id="a2157-173">In a browser, navigate to the *Web app URL* and note the app has been built and deployed.</span></span> <span data-ttu-id="a2157-174">È possibile eseguire il commit di `git commit`ulteriori modifiche nel repository Git locale con .</span><span class="sxs-lookup"><span data-stu-id="a2157-174">Additional changes can be committed to the local Git repository with `git commit`.</span></span> <span data-ttu-id="a2157-175">Queste modifiche vengono inviate ad `git push` Azure con il comando precedente.</span><span class="sxs-lookup"><span data-stu-id="a2157-175">These changes are pushed to Azure with the preceding `git push` command.</span></span>

## <a name="deployment-with-visual-studio"></a><span data-ttu-id="a2157-176">Distribuzione con Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a2157-176">Deployment with Visual Studio</span></span>

> <span data-ttu-id="a2157-177">*Nota: questa sezione si applica solo a Windows. Gli utenti Linux e macOS devono apportare la modifica descritta nel passaggio 2 riportato di seguito. Salvare il file ed eseguire il commit `git commit`della modifica nel repository locale con . Infine, spingere la `git push`modifica con , come nella prima sezione.*</span><span class="sxs-lookup"><span data-stu-id="a2157-177">*Note: This section applies to Windows only. Linux and macOS users should make the change described in step 2 below. Save the file, and commit the change to the local repository with `git commit`. Finally, push the change with `git push`, as in the first section.*</span></span>

<span data-ttu-id="a2157-178">L'app è già stata distribuita dalla shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="a2157-178">The app has already been deployed from the command shell.</span></span> <span data-ttu-id="a2157-179">Usiamo gli strumenti integrati di Visual Studio per distribuire un aggiornamento all'app.</span><span class="sxs-lookup"><span data-stu-id="a2157-179">Let's use Visual Studio's integrated tools to deploy an update to the app.</span></span> <span data-ttu-id="a2157-180">Dietro le quinte, Visual Studio esegue la stessa operazione degli strumenti della riga di comando, ma all'interno dell'interfaccia utente familiare di Visual Studio.Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.</span><span class="sxs-lookup"><span data-stu-id="a2157-180">Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.</span></span>

1. <span data-ttu-id="a2157-181">Aprire SimpleFeedReader.sln in Visual Studio.Open *SimpleFeedReader.sln* in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a2157-181">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
2. <span data-ttu-id="a2157-182">In Esplora soluzioni, aprire *Pages-Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a2157-182">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="a2157-183">Cambiare `<h2>Simple Feed Reader</h2>` in `<h2>Simple Feed Reader - V2</h2>`.</span><span class="sxs-lookup"><span data-stu-id="a2157-183">Change `<h2>Simple Feed Reader</h2>` to `<h2>Simple Feed Reader - V2</h2>`.</span></span>
3. <span data-ttu-id="a2157-184">Premere **Ctrl**+**Maiusc**+**B** per creare l'app.</span><span class="sxs-lookup"><span data-stu-id="a2157-184">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
4. <span data-ttu-id="a2157-185">In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e **scegliere Pubblica**.</span><span class="sxs-lookup"><span data-stu-id="a2157-185">In Solution Explorer, right-click on the project and click **Publish**.</span></span>

    ![Schermata che mostra il pulsante destro del mouse, Pubblica](./media/deploying-to-app-service/publish.png)
5. <span data-ttu-id="a2157-187">Visual Studio può creare una nuova risorsa del servizio app, ma questo aggiornamento verrà pubblicato sulla distribuzione esistente.</span><span class="sxs-lookup"><span data-stu-id="a2157-187">Visual Studio can create a new App Service resource, but this update will be published over the existing deployment.</span></span> <span data-ttu-id="a2157-188">Nella finestra di dialogo **Seleziona una destinazione di pubblicazione** selezionare Servizio **app** dall'elenco a sinistra e quindi selezionare **Seleziona esistente**.</span><span class="sxs-lookup"><span data-stu-id="a2157-188">In the **Pick a publish target** dialog, select **App Service** from the list on the left, and then select **Select Existing**.</span></span> <span data-ttu-id="a2157-189">Fare clic su **Pubblica**.</span><span class="sxs-lookup"><span data-stu-id="a2157-189">Click **Publish**.</span></span>
6. <span data-ttu-id="a2157-190">Nella finestra di dialogo **Servizio app** verificare che l'account Microsoft o Dell'organizzazione usato per creare la sottoscrizione di Azure sia visualizzato in alto a destra.</span><span class="sxs-lookup"><span data-stu-id="a2157-190">In the **App Service** dialog, confirm that the Microsoft or Organizational account used to create your Azure subscription is displayed in the upper right.</span></span> <span data-ttu-id="a2157-191">In caso contrario, fare clic sul menu a discesa e aggiungerlo.</span><span class="sxs-lookup"><span data-stu-id="a2157-191">If it's not, click the drop-down and add it.</span></span>
7. <span data-ttu-id="a2157-192">Verificare che sia selezionata la **sottoscrizione** di Azure corretta.</span><span class="sxs-lookup"><span data-stu-id="a2157-192">Confirm that the correct Azure **Subscription** is selected.</span></span> <span data-ttu-id="a2157-193">Per **Visualizza**, selezionare **Gruppo di risorse**.</span><span class="sxs-lookup"><span data-stu-id="a2157-193">For **View**, select **Resource Group**.</span></span> <span data-ttu-id="a2157-194">Espandere il gruppo di risorse **AzureTutorial** e quindi selezionare l'app Web esistente.</span><span class="sxs-lookup"><span data-stu-id="a2157-194">Expand the **AzureTutorial** resource group and then select the existing web app.</span></span> <span data-ttu-id="a2157-195">Fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="a2157-195">Click **OK**.</span></span>

    ![Schermata della finestra di dialogo Pubblica servizio app](./media/deploying-to-app-service/publish-dialog.png)

<span data-ttu-id="a2157-197">Visual Studio builds and deploys the app to Azure.</span><span class="sxs-lookup"><span data-stu-id="a2157-197">Visual Studio builds and deploys the app to Azure.</span></span> <span data-ttu-id="a2157-198">Passare all'URL dell'app Web.</span><span class="sxs-lookup"><span data-stu-id="a2157-198">Browse to the web app URL.</span></span> <span data-ttu-id="a2157-199">Verificare `<h2>` che la modifica dell'elemento sia attiva.</span><span class="sxs-lookup"><span data-stu-id="a2157-199">Validate that the `<h2>` element modification is live.</span></span>

![L'app con il titolo modificato](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a><span data-ttu-id="a2157-201">Slot di distribuzione</span><span class="sxs-lookup"><span data-stu-id="a2157-201">Deployment slots</span></span>

<span data-ttu-id="a2157-202">Gli slot di distribuzione supportano la gestione temporanea delle modifiche senza influire sull'app in esecuzione nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="a2157-202">Deployment slots support the staging of changes without impacting the app running in production.</span></span> <span data-ttu-id="a2157-203">Una volta che la versione in fasi dell'app viene convalidata da un team di controllo qualità, gli slot di produzione e gestione temporanea possono essere scambiati.</span><span class="sxs-lookup"><span data-stu-id="a2157-203">Once the staged version of the app is validated by a quality assurance team, the production and staging slots can be swapped.</span></span> <span data-ttu-id="a2157-204">L'app nella gestione temporanea viene promossa alla produzione in questo modo.</span><span class="sxs-lookup"><span data-stu-id="a2157-204">The app in staging is promoted to production in this manner.</span></span> <span data-ttu-id="a2157-205">La procedura seguente consente di creare uno slot di staging, distribuirvi alcune modifiche e scambiare lo slot di staging con la produzione dopo la verifica.</span><span class="sxs-lookup"><span data-stu-id="a2157-205">The following steps create a staging slot, deploy some changes to it, and swap the staging slot with production after verification.</span></span>

1. <span data-ttu-id="a2157-206">Accedere ad [Azure Cloud Shell,](https://shell.azure.com/bash)se non è già stato eseguito l'accesso.</span><span class="sxs-lookup"><span data-stu-id="a2157-206">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash), if not already signed in.</span></span>
2. <span data-ttu-id="a2157-207">Creare lo slot di gestione temporanea.</span><span class="sxs-lookup"><span data-stu-id="a2157-207">Create the staging slot.</span></span>

    <span data-ttu-id="a2157-208">a.</span><span class="sxs-lookup"><span data-stu-id="a2157-208">a.</span></span> <span data-ttu-id="a2157-209">Creare uno slot di distribuzione con il nome *staging*.</span><span class="sxs-lookup"><span data-stu-id="a2157-209">Create a deployment slot with the name *staging*.</span></span>

    ```azurecli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    <span data-ttu-id="a2157-210">b.</span><span class="sxs-lookup"><span data-stu-id="a2157-210">b.</span></span> <span data-ttu-id="a2157-211">Configurare lo slot di gestione temporanea per usare la distribuzione da Git locale e ottenere l'URL di distribuzione di **gestione temporanea.**</span><span class="sxs-lookup"><span data-stu-id="a2157-211">Configure the staging slot to use deployment from local Git and get the **staging** deployment URL.</span></span> <span data-ttu-id="a2157-212">**Prendere nota di questo URL per riferimento in un secondo momento**.</span><span class="sxs-lookup"><span data-stu-id="a2157-212">**Note this URL for reference later**.</span></span>

    ```azurecli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    <span data-ttu-id="a2157-213">c.</span><span class="sxs-lookup"><span data-stu-id="a2157-213">c.</span></span> <span data-ttu-id="a2157-214">Visualizzare l'URL dello slot di gestione temporanea.</span><span class="sxs-lookup"><span data-stu-id="a2157-214">Display the staging slot's URL.</span></span> <span data-ttu-id="a2157-215">Passare all'URL per visualizzare lo slot di gestione temporanea vuoto.</span><span class="sxs-lookup"><span data-stu-id="a2157-215">Browse to the URL to see the empty staging slot.</span></span> <span data-ttu-id="a2157-216">**Prendere nota di questo URL per riferimento in un secondo momento**.</span><span class="sxs-lookup"><span data-stu-id="a2157-216">**Note this URL for reference later**.</span></span>

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. <span data-ttu-id="a2157-217">In un editor di testo o visual Studio, modificare `<h2>` *nuovamente Pages/Index.cshtml* in modo che l'elemento legga `<h2>Simple Feed Reader - V3</h2>` e salvi il file.</span><span class="sxs-lookup"><span data-stu-id="a2157-217">In a text editor or Visual Studio, modify *Pages/Index.cshtml* again so that the `<h2>` element reads `<h2>Simple Feed Reader - V3</h2>` and save the file.</span></span>

4. <span data-ttu-id="a2157-218">Eseguire il commit del file nel repository Git locale, utilizzando la pagina **Modifiche** nella scheda *Team Explorer* di Visual Studio o immettendo quanto segue utilizzando la shell dei comandi del computer locale:</span><span class="sxs-lookup"><span data-stu-id="a2157-218">Commit the file to the local Git repository, using either the **Changes** page in Visual Studio's *Team Explorer* tab, or by entering the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. <span data-ttu-id="a2157-219">Utilizzando la shell dei comandi del computer locale, aggiungere l'URL di distribuzione di gestione temporanea come remoto Git ed eseguire il push delle modifiche di cui è stato eseguito il commit:</span><span class="sxs-lookup"><span data-stu-id="a2157-219">Using the local machine's command shell, add the staging deployment URL as a Git remote and push the committed changes:</span></span>

    <span data-ttu-id="a2157-220">a.</span><span class="sxs-lookup"><span data-stu-id="a2157-220">a.</span></span> <span data-ttu-id="a2157-221">Aggiungere l'URL remoto per la gestione temporanea al repository Git locale.</span><span class="sxs-lookup"><span data-stu-id="a2157-221">Add the remote URL for staging to the local Git repository.</span></span>

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    <span data-ttu-id="a2157-222">b.</span><span class="sxs-lookup"><span data-stu-id="a2157-222">b.</span></span> <span data-ttu-id="a2157-223">Eseguire il push del ramo *master* locale nel ramo *master* del server di gestione remota *azure-staging.*</span><span class="sxs-lookup"><span data-stu-id="a2157-223">Push the local *master* branch to the *azure-staging* remote's *master* branch.</span></span>

    ```console
    git push azure-staging master
    ```

    <span data-ttu-id="a2157-224">Attendere mentre Azure compila e distribuisce l'app.</span><span class="sxs-lookup"><span data-stu-id="a2157-224">Wait while Azure builds and deploys the app.</span></span>

6. <span data-ttu-id="a2157-225">Per verificare che V3 sia stato distribuito nello slot di gestione temporanea, aprire due finestre del browser.</span><span class="sxs-lookup"><span data-stu-id="a2157-225">To verify that V3 has been deployed to the staging slot, open two browser windows.</span></span> <span data-ttu-id="a2157-226">In una finestra passare all'URL dell'app Web originale.</span><span class="sxs-lookup"><span data-stu-id="a2157-226">In one window, navigate to the original web app URL.</span></span> <span data-ttu-id="a2157-227">Nell'altra finestra passare all'URL dell'app Web di gestione temporanea.</span><span class="sxs-lookup"><span data-stu-id="a2157-227">In the other window, navigate to the staging web app URL.</span></span> <span data-ttu-id="a2157-228">L'URL di produzione serve V2 dell'app.</span><span class="sxs-lookup"><span data-stu-id="a2157-228">The production URL serves V2 of the app.</span></span> <span data-ttu-id="a2157-229">L'URL di gestione temporanea serve V3 dell'app.</span><span class="sxs-lookup"><span data-stu-id="a2157-229">The staging URL serves V3 of the app.</span></span>

    ![Schermata che confronta le finestre del browser](./media/deploying-to-app-service/ready-to-swap.png)

7. <span data-ttu-id="a2157-231">In Cloud Shell, scambiare lo slot di gestione temporanea verificato/riscaldato nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="a2157-231">In the Cloud Shell, swap the verified/warmed-up staging slot into production.</span></span>

    ```azurecli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. <span data-ttu-id="a2157-232">Verificare che lo scambio sia stato verificato aggiornando le due finestre del browser.</span><span class="sxs-lookup"><span data-stu-id="a2157-232">Verify that the swap occurred by refreshing the two browser windows.</span></span>

    ![Confrontando le finestre del browser dopo lo scambio](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a><span data-ttu-id="a2157-234">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="a2157-234">Summary</span></span>

<span data-ttu-id="a2157-235">In questa sezione sono state completate le attività seguenti:In this section, the following tasks were completed:</span><span class="sxs-lookup"><span data-stu-id="a2157-235">In this section, the following tasks were completed:</span></span>

* <span data-ttu-id="a2157-236">Scaricata e creata l'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="a2157-236">Downloaded and built the sample app.</span></span>
* <span data-ttu-id="a2157-237">È stata creata un'app Web del servizio app di Azure usando Azure Cloud Shell.Created an Azure App Service Web App using the Azure Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="a2157-237">Created an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="a2157-238">L'app di esempio è stata distribuita in Azure usando Git.Deployed the sample app to Azure using Git.</span><span class="sxs-lookup"><span data-stu-id="a2157-238">Deployed the sample app to Azure using Git.</span></span>
* <span data-ttu-id="a2157-239">Distribuita una modifica all'app usando Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a2157-239">Deployed a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="a2157-240">È stato aggiunto uno slot di gestione temporanea all'app Web.</span><span class="sxs-lookup"><span data-stu-id="a2157-240">Added a staging slot to the web app.</span></span>
* <span data-ttu-id="a2157-241">È stato distribuito un aggiornamento allo slot di gestione temporanea.</span><span class="sxs-lookup"><span data-stu-id="a2157-241">Deployed an update to the staging slot.</span></span>
* <span data-ttu-id="a2157-242">Scambiato gli slot di messa in scena e produzione.</span><span class="sxs-lookup"><span data-stu-id="a2157-242">Swapped the staging and production slots.</span></span>

<span data-ttu-id="a2157-243">Nella sezione successiva verrà illustrato come compilare una pipeline DevOps con pipeline di Azure.In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.</span><span class="sxs-lookup"><span data-stu-id="a2157-243">In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="a2157-244">Informazioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a2157-244">Additional reading</span></span>

* [<span data-ttu-id="a2157-245">Panoramica di App Web</span><span class="sxs-lookup"><span data-stu-id="a2157-245">Web Apps overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="a2157-246">Creare un'app Web .NET Core e database SQL nel Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="a2157-246">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [<span data-ttu-id="a2157-247">Configurazione delle credenziali per la distribuzione del Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="a2157-247">Configure deployment credentials for Azure App Service</span></span>](/azure/app-service/app-service-deployment-credentials)
* [<span data-ttu-id="a2157-248">Configurare gli ambienti di gestione temporanea nel Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="a2157-248">Set up staging environments in Azure App Service</span></span>](/azure/app-service/web-sites-staged-publishing)
