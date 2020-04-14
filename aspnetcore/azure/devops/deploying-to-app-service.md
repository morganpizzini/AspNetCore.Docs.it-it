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
# <a name="deploy-an-app-to-app-service"></a>Distribuire un'app nel servizio appDeploy an app to App Service

[Il servizio app di Azure](/azure/app-service/) è la piattaforma di hosting Web di Azure.Azure App Service is Azure's web hosting platform. La distribuzione di un'app Web nel servizio app di Azure può essere eseguita manualmente o tramite un processo automatizzato. In questa sezione della guida vengono illustrati i metodi di distribuzione che possono essere attivati manualmente o tramite script tramite la riga di comando o manualmente tramite Visual Studio.

In questa sezione verranno eseguite le attività seguenti:In this section, you'll accomplish the following tasks:

* Scaricare e compilare l'app di esempio.
* Creare un'app Web del servizio app di Azure usando Azure Cloud Shell.Create an Azure App Service Web App using the Azure Cloud Shell.
* Distribuire l'app di esempio in Azure usando Git.Deploy the sample app to Azure using Git.
* Distribuire una modifica all'app usando Visual Studio.Deploy a change to the app using Visual Studio.
* Aggiungere uno slot di gestione temporanea all'app Web.
* Distribuire un aggiornamento allo slot di gestione temporanea.
* Scambiare gli slot di staging e di produzione.

## <a name="download-and-test-the-app"></a>Scarica e testa l'app

L'app utilizzata in questa guida è un'app ASP.NET Core predefinita, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/). Si tratta di un'app Razor Pages che usa l'API `Microsoft.SyndicationFeed.ReaderWriter` per recuperare un feed RSS/Atom e visualizzare le voci di notizie in un elenco.

Sentitevi liberi di rivedere il codice, ma è importante capire che non c'è niente di speciale in questa applicazione. È solo una semplice app ASP.NET Core a scopo illustrativo.

Da una shell dei comandi, scaricare il codice, compilare il progetto ed eseguirlo come segue.

> *Nota: gli utenti Linux/macOS devono apportare le modifiche appropriate`/`per i percorsi, ad esempio utilizzando la barra ( ) anziché la barra rovesciata (`\`).*

1. Clonare il codice in una cartella nel computer locale.

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. Modificare la cartella di lavoro per la cartella *simple-feed-reader* che è stata creata.

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. Ripristinare i pacchetti e compilare la soluzione.

    ```dotnetcli
    dotnet build
    ```

4. Eseguire l'app.

    ```dotnetcli
    dotnet run
    ```

    ![Il comando dotnet run ha esito positivo](./media/deploying-to-app-service/dotnet-run.png)

5. Aprire un browser e passare a `http://localhost:5000`. L'applicazione consente di digitare o incollare l'URL di un feed di diffusione e visualizzare un elenco di notizie.

     ![L'app che visualizza il contenuto di un feed RSS](./media/deploying-to-app-service/app-in-browser.png)

6. Quando si è certi che l'app funzioni correttamente, arrestarla premendo **Ctrl**+**C** nella shell dei comandi.

## <a name="create-the-azure-app-service-web-app"></a>Creare l'app Web del servizio app di AzureCreate the Azure App Service Web App

Per distribuire l'app, è necessario creare [un'app Web](/azure/app-service/app-service-web-overview)del servizio app. Dopo la creazione dell'app Web, verrà distribuito dal computer locale utilizzando Git.

1. Accedere ad [Azure Cloud Shell](https://shell.azure.com/bash). Nota: quando si accede per la prima volta, Cloud Shell richiede di creare un account di archiviazione per i file di configurazione. Accettare le impostazioni predefinite o fornire un nome univoco.

2. Utilizzare Cloud Shell per la procedura seguente.

    a. Dichiarare una variabile per archiviare il nome dell'app Web. Il nome deve essere univoco per essere utilizzato nell'URL predefinito. L'utilizzo della `$RANDOM` funzione Bash per costruire il nome `webappname99999`garantisce l'univocità e restituisce il formato .

    ```console
    webappname=mywebapp$RANDOM
    ```

    b. Creare un gruppo di risorse. I gruppi di risorse consentono di aggregare le risorse di Azure da gestire come gruppo.

    ```azurecli
    az group create --location centralus --name AzureTutorial
    ```

    Il `az` comando richiama l'interfaccia della riga di comando di [Azure.](/cli/azure/) L'interfaccia della riga di comando può essere eseguita in locale, ma l'utilizzo in Cloud Shell consente di risparmiare tempo e configurazione.

    c. Creare un piano di servizio app nel livello S1. Un piano di servizio app è un raggruppamento di app Web che condividono lo stesso piano tariffario. Il livello S1 non è gratuito, ma è necessario per la funzionalità di slot di gestione temporanea.

    ```azurecli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    d. Creare la risorsa dell'app Web usando il piano di servizio app nello stesso gruppo di risorse.

    ```azurecli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    e. Impostare le credenziali di distribuzione. Queste credenziali di distribuzione si applicano a tutte le app Web nella sottoscrizione. Non utilizzare caratteri speciali nel nome utente.

    ```azurecli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    f. Configurare l'app Web in modo che accetti le distribuzioni da Git locale e visualizzi *l'URL di distribuzione Git*. **Prendere nota di questo URL per riferimento in un secondo momento**.

    ```azurecli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    g. Visualizzare *l'URL dell'app Web*. Passare a questo URL per visualizzare l'app Web vuota. **Prendere nota di questo URL per riferimento in un secondo momento**.

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. Utilizzando una shell dei comandi nel computer locale, passare alla cartella `.\simple-feed-reader\SimpleFeedReader`del progetto dell'app Web, ad esempio . Eseguire i comandi seguenti per impostare Git per il push all'URL di distribuzione:

    a. Aggiungere l'URL remoto al repository locale.

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    b. Eseguire il push del ramo *master* locale nel ramo *master* del telecomando *azure-prod.*

    ```console
    git push azure-prod master
    ```

    Verranno richieste le credenziali di distribuzione create in precedenza. Osservare l'output nella shell dei comandi. Azure crea l'app ASP.NET Core in remoto.

4. In un browser passare *all'URL dell'app Web* e prendere nota che l'app è stata compilata e distribuita. È possibile eseguire il commit di `git commit`ulteriori modifiche nel repository Git locale con . Queste modifiche vengono inviate ad `git push` Azure con il comando precedente.

## <a name="deployment-with-visual-studio"></a>Distribuzione con Visual Studio

> *Nota: questa sezione si applica solo a Windows. Gli utenti Linux e macOS devono apportare la modifica descritta nel passaggio 2 riportato di seguito. Salvare il file ed eseguire il commit `git commit`della modifica nel repository locale con . Infine, spingere la `git push`modifica con , come nella prima sezione.*

L'app è già stata distribuita dalla shell dei comandi. Usiamo gli strumenti integrati di Visual Studio per distribuire un aggiornamento all'app. Dietro le quinte, Visual Studio esegue la stessa operazione degli strumenti della riga di comando, ma all'interno dell'interfaccia utente familiare di Visual Studio.Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.

1. Aprire SimpleFeedReader.sln in Visual Studio.Open *SimpleFeedReader.sln* in Visual Studio.
2. In Esplora soluzioni, aprire *Pages-Index.cshtml*. Cambiare `<h2>Simple Feed Reader</h2>` in `<h2>Simple Feed Reader - V2</h2>`.
3. Premere **Ctrl**+**Maiusc**+**B** per creare l'app.
4. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e **scegliere Pubblica**.

    ![Schermata che mostra il pulsante destro del mouse, Pubblica](./media/deploying-to-app-service/publish.png)
5. Visual Studio può creare una nuova risorsa del servizio app, ma questo aggiornamento verrà pubblicato sulla distribuzione esistente. Nella finestra di dialogo **Seleziona una destinazione di pubblicazione** selezionare Servizio **app** dall'elenco a sinistra e quindi selezionare **Seleziona esistente**. Fare clic su **Pubblica**.
6. Nella finestra di dialogo **Servizio app** verificare che l'account Microsoft o Dell'organizzazione usato per creare la sottoscrizione di Azure sia visualizzato in alto a destra. In caso contrario, fare clic sul menu a discesa e aggiungerlo.
7. Verificare che sia selezionata la **sottoscrizione** di Azure corretta. Per **Visualizza**, selezionare **Gruppo di risorse**. Espandere il gruppo di risorse **AzureTutorial** e quindi selezionare l'app Web esistente. Fare clic su **OK**.

    ![Schermata della finestra di dialogo Pubblica servizio app](./media/deploying-to-app-service/publish-dialog.png)

Visual Studio builds and deploys the app to Azure. Passare all'URL dell'app Web. Verificare `<h2>` che la modifica dell'elemento sia attiva.

![L'app con il titolo modificato](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a>Slot di distribuzione

Gli slot di distribuzione supportano la gestione temporanea delle modifiche senza influire sull'app in esecuzione nell'ambiente di produzione. Una volta che la versione in fasi dell'app viene convalidata da un team di controllo qualità, gli slot di produzione e gestione temporanea possono essere scambiati. L'app nella gestione temporanea viene promossa alla produzione in questo modo. La procedura seguente consente di creare uno slot di staging, distribuirvi alcune modifiche e scambiare lo slot di staging con la produzione dopo la verifica.

1. Accedere ad [Azure Cloud Shell,](https://shell.azure.com/bash)se non è già stato eseguito l'accesso.
2. Creare lo slot di gestione temporanea.

    a. Creare uno slot di distribuzione con il nome *staging*.

    ```azurecli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    b. Configurare lo slot di gestione temporanea per usare la distribuzione da Git locale e ottenere l'URL di distribuzione di **gestione temporanea.** **Prendere nota di questo URL per riferimento in un secondo momento**.

    ```azurecli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    c. Visualizzare l'URL dello slot di gestione temporanea. Passare all'URL per visualizzare lo slot di gestione temporanea vuoto. **Prendere nota di questo URL per riferimento in un secondo momento**.

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. In un editor di testo o visual Studio, modificare `<h2>` *nuovamente Pages/Index.cshtml* in modo che l'elemento legga `<h2>Simple Feed Reader - V3</h2>` e salvi il file.

4. Eseguire il commit del file nel repository Git locale, utilizzando la pagina **Modifiche** nella scheda *Team Explorer* di Visual Studio o immettendo quanto segue utilizzando la shell dei comandi del computer locale:

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. Utilizzando la shell dei comandi del computer locale, aggiungere l'URL di distribuzione di gestione temporanea come remoto Git ed eseguire il push delle modifiche di cui è stato eseguito il commit:

    a. Aggiungere l'URL remoto per la gestione temporanea al repository Git locale.

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    b. Eseguire il push del ramo *master* locale nel ramo *master* del server di gestione remota *azure-staging.*

    ```console
    git push azure-staging master
    ```

    Attendere mentre Azure compila e distribuisce l'app.

6. Per verificare che V3 sia stato distribuito nello slot di gestione temporanea, aprire due finestre del browser. In una finestra passare all'URL dell'app Web originale. Nell'altra finestra passare all'URL dell'app Web di gestione temporanea. L'URL di produzione serve V2 dell'app. L'URL di gestione temporanea serve V3 dell'app.

    ![Schermata che confronta le finestre del browser](./media/deploying-to-app-service/ready-to-swap.png)

7. In Cloud Shell, scambiare lo slot di gestione temporanea verificato/riscaldato nell'ambiente di produzione.

    ```azurecli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. Verificare che lo scambio sia stato verificato aggiornando le due finestre del browser.

    ![Confrontando le finestre del browser dopo lo scambio](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a>Riepilogo

In questa sezione sono state completate le attività seguenti:In this section, the following tasks were completed:

* Scaricata e creata l'app di esempio.
* È stata creata un'app Web del servizio app di Azure usando Azure Cloud Shell.Created an Azure App Service Web App using the Azure Cloud Shell.
* L'app di esempio è stata distribuita in Azure usando Git.Deployed the sample app to Azure using Git.
* Distribuita una modifica all'app usando Visual Studio.
* È stato aggiunto uno slot di gestione temporanea all'app Web.
* È stato distribuito un aggiornamento allo slot di gestione temporanea.
* Scambiato gli slot di messa in scena e produzione.

Nella sezione successiva verrà illustrato come compilare una pipeline DevOps con pipeline di Azure.In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.

## <a name="additional-reading"></a>Informazioni aggiuntive

* [Panoramica di App Web](/azure/app-service/app-service-web-overview)
* [Creare un'app Web .NET Core e database SQL nel Servizio app di Azure](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [Configurazione delle credenziali per la distribuzione del Servizio app di Azure](/azure/app-service/app-service-deployment-credentials)
* [Configurare gli ambienti di gestione temporanea nel Servizio app di Azure](/azure/app-service/web-sites-staged-publishing)
