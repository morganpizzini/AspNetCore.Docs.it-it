---
title: Integrazione e distribuzione continue - DevOps con ASP.NET Core e Azure
author: CamSoper
description: Integrazione e distribuzione continua in DevOps con ASP.NET Core e Azure
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: mvc, seodec18
uid: azure/devops/cicd
ms.openlocfilehash: 5fdf52235b49119503885f92c370dc588e809ffe
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655834"
---
# <a name="continuous-integration-and-deployment"></a>Integrazione e distribuzione continue

Nel capitolo precedente è stato creato un repository Git locale per l'app Lettore feed semplice. In this chapter, you'll publish that code to a GitHub repository and construct an Azure DevOps Services pipeline using Azure Pipelines. La pipeline abilita compilazioni e distribuzioni continue dell'app. Qualsiasi commit nel repository GitHub attiva una compilazione e una distribuzione nello slot di gestione temporanea dell'app Web di Azure.Any commit to the GitHub repository triggers a build and a deployment to the Azure Web App's staging slot.

In questa sezione verranno completate le attività seguenti:In this section, you'll complete the following tasks:

* Pubblicare il codice dell'app in GitHubPublish the app's code to GitHub
* Disconnettere la distribuzione Git locale
* Creare un'organizzazione di Azure DevOps
* Creare un progetto team nei servizi DevOps di AzureCreate a team project in Azure DevOps Services
* Creare una definizione di compilazione
* Creare una pipeline di versione
* Eseguire il commit delle modifiche in GitHub e la distribuzione automatica in Azure
* Esaminare la pipeline di Azure PipelinesExamine the Azure Pipelines pipeline

## <a name="publish-the-apps-code-to-github"></a>Pubblicare il codice dell'app in GitHubPublish the app's code to GitHub

1. Aprire una finestra del `https://github.com`browser e passare a .
1. Fare **+** clic sull'elenco a discesa nell'intestazione e selezionare **Nuovo repository:**

    ![Opzione del nuovo repository GitHub](media/cicd/github-new-repo.png)

1. Selezionare l'account nell'elenco a discesa **Proprietario** e immettere *simple-feed-reader* nella casella di testo **Nome repository.**
1. Fare clic sul pulsante **Crea repository.**
1. Aprire la shell dei comandi del computer locale. Passare alla directory in cui è archiviato il repository Git *di simple-feed-reader.*
1. Rinominare il telecomando di *origine* esistente *in upstream*. Eseguire il comando seguente:

    ```console
    git remote rename origin upstream
    ```

1. Aggiungere una nuova versione remota di origine che punta alla copia del repository in GitHub.Add a new *origin* remote pointing to your copy of the repository on GitHub. Eseguire il comando seguente:

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. Pubblicare il repository Git locale nel repository GitHub appena creato. Eseguire il comando seguente:

    ```console
    git push -u origin master
    ```

1. Aprire una finestra del `https://github.com/<GitHub_username>/simple-feed-reader/`browser e passare a . Verificare che il codice venga visualizzato nel repository GitHub.Validate that your code appears in the GitHub repository.

## <a name="disconnect-local-git-deployment"></a>Disconnettere la distribuzione Git locale

Rimuovere la distribuzione Git locale con i passaggi seguenti. Pipeline di Azure (un servizio DevOps di Azure) sostituisce e aumenta tale funzionalità.

1. Aprire il [portale di Azure](https://portal.azure.com/)e passare all'app Web di gestione temporanea *(mywebapp\<unique_number\>/staging).* È possibile individuarla rapidamente immettendo la *gestione temporanea* nella casella di ricerca del portale:

    ![termine di ricerca di Gestione temporanea app Web](media/cicd/portal-search-box.png)

1. Fare clic su **Centro distribuzione**. Viene visualizzato un nuovo pannello. Fare clic su **Disconnetti** per rimuovere la configurazione del controllo del codice sorgente Git locale aggiunta nel capitolo precedente. Confermare l'operazione di rimozione facendo clic sul pulsante **Sì.**
1. Passare al *<mywebapp unique_number>* servizio app. Come promemoria, la casella di ricerca del portale può essere utilizzata per individuare rapidamente il servizio app.
1. Fare clic su **Centro distribuzione**. Viene visualizzato un nuovo pannello. Fare clic su **Disconnetti** per rimuovere la configurazione del controllo del codice sorgente Git locale aggiunta nel capitolo precedente. Confermare l'operazione di rimozione facendo clic sul pulsante **Sì.**

## <a name="create-an-azure-devops-organization"></a>Creare un'organizzazione di Azure DevOps

1. Aprire un browser e passare alla pagina di [creazione dell'organizzazione DevOps di Azure.](https://go.microsoft.com/fwlink/?LinkId=307137)
1. Digitare un nome univoco nella casella di testo **Selezionare un nome come memorabl** per formare l'URL per l'accesso all'organizzazione DevOps di Azure.Type a unique name into the Pick a memorable name text box to form the URL for accessing your Azure DevOps organization.
1. Selezionare il pulsante di opzione **Git,** poiché il codice è ospitato in un repository GitHub.Select the Git radio button, since the code is hosted in a GitHub repository.
1. Fare clic sul pulsante **Continua**. Dopo una breve attesa, vengono creati un account e un progetto team, denominato *MyFirstProject*.

    ![Pagina di creazione dell'organizzazione DevOps di AzureAzure DevOps organization creation page](media/cicd/vsts-account-creation.png)

1. Aprire l'e-mail di conferma che indica che l'organizzazione devOps di Azure e il progetto sono pronti per l'uso. Fare clic sul **pulsante Avvia il progetto:**

    ![Pulsante Avvia il progetto](media/cicd/vsts-start-project.png)

1. Viene aperto un browser in * \<\>account_name .visualstudio.com*. Fare clic sul collegamento *MyFirstProject* per iniziare a configurare la pipeline DevOps del progetto.

## <a name="configure-the-azure-pipelines-pipeline"></a>Configurare la pipeline delle pipeline di AzureConfigure the Azure Pipelines pipeline

Ci sono tre passaggi distinti da completare. Il completamento dei passaggi nelle tre sezioni seguenti comporta una pipeline DevOps operativa.

### <a name="grant-azure-devops-access-to-the-github-repository"></a>Concedere l'accesso DevOps di Azure al repository GitHubGrant Azure DevOps access to the GitHub repository

1. Espandere il **codice o compilarlo da** una fisarmonica di repository esterni. Fare clic sul pulsante **Imposta compilazione:**

    ![Pulsante Crea](media/cicd/vsts-setup-build.png)

1. Selezionare l'opzione GitHub dalla sezione **Selezionare un'origine:Select** the **GitHub** option from the Select a source section:

    ![Selezionare un'origine - GitHubSelect a source - GitHub](media/cicd/vsts-select-source.png)

1. L'autorizzazione è necessaria prima che DevOps di Azure possa accedere al repository GitHub.Authorization is required before Azure DevOps can access your GitHub repository. Immettere *<GitHub_username> connessione GitHub* nella casella di testo Nome **connessione.** Ad esempio:

    ![Nome connessione GitHub](media/cicd/vsts-repo-authz.png)

1. Se l'autenticazione a due fattori è abilitata nell'account GitHub, è necessario un token di accesso personale. In tal caso, fare clic sul collegamento Autorizza con un token di accesso personale GitHub.In that case, click the **Authorize with a GitHub personal access token** link. Per assistenza, vedere le istruzioni ufficiali per la creazione di token di accesso personale GitHub.See the [official GitHub personal access token creation instructions](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) for help. È necessario solo l'ambito di *repository* delle autorizzazioni. In caso contrario, fare clic sul pulsante **Autorizza tramite OAuth.**
1. Quando richiesto, accedi al tuo account GitHub. Selezionare quindi Autorizza per concedere l'accesso all'organizzazione DevOps di Azure.Then select Authorize to grant access to your Azure DevOps organization. In caso di esito positivo, viene creato un nuovo endpoint del servizio.
1. Fare clic sul pulsante con i conielli accanto al pulsante **Repository.** Selezionare il *repository<GitHub_username>/semplice-feed-reader* dall'elenco. Fare clic sul pulsante **Seleziona**.
1. Selezionare il ramo *master* dal **ramo predefinito per le compilazioni manuali e pianificate.** Fare clic sul pulsante **Continua**. Viene visualizzata la pagina di selezione del modello.

### <a name="create-the-build-definition"></a>Creare la definizione di compilazioneCreate the build definition

1. Nella pagina di selezione del modello, inserisci *ASP.NET Core* nella casella di ricerca:

    ![ASP.NET Core ricerca nella pagina del modello](media/cicd/vsts-template-selection.png)

1. Vengono visualizzati i risultati della ricerca del modello. Passare il mouse sul modello **ASP.NET Core** e fare clic sul pulsante **Applica.**
1. Viene visualizzata la scheda **Attività** della definizione di compilazione. Fare clic sulla scheda **Trigger.**
1. Selezionare la casella **Abilita integrazione continua.** Nella sezione **Filtri ramo** verificare che l'elenco a discesa **Tipo** sia impostato su *Includi*. Impostare il menu a discesa **Specifica ramo** su *master*.

    ![Abilitare le impostazioni di integrazione continua](media/cicd/vsts-enable-ci.png)

    Queste impostazioni causano il trigger di una compilazione quando qualsiasi modifica viene inserita nel ramo master del repository GitHub.These settings cause a build to trigger when any change is pushed to the *master* branch of the GitHub repository. L'integrazione continua viene testata nella sezione Commit delle modifiche in GitHub e distribuita automaticamente in Azure.Continuous integration is tested in the [Commit changes to GitHub and automatically deploy to Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) section.

1. Fare clic sul pulsante **Salva coda &** e selezionare l'opzione **Salva:**

    ![Pulsante per il salvataggio](media/cicd/vsts-save-build.png)

1. Viene visualizzata la seguente finestra di dialogo modale:

    ![Salva definizione di compilazione - finestra di dialogo modale](media/cicd/vsts-save-modal.png)

    Utilizzare la cartella *\\*predefinita di , quindi fare clic sul pulsante **Salva.**

### <a name="create-the-release-pipeline"></a>Creare la pipeline di versione

1. Fare clic sulla scheda **Rilasci** del progetto team. Fare clic sul pulsante **Nuova pipeline.**

    ![Scheda Rilasci - Pulsante Nuova definizione](media/cicd/vsts-new-release-definition.png)

    Viene visualizzato il riquadro di selezione del modello.

1. Nella pagina di selezione del modello immettere *Servizio app* nella casella di ricerca:

    ![Casella di ricerca modello di pipeline di rilascioRelease pipeline template search box](media/cicd/vsts-release-template-search.png)

1. Vengono visualizzati i risultati della ricerca del modello. Passare il puntatore del mouse sul modello **Distribuzione del servizio app di Azure con slot** e fare clic sul pulsante **Applica.Click** over the Azure App Service Deployment with Slot template, and click the Apply button. Viene visualizzata la scheda **Pipeline** della pipeline di rilascio.

    ![Scheda Pipeline di rilascio](media/cicd/vsts-release-definition-pipeline.png)

1. Fare clic sul pulsante **Aggiungi** nella casella **Elementi.** Viene visualizzato il pannello **Aggiungi elemento:**

    ![Pipeline di rilascio - Aggiungi pannello ArtefactRelease pipeline - Add artifact panel](media/cicd/vsts-release-add-artifact.png)

1. Selezionare il riquadro **Compila** nella sezione **Tipo di origine.** Questo tipo consente il collegamento della pipeline di rilascio alla definizione di compilazione.
1. Selezionare *MyFirstProject* dall'elenco a discesa **Progetto.**
1. Selezionare il nome della definizione di compilazione, *MyFirstProject-ASP.NET Core-CI*, dall'elenco a discesa **Origine (definizione di compilazione).**
1. Selezionare *Più recente* dall'elenco a discesa **Versione predefinita.** Questa opzione consente di compilare gli elementi prodotti dall'ultima esecuzione della definizione di compilazione.
1. Sostituire il testo nella casella di testo Alias di **origine** con *Drop*.
1. Fare clic sul pulsante **Aggiungi**. La sezione **Artefatti** viene aggiornata per visualizzare le modifiche.
1. Fare clic sull'icona del fulmine per abilitare le distribuzioni continue:

    ![Elementi della pipeline di rilascio - icona del fulmine](media/cicd/vsts-artifacts-lightning-bolt.png)

    Con questa opzione abilitata, viene eseguita una distribuzione ogni volta che è disponibile una nuova compilazione.
1. A destra viene visualizzato un pannello **di attivazione della distribuzione continua.** Fare clic sul pulsante di attivazione/disattivazione per attivare la funzione. Non è necessario abilitare il trigger di **richiesta Pull**.
1. Fare clic sull'elenco a discesa **Aggiungi** nella sezione **Filtri di compilazione ramo.** Scegliere l'opzione di **diramazione predefinita della definizione** di compilazione. Questo filtro fa sì che il rilascio venga attivato solo per una compilazione dal ramo master del repository GitHub.This filter causes the release to trigger only for a build from the GitHub repository's *master* branch.
1. Fare clic su **Salva**. Fare clic sul pulsante **OK** nella finestra di dialogo modale **Salva** risultante.
1. Fare clic sulla casella **Ambiente 1.** A destra viene visualizzato un pannello **Ambiente.** Modificare il testo *Ambiente 1* nella casella di testo **Nome ambiente** in *Produzione*.

   ![Pipeline di rilascio - Casella di testo Nome ambienteRelease pipeline - Environment name textbox](media/cicd/vsts-environment-name-textbox.png)

1. Fare clic sul collegamento **1 fase, 2 attività** nella casella **Produzione:**

    ![Pipeline di rilascio - Collegamento ambiente di produzione.pngRelease pipeline - Production environment link.png](media/cicd/vsts-production-link.png)

    Viene visualizzata la scheda **Attività** dell'ambiente.
1. Fare clic sull'attività Distribuisci servizio app di Azure in Slot.Click the **Deploy Azure App Service to Slot** task. Le sue impostazioni vengono visualizzate in un pannello a destra.
1. Selezionare la sottoscrizione di Azure associata al servizio app dall'elenco a discesa Sottoscrizione di Azure.Select the Azure subscription associated with the App Service from the **Azure subscription drop-down.** Una volta selezionato, fare clic sul pulsante **Autorizza.**
1. Selezionare *App Web* dall'elenco a discesa Tipo di **app.**
1. Selezionare *mywebapp/<unique_number/>* nell'elenco a discesa **Nome servizio app.**
1. Selezionare *AzureTutorial* dall'elenco a discesa **Gruppo di** risorse.
1. Selezionare *staging* dall'elenco a discesa **Slot.**
1. Fare clic su **Salva**.
1. Posizionare il puntatore del mouse sul nome della pipeline di rilascio predefinita. Fare clic sull'icona a matita per modificarla. Utilizzare *MyFirstProject-ASP.NET Core-CD* come nome.

    ![Nome pipeline di rilascio](media/cicd/vsts-release-definition-name.png)

1. Fare clic su **Salva**.

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>Eseguire il commit delle modifiche in GitHub e la distribuzione automatica in Azure

1. Aprire SimpleFeedReader.sln in Visual Studio.Open *SimpleFeedReader.sln* in Visual Studio.
1. In Esplora soluzioni, aprire *Pages-Index.cshtml*. Cambiare `<h2>Simple Feed Reader - V3</h2>` in `<h2>Simple Feed Reader - V4</h2>`.
1. Premere **Ctrl**+**Maiusc**+**B** per creare l'app.
1. Eseguire il commit del file nel repository GitHub. Utilizzare la pagina **Modifiche** nella scheda *Team Explorer* di Visual Studio oppure eseguire le operazioni seguenti utilizzando la shell dei comandi del computer locale:

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. Eseguire il push della modifica nel ramo *master* nel telecomando di *origine* del repository GitHub:

    ```console
    git push origin master
    ```

    Il commit viene visualizzato nel ramo master del repository GitHub:The commit appears in the GitHub repository's *master* branch:

    ![Impegno GitHub nel ramo master](media/cicd/github-commit.png)

    La compilazione viene attivata, poiché l'integrazione continua è abilitata nella scheda Trigger della definizione di compilazione:The build is triggered, since continuous integration is enabled in the build definition's **Triggers** tab:

    ![consentire l'integrazione continua](media/cicd/enable-ci.png)

1. Passare alla scheda In coda della pagina**Compilazioni** pipeline di Azure nei servizi DevOps di Azure.Navigate to the **Queued** tab of the Azure **Pipelines Builds** > page in Azure DevOps Services. La compilazione in coda mostra il ramo e il commit che hanno attivato la compilazione:The queued build shows the branch and commit that triggered the build:

    ![compilazione in coda](media/cicd/build-queued.png)

1. Una volta completata la compilazione, si verifica una distribuzione in Azure.Once the build succeeds, a deployment to Azure occurs. Passare all'app nel browser. Si noti che il testo "V4" viene visualizzato nell'intestazione:

    ![app aggiornata](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a>Esaminare la pipeline di Azure PipelinesExamine the Azure Pipelines pipeline

### <a name="build-definition"></a>Definizione di compilazione

È stata creata una definizione di compilazione con il nome *MyFirstProject-ASP.NET Core-CI*. Al termine, la compilazione produce un file *.zip* che include le risorse da pubblicare. La pipeline di rilascio distribuisce tali asset in Azure.The release pipeline deploys those assets to Azure.

Nella scheda **Attività** della definizione di compilazione sono elencati i singoli passaggi in uso. Sono disponibili cinque attività di compilazione.

![attività di definizione di compilazione](media/cicd/build-definition-tasks.png)

1. **Restore** &mdash; Esegue `dotnet restore` il comando per ripristinare i pacchetti NuGet dell'app. Il feed di pacchetto predefinito utilizzato è nuget.org.
1. **Compilazione** &mdash; Esegue `dotnet build --configuration release` il comando per compilare il codice dell'app. Questa `--configuration` opzione viene utilizzata per produrre una versione ottimizzata del codice, adatta per la distribuzione in un ambiente di produzione. Modificare la variabile *BuildConfiguration* nella scheda **Variabili** della definizione di compilazione se, ad esempio, è necessaria una configurazione di debug.
1. **Test** &mdash; Esegue `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` il comando per eseguire gli unit test dell'app. Gli unit test vengono eseguiti all'interno di qualsiasi progetto C , corrispondente al `**/*Tests/*.csproj` modello glob. I risultati dei test vengono salvati in un `--results-directory` file *.trx* nel percorso specificato dall'opzione. Se i test hanno esito negativo, la compilazione ha esito negativo e non viene distribuita.

    > [!NOTE]
    > Per verificare il funzionamento degli unit test, modificare *SimpleFeedReader.Tests, Services, NewsServiceTests.cs* per interrompere intenzionalmente uno dei test. Ad esempio, `Assert.True(result.Count > 0);` `Assert.False(result.Count > 0);` modificare `Returns_News_Stories_Given_Valid_Uri` nel metodo . Commit and push the change to GitHub. La compilazione viene attivata e ha esito negativo. Lo stato della pipeline di compilazione viene modificato **in non riuscito.** Annullare la modifica, eseguire il commit e inviare di nuovo. La compilazione ha esito positivo.

1. **Pubblica** &mdash; Esegue `dotnet publish --configuration release --output <local_path_on_build_agent>` il comando per produrre un file *con estensione zip* con gli elementi da distribuire. L'opzione `--output` specifica il percorso di pubblicazione del file *.zip.* Tale percorso viene specificato passando una `$(build.artifactstagingdirectory)`variabile predefinita [denominata](/azure/devops/pipelines/build/variables) . Tale variabile si espande in un percorso locale, ad esempio *c:,\_agent work, 1 , nell'agente*di compilazione.
1. **Pubblica artefatto** &mdash; Pubblica il file *.zip* prodotto dall'attività **Pubblica.** L'attività accetta il percorso del file *.zip* come `$(build.artifactstagingdirectory)`parametro, ovvero la variabile predefinita. Il file *.zip* viene pubblicato come cartella denominata *drop*.

Fare clic sul collegamento Riepilogo della definizione di compilazione per visualizzare una cronologia delle compilazioni con la definizione:Click the build definition's **Summary** link to view a history of builds with the definition:

![Schermata che mostra la cronologia delle definizioni di compilazione](media/cicd/build-definition-summary.png)

Nella pagina risultante fare clic sul collegamento corrispondente al numero di build univoco:

![Schermata che mostra la pagina di riepilogo della definizione di compilazione](media/cicd/build-definition-completed.png)

Viene visualizzato un riepilogo di questa build specifica. Fare clic sulla scheda **Elementi** e notare che la cartella di *ricezione* prodotta dalla compilazione è elencata:

![Schermata che mostra gli elementi di definizione di compilazione - cartella di ricezione](media/cicd/build-definition-artifacts.png)

Utilizzare i collegamenti **Scarica** ed **Esplora** per esaminare gli elementi pubblicati.

### <a name="release-pipeline"></a>Pipeline di versione

È stata creata una pipeline di rilascio con il nome *MyFirstProject-ASP.NET Core-CD:*

![Schermata che mostra la panoramica della pipeline di rilascio](media/cicd/release-definition-overview.png)

I due componenti principali della pipeline di rilascio sono **gli elementi** e gli **ambienti**. Facendo clic sulla casella nella sezione **Artefatti** viene riportato il seguente pannello:

![Schermata che mostra gli elementi della pipeline di rilascio](media/cicd/release-definition-artifacts.png)

Il valore **Origine (definizione di compilazione)** rappresenta la definizione di compilazione a cui è collegata questa pipeline di rilascio. Il file con estensione zip prodotto da un'esecuzione corretta della definizione di compilazione viene fornito all'ambiente *di produzione* per la distribuzione in Azure.The *.zip* file produced by a run of the build definition is provided to the Production environment for deployment to Azure. Fare clic sul collegamento 1 fase, 2 attività nella casella Ambiente di produzione per visualizzare le attività della pipeline di rilascio:Click the *1 phase, 2 tasks* link in the *Production* environment box to view the release pipeline tasks:

![Schermata che mostra le attività della pipeline di rilascio](media/cicd/release-definition-tasks.png)

La pipeline di rilascio è costituita da due attività: Distribuire il *servizio app di Azure in Slot* e Gestire il servizio app di Azure - Slot *Scambio.* Facendo clic sulla prima attività viene visualizzata la seguente configurazione dell'attività:

![Schermata che mostra l'attività di distribuzione della pipeline di rilascio](media/cicd/release-definition-task1.png)

La sottoscrizione di Azure, il tipo di servizio, il nome dell'app Web, il gruppo di risorse e lo slot di distribuzione sono definiti nell'attività di distribuzione. La casella di testo **Pacchetto o cartella** contiene il percorso del file *.zip* da estrarre e distribuito nello slot di *gestione temporanea* dell'app Web *\<unique_number\> * web.

Facendo clic sull'attività di scambio di slot viene visualizzata la seguente configurazione dell'attività:

![Schermata che mostra l'attività di scambio dello slot della pipeline di rilascio](media/cicd/release-definition-task2.png)

Vengono forniti i dettagli della sottoscrizione, del gruppo di risorse, del servizio, del nome dell'app Web e dello slot di distribuzione. La casella di controllo **Scambia con produzione** è selezionata. Di conseguenza, i bit distribuiti nello slot di *gestione temporanea* vengono scambiati nell'ambiente di produzione.

## <a name="additional-reading"></a>Informazioni aggiuntive

* [Creare la prima pipeline con Azure Pipelines](/azure/devops/pipelines/get-started-yaml)
* [Progetto Compilazione e .NET Core](/azure/devops/pipelines/languages/dotnet-core)
* [Distribuire un'app Web con le pipeline di AzureDeploy a web app with Azure Pipelines](/azure/devops/pipelines/targets/webapp)
