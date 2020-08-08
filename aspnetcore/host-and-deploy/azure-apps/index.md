---
title: Distribuire le app ASP.NET Core in Servizio app di Azure
author: bradygaster
description: Questo articolo contiene collegamenti a risorse di hosting e distribuzione di Azure.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/16/2019
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
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: 11de6b04f6813161e5eaee294f3e67e223ae0db3
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015920"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a>Distribuire le app ASP.NET Core in Servizio app di Azure

Il [servizio app di Azure](https://azure.microsoft.com/services/app-service/) è un [servizio di piattaforma di cloud computing Microsoft](https://azure.microsoft.com/) per l'hosting di app Web, inclusa ASP.NET Core.

## <a name="useful-resources"></a>Risorse utili

[Documentazione del servizio app](/azure/app-service/) è la home page di documentazione, esercitazioni, esempi, guide introduttive e altre risorse per le app di Azure. Due importanti esercitazioni relative all'hosting di app ASP.NET Core sono:

[Creare un'app Web ASP.NET Core in Azure](/azure/app-service/app-service-web-get-started-dotnet)  
Usare Visual Studio per creare e distribuire un'app Web ASP.NET Core nel servizio app di Azure in Windows.

[Creare un'app ASP.NET Core nel Servizio app in Linux](/azure/app-service/containers/quickstart-dotnetcore)  
Usare la riga di comando per creare e distribuire un'app Web ASP.NET Core nel servizio app di Azure in Linux.

Per la versione di ASP.NET Core disponibile nel servizio app Azure, vedere il [ASP.NET Core nel dashboard del servizio app](https://aspnetcoreon.azurewebsites.net/) .

Sottoscrivere il repository degli [annunci del servizio app](https://github.com/Azure/app-service-announcements/) e monitorare i problemi. Il team del servizio app pubblica regolarmente annunci e scenari in arrivo nel servizio app.

Gli articoli seguenti sono disponibili nella documentazione di ASP.NET Core:

<xref:tutorials/publish-to-azure-webapp-using-vs>  
Informazioni su come pubblicare un'app ASP.NET Core in Servizio app di Azure con Visual Studio.

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
Informazioni su come creare un'app Web ASP.NET Core tramite Visual Studio e distribuirla nel Servizio app di Azure usando Git per la distribuzione continua.

[Creare la prima pipeline](/azure/devops/pipelines/get-started-yaml)  
Impostare una build CI per un'app ASP.NET Core e quindi creare una versione di distribuzione continua in Servizio App di Azure.

[Sandbox per app Web di Azure](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
Individuare le limitazioni di esecuzione di runtime di Servizio app di Azure applicate dalla piattaforma per le app Azure.

<xref:test/troubleshoot>  
Riconoscere e risolvere i problemi di avvisi ed errori con i progetti ASP.NET Core.

## <a name="application-configuration"></a>Configurazione dell'applicazione

### <a name="platform"></a>Piattaforma

L'architettura della piattaforma (x86/x64) di un'app di servizi app viene impostata nelle impostazioni dell'app nel portale di Azure per le app ospitate in un livello di hosting A serie A (Basic) o superiore. Verificare che le impostazioni di pubblicazione dell'app, ad esempio nel profilo di pubblicazione di Visual Studio [(con estensione pubxml)](xref:host-and-deploy/visual-studio-publish-profiles), corrispondano all'impostazione nella configurazione del servizio dell'app nel portale di Azure.

::: moniker range=">= aspnetcore-2.2"

I runtime per le app a 64 bit (x64) e a 32 bit (x86) sono disponibili in Servizio app di Azure. [.NET Core SDK](/dotnet/core/sdk) disponibile nel servizio app è a 32 bit, ma è possibile distribuire app a 64 bit compilate in locale usando la console [Kudu](https://github.com/projectkudu/kudu/wiki) o il processo di pubblicazione di Visual Studio. Per altre informazioni, vedere la sezione [Pubblicare e distribuire l'app](#publish-and-deploy-the-app).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Per le app con dipendenze native, i runtime per le app a 32 bit (x86) sono disponibili in Servizio app di Azure. La versione di [.NET Core SDK](/dotnet/core/sdk) disponibile nel servizio app è a 32 bit.

::: moniker-end

Per altre informazioni sui componenti e sui metodi di distribuzione di .NET Core Framework, ad esempio informazioni sul runtime di .NET Core e la .NET Core SDK, vedere [informazioni su .NET Core: Composition](/dotnet/core/about#composition).

### <a name="packages"></a>Pacchetti

Includere i pacchetti NuGet seguenti per offrire funzionalità di registrazione automatica per le app distribuite in Servizio app di Azure:

* [Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) usa [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) per fornire l'integrazione di ASP.NET Core con il Servizio app di Azure. La funzionalità di registrazione aggiunte vengono fornite dal pacchetto `Microsoft.AspNetCore.AzureAppServicesIntegration`.
* [Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) esegue [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) per aggiungere i provider di registrazione diagnostica del servizio app di Azure nel pacchetto `Microsoft.Extensions.Logging.AzureAppServices`.
* [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) offre implementazioni di logger per il supporto dei registri di diagnostica del servizio app di Azure e delle funzionalità del flusso di registrazione.

I pacchetti precedenti non sono disponibili dal [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Le app destinate a .NET Framework o che fanno riferimento al metapacchetto `Microsoft.AspNetCore.App` in modo esplicito deve fare riferimento ai singoli pacchetti nel file di progetto dell'app.

## <a name="override-app-configuration-using-the-azure-portal"></a>Eseguire l'override della configurazione delle app usando il portale di Azure

::: moniker range=">= aspnetcore-3.0"

Le impostazioni dell'app nel portale di Azure consentono di impostare le variabili di ambiente per l'app. Le variabili di ambiente possono essere utilizzate dal [provider di configurazione delle variabili di ambiente](xref:fundamentals/configuration/index#environment-variables).

Quando nel portale di Azure viene creata o modificata un'impostazione dell'app e viene selezionato il pulsante **Salva**, l'app Azure viene riavviata. La variabile di ambiente risulterà disponibile per l'app dopo il riavvio del servizio.

Quando un'app usa l' [host generico](xref:fundamentals/host/generic-host), le variabili di ambiente vengono caricate nella configurazione dell'app quando <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> viene chiamato per compilare l'host. Per altre informazioni, vedere <xref:fundamentals/host/generic-host> e il [provider di configurazione delle variabili di ambiente](xref:fundamentals/configuration/index#environment-variables).

::: moniker-end
::: moniker range="< aspnetcore-3.0"

Le impostazioni dell'app nel portale di Azure consentono di impostare le variabili di ambiente per l'app. Le variabili di ambiente possono essere utilizzate dal [provider di configurazione delle variabili di ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

Quando nel portale di Azure viene creata o modificata un'impostazione dell'app e viene selezionato il pulsante **Salva**, l'app Azure viene riavviata. La variabile di ambiente risulterà disponibile per l'app dopo il riavvio del servizio.

Quando un'app usa l' [host Web](xref:fundamentals/host/web-host), le variabili di ambiente vengono caricate nella configurazione dell'app quando <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> viene chiamato per compilare l'host. Per altre informazioni, vedere <xref:fundamentals/host/web-host> e il [provider di configurazione delle variabili di ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scenari con server proxy e servizi di bilanciamento del carico

Il [middleware di integrazione IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), che consente di configurare il middleware delle intestazioni inoltrate in caso di hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), e il modulo ASP.NET Core sono configurati per inoltrare lo schema (HTTP/HTTPS) e l'indirizzo IP remoto di origine della richiesta. Potrebbero essere necessari interventi di configurazione aggiuntivi per le app ospitate dietro ulteriori server proxy e servizi di bilanciamento del carico. Per altre informazioni, vedere [Configurare ASP.NET Core per l'utilizzo di server proxy e servizi di bilanciamento del carico](xref:host-and-deploy/proxy-load-balancer).

## <a name="monitoring-and-logging"></a>Monitoraggio e registrazione

::: moniker range=">= aspnetcore-3.0"

Le app ASP.NET Core distribuite in Servizio app di Azure ricevono automaticamente l'estensione di Servizio app **ASP.NET Core Logging Integration** (Integrazione di registrazione ASP.NET Core). L'estensione abilita l'integrazione della registrazione per le app ASP.NET Core in Servizio app di Azure.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Le app ASP.NET Core distribuite in Servizio app ricevono automaticamente un'estensione di Servizio app, ovvero le **estensioni di registrazione di ASP.NET Core**. L'estensione abilita l'integrazione della registrazione per le app ASP.NET Core in Servizio app di Azure.

::: moniker-end

Per informazioni sul monitoraggio, la registrazione e la risoluzione dei problemi, vedere gli articoli seguenti:

[Monitorare le app in Servizio app di Azure](/azure/app-service/web-sites-monitor)  
Informazioni su come esaminare le quote e le metriche per le app e i piani del servizio app.

[Abilitare la registrazione diagnostica per le app nel Servizio app di Azure](/azure/app-service/web-sites-enable-diagnostic-log)  
Informazioni su come abilitare e accedere alla registrazione diagnostica per i codici di stato HTTP, le richieste non riuscite e l'attività del server Web.

<xref:fundamentals/error-handling>  
Riconoscimento degli approcci comuni di gestione degli errori nelle app ASP.NET Core.

<xref:test/troubleshoot-azure-iis>  
Informazioni su come diagnosticare i problemi delle distribuzioni del servizio app di Azure con le app ASP.NET Core.

<xref:host-and-deploy/azure-iis-errors-reference>  
Informazioni sugli errori comuni di configurazione della distribuzione per le app ospitate dal servizio app di Azure o da IIS con suggerimenti per la risoluzione.

## <a name="data-protection-key-ring-and-deployment-slots"></a>KeyRing di protezione dati e slot di distribuzione

Le [chiavi di protezione dati](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) sono salvate in modo permanente nella cartella *%HOME%\ASP.NET\DataProtection-Keys*. La cartella è associata all'archiviazione di rete e sincronizzata in tutti i computer che ospitano l'app. Le chiavi non vengono protette quando sono inattive. La cartella offre il KeyRing a tutte le istanze di un'app in un singolo slot di distribuzione. Gli slot di distribuzione separati, ad esempio gli slot di gestione temporanea e di produzione, non condividono un KeyRing.

Nel passaggio da uno slot di distribuzione all'altro, tutti i sistemi che usano la protezione dati non saranno in grado di decrittografare i dati archiviati usando il KeyRing all'interno dello slot precedente. CookieIl Middleware ASP.NET usa la protezione dei dati per proteggere i relativi cookie . In questo modo gli utenti vengono disconnessi da un'app che usa il middleware ASP.NET standard Cookie . Per una soluzione di KeyRing indipendente dallo slot, usare un provider di KeyRing esterno, ad esempio:

* Archiviazione BLOB di Azure
* Insieme di credenziali chiave di Azure
* Archivio SQL
* Cache Redis

Per altre informazioni, vedere <xref:security/data-protection/implementation/key-storage-providers>.
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>

## <a name="deploy-an-aspnet-core-app-that-uses-a-net-core-preview"></a>Distribuire un'app ASP.NET Core che usa un'anteprima di .NET Core

Per distribuire un'app che usa una versione di anteprima di .NET Core, vedere le risorse seguenti. Questi approcci vengono usati anche quando il runtime è disponibile, ma l'SDK non è stato installato nel servizio app Azure.

* [Specificare la versione di .NET Core SDK utilizzando Azure Pipelines](#specify-the-net-core-sdk-version-using-azure-pipelines)
* [Distribuire un'app di anteprima autonoma](#deploy-a-self-contained-preview-app)
* [Usare Docker con app Web per contenitori](#use-docker-with-web-apps-for-containers)
* [Installare l'estensione del sito di anteprima](#install-the-preview-site-extension)

Per la versione di ASP.NET Core disponibile nel servizio app Azure, vedere il [ASP.NET Core nel dashboard del servizio app](https://aspnetcoreon.azurewebsites.net/) .

### <a name="specify-the-net-core-sdk-version-using-azure-pipelines"></a>Specificare la versione di .NET Core SDK utilizzando Azure Pipelines

Usare [app Azure scenari](/azure/app-service/deploy-continuous-deployment) di integrazione continua/distribuzione continua del servizio per configurare una compilazione di integrazione continua con Azure DevOps. Dopo la creazione della build di Azure DevOps, configurare facoltativamente la compilazione per l'uso di una versione specifica dell'SDK. 

#### <a name="specify-the-net-core-sdk-version"></a>Specificare la versione di .NET Core SDK

Quando si usa il centro distribuzione servizio app per creare una compilazione di Azure DevOps, la pipeline di compilazione predefinita include i passaggi per `Restore` ,, `Build` `Test` e `Publish` . Per specificare la versione dell'SDK, selezionare il pulsante **Aggiungi (+)** nell'elenco processo agente per aggiungere un nuovo passaggio. Cercare **.NET Core SDK** nella barra di ricerca. 

![Aggiungere il passaggio .NET Core SDK](index/add-sdk-step.png)

Spostare il passaggio nella prima posizione della compilazione in modo che i passaggi successivi usino la versione specificata del .NET Core SDK. Specificare la versione del .NET Core SDK. In questo esempio, l'SDK è impostato su `3.0.100` .

![Passaggio SDK completato](index/sdk-step-first-place.png)

Per pubblicare una [distribuzione autonoma (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), configurare SCD nel `Publish` passaggio e specificare l'identificatore di [Runtime (RID)](/dotnet/core/rid-catalog).

![Pubblicazione autonoma](index/self-contained.png)

### <a name="deploy-a-self-contained-preview-app"></a>Distribuire un'app di anteprima autonoma

Una [distribuzione autonoma](/dotnet/core/deploying/#self-contained-deployments-scd) che ha come destinazione un runtime di anteprima include il runtime di anteprima nella distribuzione.

Per la distribuzione di un'app autonoma:

* Il sito nel Servizio app di Azure non richiede l'[estensione del sito di anteprima](#install-the-preview-site-extension).
* L'app deve essere pubblicata seguendo un approccio diverso rispetto alla procedura di pubblicazione per una [distribuzione dipendente dal framework](/dotnet/core/deploying#framework-dependent-deployments-fdd).

Seguire le istruzioni riportate nella sezione [Distribuire l'app autonoma](#deploy-the-app-self-contained).

### <a name="use-docker-with-web-apps-for-containers"></a>Usare Docker con app Web per contenitori

L'[hub Docker](https://hub.docker.com/r/microsoft/aspnetcore/) contiene le immagini di Docker più recenti per la versione di anteprima. Le immagini possono essere usate come immagini di base. Usare l'immagine e distribuirla alle app Web per i contenitori normalmente.

### <a name="install-the-preview-site-extension"></a>Installare l'estensione del sito di anteprima

Se si verifica un problema usando l'estensione del sito di anteprima, aprire un [problema DotNet/AspNetCore](https://github.com/dotnet/AspNetCore/issues).

1. Dal portale di Azure passare al servizio app.
1. Selezionare l'app Web.
1. Digitare "es" nella casella di ricerca per filtrare per "Estensioni" o scorrere l'elenco degli strumenti di gestione.
1. Selezionare **Estensioni**.
1. Selezionare **Aggiungi**.
1. Selezionare l'estensione **ASP.NET Core {X.Y} ({x64|x86}) Runtime** nell'elenco, dove `{X.Y}` è la versione di anteprima di ASP.NET Core e `{x64|x86}` specifica la piattaforma.
1. Selezionare **OK** per accettare le condizioni legali.
1. Per installare l'estensione, selezionare **OK**.

Al termine dell'operazione, viene installata l'anteprima più recente di .NET Core. Verificare l'installazione:

1. Selezionare **Strumenti avanzati**.
1. Selezionare **Vai** in **Strumenti avanzati**.
1. Selezionare la **Debug console**  >  voce di menu**PowerShell** per console di debug.
1. Eseguire il comando seguente dal prompt di PowerShell. Sostituire la versione di runtime di ASP.NET Core in `{X.Y}` e la piattaforma in `{PLATFORM}` nel comando:

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   Il comando restituisce `True` quando è installato il runtime di anteprima x64.

> [!NOTE]
> L'architettura della piattaforma (x86/x64) di un'app di servizi app viene impostata nelle impostazioni dell'app nel portale di Azure per le app ospitate in un livello di hosting A serie A (Basic) o superiore. Verificare che le impostazioni di pubblicazione dell'app, ad esempio nel profilo di pubblicazione di Visual Studio [(con estensione pubxml)](xref:host-and-deploy/visual-studio-publish-profiles), corrispondano a quelle della configurazione del servizio dell'app nella portale di Azure.
>
> Se l'app viene eseguita in modalità in-process e l'architettura della piattaforma è configurata per 64 bit (x64), il modulo ASP.NET Core usa il runtime dell'anteprima a 64 bit, se presente. Installare l'estensione di **Runtime ASP.NET Core {X. Y} (x64)** usando il portale di Azure.
>
> Dopo aver installato il runtime di anteprima x64, eseguire il comando seguente nella finestra di comando PowerShell di Azure Kudu per verificare l'installazione. Sostituire il ASP.NET Core versione runtime per `{X.Y}` nel comando seguente:
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> Il comando restituisce `True` quando è installato il runtime di anteprima x64.

> [!NOTE]
> Le **estensioni di ASP.NET Core** abilitano funzionalità aggiuntive per ASP.NET Core nei Servizi app di Azure, ad esempio la registrazione di Azure. L'estensione viene installata automaticamente durante la distribuzione da Visual Studio. Se l'estensione non è installata, installarla per l'app.

**Usare l'estensione del sito di anteprima con un modello ARM**

Se per creare e distribuire le app si usa un modello ARM, è possibile usare il tipo di risorsa `siteextensions` per aggiungere l'estensione del sito a un'app Web. Ad esempio:

[!code-json[](index/sample/arm.json?highlight=2)]

## <a name="publish-and-deploy-the-app"></a>Pubblicare e distribuire l'app

::: moniker range=">= aspnetcore-2.2"

Per una distribuzione a 64 bit:

* Usare .NET Core SDK a 64 bit per compilare un'app a 64 bit.
* Impostare **Piattaforma** su **64 bit** in **Configurazione** > **Impostazioni generali** nel servizio app. L'app deve usare un piano di servizio Basic o superiore per abilitare la scelta del numero di bit della piattaforma.

::: moniker-end

### <a name="deploy-the-app-framework-dependent"></a>Distribuire l'app in modo dipendente dal framework

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Selezionare **Compila**  >  **pubblicazione {nome applicazione}** dalla barra degli strumenti di Visual Studio oppure fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **pubblica**.
1. Nella finestra di dialogo **Selezionare una destinazione di pubblicazione.** verificare che sia selezionata la voce **Servizio app**.
1. Fare clic su **Advanced** (Avanzate). Viene visualizzata la finestra di dialogo **Pubblica**.
1. Nella finestra di dialogo **Pubblica**:
   * Verificare che sia selezionata la configurazione **Rilascio**.
   * Aprire l'elenco a discesa **Modalità di distribuzione** e selezionare **Dipendente dal framework**.
   * Selezionare **Portabile** come **Runtime di destinazione**.
   * Se durante la distribuzione è necessario rimuovere i file aggiuntivi, aprire **Opzioni pubblicazione file** e selezionare la casella di controllo che consente di rimuovere i file aggiuntivi nella destinazione.
   * Selezionare **Salva**.
1. Creare un nuovo sito o aggiornare un sito esistente seguendo le istruzioni rimanenti della pubblicazione guidata.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

1. Nel file di progetto non specificare un [Identificatore runtime](/dotnet/core/rid-catalog).

1. Da una shell dei comandi pubblicare l'app nella configurazione di versione con il comando [dotnet publish](/dotnet/core/tools/dotnet-publish). Nell'esempio seguente l'app viene pubblicata come app dipendente dal framework:

   ```console
   dotnet publish --configuration Release
   ```

1. Spostare il contenuto della directory *bin/Release/{TARGET FRAMEWORK}/publish* nel sito nel servizio app. Se il contenuto della cartella *publish* viene trascinato dal disco rigido locale o dalla condivisione di rete direttamente nel servizio app nella console [Kudu](https://github.com/projectkudu/kudu/wiki), trascinare i file nella cartella `D:\home\site\wwwroot` nella console Kudu.

---

### <a name="deploy-the-app-self-contained"></a>Distribuire l'app autonoma

Usare Visual Studio o il interfaccia della riga di comando di .NET Core per una [distribuzione autonoma (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Selezionare **Compila**  >  **pubblicazione {nome applicazione}** dalla barra degli strumenti di Visual Studio oppure fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **pubblica**.
1. Nella finestra di dialogo **Selezionare una destinazione di pubblicazione.** verificare che sia selezionata la voce **Servizio app**.
1. Fare clic su **Advanced** (Avanzate). Viene visualizzata la finestra di dialogo **Pubblica**.
1. Nella finestra di dialogo **Pubblica**:
   * Verificare che sia selezionata la configurazione **Rilascio**.
   * Aprire l'elenco a discesa **Modalità di distribuzione** e selezionare **Completa**.
   * Selezionare il runtime di destinazione dall'elenco a discesa **Runtime di destinazione**. Il valore predefinito è `win-x86`.
   * Se durante la distribuzione è necessario rimuovere i file aggiuntivi, aprire **Opzioni pubblicazione file** e selezionare la casella di controllo che consente di rimuovere i file aggiuntivi nella destinazione.
   * Selezionare **Salva**.
1. Creare un nuovo sito o aggiornare un sito esistente seguendo le istruzioni rimanenti della pubblicazione guidata.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

1. Nel file di progetto specificare uno o più [identificatori di runtime (RID)](/dotnet/core/rid-catalog). Usare `<RuntimeIdentifier>` (singolare) per un unico RID o `<RuntimeIdentifiers>` (plurale) per specificare un elenco di RID delimitato da punto e virgola. Nell'esempio seguente è specificato il RID `win-x86`:

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. Da una shell dei comandi eseguire il comando [dotnet publish](/dotnet/core/tools/dotnet-publish) per pubblicare l'app in configurazione Rilascio per il runtime dell'host. Nell'esempio seguente l'app viene pubblicata per il RID `win-x86`. Il RID fornito per l'opzione `--runtime` deve essere specificato nella proprietà `<RuntimeIdentifier>` (o `<RuntimeIdentifiers>`) del file di progetto.

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. Spostare il contenuto della directory *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* nel sito del Servizio app. Se il contenuto della cartella *publish* viene trascinato dal disco rigido locale o dalla condivisione di rete direttamente nel servizio app nella console Kudu, trascinare i file nella cartella `D:\home\site\wwwroot` nella console Kudu.

---

## <a name="protocol-settings-https"></a>Impostazioni del protocollo (HTTPS)

Le associazioni di protocollo protette consentono di specificare un certificato da usare per rispondere alle richieste su HTTPS. L'associazione richiede un certificato privato valido (*PFX*) rilasciato per il nome host specifico. Per altre informazioni, vedere [esercitazione: associare un certificato SSL personalizzato esistente al servizio app Azure](/azure/app-service/app-service-web-tutorial-custom-ssl).

## <a name="transform-webconfig"></a>Trasformare web.config

Se è necessario trasformare *web.config* in fase di pubblicazione (ad esempio, impostare variabili di ambiente in base a configurazione, profilo o ambiente), vedere <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Panoramica del Servizio app](/azure/app-service/app-service-web-overview)
* [Azure App Service: The Best Place to Host your .NET Apps ](https://channel9.msdn.com/events/dotnetConf/2017/T222) (Servizio app di Azure: la soluzione migliore per l'hosting delle app .NET) (video di 55 minuti)
* [Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience) (Azure Friday: diagnostica e risoluzione dei problemi del servizio app di Azure) (video di 12 minuti)
* [Panoramica della diagnostica del servizio app di Azure](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

Il servizio app di Azure in Windows Server usa [Internet Information Services (IIS)](https://www.iis.net/). Gli argomenti seguenti riguardano la tecnologia IIS sottostante:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [Windows Server - Contenuti per gli amministratori IT relativi alle versioni correnti e precedenti](/windows-server/windows-server-versions)
