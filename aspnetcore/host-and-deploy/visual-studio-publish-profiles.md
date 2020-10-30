---
title: Profili di pubblicazione di Visual Studio (con estensione pubxml) per la distribuzione di app ASP.NET Core
author: rick-anderson
description: Informazioni su come creare profili di pubblicazione in Visual Studio e usarli per la gestione delle distribuzioni di app ASP.NET Core in varie destinazioni.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/28/2020
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
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: 98dfd539171807cbf94d4ac8746458152c809495
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057570"
---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a>Profili di pubblicazione di Visual Studio (con estensione pubxml) per la distribuzione di app ASP.NET Core

Di [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) e [Rick Anderson](https://twitter.com/RickAndMSFT)

Questo documento descrive come usare Visual Studio 2019 o versioni successive per la creazione e l'uso di profili di pubblicazione. I profili di pubblicazione creati con Visual Studio possono essere usati con MSBuild e Visual Studio. Per istruzioni sulla pubblicazione in Azure, vedere <xref:tutorials/publish-to-azure-webapp-using-vs>.

Il `dotnet new mvc` comando produce un file di progetto contenente l' [ \<Project> elemento](/visualstudio/msbuild/project-element-msbuild)di livello radice seguente:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

L'attributo `Sdk` dell'elemento `<Project>` precedente importa le [proprietà](/visualstudio/msbuild/msbuild-properties) e le [destinazioni](/visualstudio/msbuild/msbuild-targets) di MSBuild rispettivamente da *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* e *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets* . Il percorso predefinito per `$(MSBuildSDKsPath)` (con Visual Studio 2019 Enterprise) è la cartella *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* .

`Microsoft.NET.Sdk.Web`([Web SDK](xref:razor-pages/web-sdk)) dipende da altri SDK, tra cui `Microsoft.NET.Sdk` ([.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) e `Microsoft.NET.Sdk.Razor` ([ Razor SDK](xref:razor-pages/sdk)). Vengono importate le proprietà e le destinazioni di MSBuild associate a ogni SDK dipendente. Le destinazioni di pubblicazione importano il set appropriato di destinazioni in base al metodo di pubblicazione usato.

Quando MSBuild o Visual Studio carica un progetto, vengono eseguite le azioni di alto livello seguenti:

* Compilare un progetto
* Calcolare i file da pubblicare
* Pubblicare i file nella destinazione

## <a name="compute-project-items"></a>Calcolare gli elementi del progetto

Quando viene caricato il progetto, vengono calcolati gli [elementi del progetto di MSBuild](/visualstudio/msbuild/common-msbuild-project-items) (file). Il tipo di elemento determina la modalità di elaborazione del file. Per impostazione predefinita, i file *cs* sono inclusi nell'elenco di elementi `Compile`. I file presenti nell'elenco di elementi `Compile` vengono compilati.

L'elenco di elementi `Content` contiene i file che sono pubblicati in aggiunta agli output di compilazione. Per impostazione predefinita, i file corrispondenti ai criteri `wwwroot\**`, `**\*.config` e `**\*.json` vengono inclusi nell'elenco di elementi `Content`. Il  [criterio GLOB](https://gruntjs.com/configuring-tasks#globbing-patterns)`wwwroot\**`, ad esempio, specifica tutti i file nella cartella *wwwroot* e relative sottocartelle.

::: moniker range=">= aspnetcore-3.0"

SDK [Web](xref:razor-pages/web-sdk) importa l' [ Razor SDK](xref:razor-pages/sdk). Di conseguenza, anche i file corrispondenti ai criteri `**\*.cshtml` e `**\*.razor` vengono inclusi nell'elenco di elementi `Content`.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

SDK [Web](xref:razor-pages/web-sdk) importa l' [ Razor SDK](xref:razor-pages/sdk). Di conseguenza, anche i file corrispondenti al criterio `**\*.cshtml` vengono inclusi nell'elenco di elementi `Content`.

::: moniker-end

Per aggiungere esplicitamente un file all'elenco di pubblicazione, aggiungere il file direttamente nel file con estensione *csproj* , come illustrato nella sezione [File di inclusione](#include-files).

Quando si seleziona il pulsante **Pubblica** in Visual Studio o quando si pubblica dalla riga di comando:

* Vengono calcolati le proprietà/gli elementi (i file necessari per compilare).
* **Solo Visual Studio** : i pacchetti NuGet vengono ripristinati. (Il ripristino deve essere esplicito da parte dell'utente nell'interfaccia della riga di comando.)
* Il progetto viene compilato.
* Vengono calcolati gli elementi di pubblicazione (i file necessari per pubblicare).
* Il progetto viene pubblicato (i file calcolati vengono copiati nella destinazione di pubblicazione).

Quando un progetto ASP.NET Core fa riferimento a `Microsoft.NET.Sdk.Web` nel file di progetto, nella radice della directory dell'app Web viene posizionato un file *app_offline.htm* . Quando il file è presente, il modulo ASP.NET Core arresta normalmente l'app e rende disponibile il file *app_offline.htm* durante la distribuzione. Per altre informazioni, vedere la [Guida di riferimento per la configurazione del modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).

## <a name="basic-command-line-publishing"></a>Pubblicazione di base dalla riga di comando

La pubblicazione dalla riga di comando funziona su tutte le piattaforme supportate da .NET Core e non richiede Visual Studio. Negli esempi seguenti il comando [dotnet publish](/dotnet/core/tools/dotnet-publish) dell'interfaccia della riga di comando di .NET Core viene eseguito dalla directory del progetto (che contiene il file con estensione *csproj* ). Se la cartella del progetto non è la directory di lavoro corrente, passare in modo esplicito il percorso del file di progetto. Ad esempio:

```dotnetcli
dotnet publish C:\Webs\Web1
```

Eseguire i comandi seguenti per creare e pubblicare un'app Web:

```dotnetcli
dotnet new mvc
dotnet publish
```

Il comando `dotnet publish` produce una variazione dell'output seguente:

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

Il formato della cartella di pubblicazione predefinito è *bin\Debug\\{MONIKER FRAMEWORK DI DESTINAZIONE}\publish\\* . Ad esempio, *bin\Debug\netcoreapp2.2\publish\\* .

Il comando seguente specifica un build `Release` e la directory di pubblicazione:

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

Il comando `dotnet publish` chiama MSBuild che richiama la destinazione `Publish`. Tutti i parametri passati a `dotnet publish` vengono passati a MSBuild. I parametri `-c` e `-o` sono mappati rispettivamente alle proprietà `Configuration` e `OutputPath` di MSBuild.

È possibile passare le proprietà di MSBuild usando uno dei formati seguenti:

* `-p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

Ad esempio, il comando seguente pubblica una build `Release` in una condivisione di rete. La condivisione di rete è specificata con le barre ( *//r8/* ) e funziona su tutte le piattaforme .NET Core supportate.

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

Verificare che l'app pubblicata per la distribuzione non sia in esecuzione. I file nella cartella *publish* sono bloccati durante l'esecuzione dell'app. La distribuzione non viene eseguita perché non è possibile copiare i file bloccati.

## <a name="publish-profiles"></a>Profili di pubblicazione

Questa sezione usa Visual Studio 2019 o versioni successive per creare un profilo di pubblicazione. Dopo la creazione del profilo, la pubblicazione è disponibile da Visual Studio o dalla riga di comando. I profili di pubblicazione possono semplificare il processo di pubblicazione e può essere presente un numero illimitato di profili.

Creare un profilo di pubblicazione in Visual Studio scegliendo uno dei seguenti percorsi:

* Fare clic con il pulsante destro del mouse in **Esplora soluzioni** , quindi scegliere **Pubblica** .
* Selezionare **Pubblica {NOME PROGETTO}** dal menu **Compila** .

Verrà visualizzata la scheda **Pubblica** della pagina di capacità dell'app. Se il progetto non ha un profilo di pubblicazione, viene visualizzata la pagina **Selezionare una destinazione di pubblicazione** . Viene richiesto di selezionare una delle destinazioni di pubblicazione seguenti:

* Servizio app di Azure
* Servizio app di Azure in Linux
* Macchine virtuali di Azure
* Cartella
* IIS, FTP, Distribuzione Web (per qualsiasi server Web)
* Importa profilo

Per determinare la destinazione di pubblicazione più appropriata, vedere [Quali sono le opzioni di pubblicazione più adatte](/visualstudio/ide/not-in-toc/web-publish-options).

Quando è selezionata la destinazione di pubblicazione **Cartella** , specificare il percorso di una cartella in cui archiviare gli asset pubblicati. Il percorso della cartella predefinita è *bin\\{CONFIGURAZIONE PROGETTO}\\{MONIKER FRAMEWORK DI DESTINAZIONE}\publish\\* . Ad esempio, *bin\Release\netcoreapp2.2\publish\\* . Selezionare il pulsante **Crea profilo** per terminare.

Dopo aver creato un profilo di pubblicazione, il contenuto della scheda **Pubblica** cambia. Il nuovo profilo creato viene visualizzato in un elenco a discesa. Sotto l'elenco a discesa selezionare **Crea nuovo profilo** per creare un altro nuovo profilo.

Lo strumento di pubblicazione di Visual Studio produce un file MSBuild *Properties/PublishProfiles/{PROFILE NAME}.pubxml* che descrive il profilo di pubblicazione. Il file con estensione *pubxml* :

* Contiene le impostazioni di configurazione di pubblicazione e viene utilizzato dal processo di pubblicazione.
* Può essere modificato per personalizzare il processo di compilazione e pubblicazione.

Quando si esegue la pubblicazione in una destinazione di Azure, il file *.pubxml* contiene l'identificatore della sottoscrizione Azure. Con tale tipo di destinazione, è sconsigliato aggiungere questo file al controllo del codice sorgente. Quando si pubblica in una destinazione non Azure, è possibile archiviare il file *.pubxml* .

Le informazioni riservate, come la password di pubblicazione, vengono crittografate a livello di singolo utente/computer. Sono memorizzate nel file *Properties/PublishProfiles/{NOME PROFILO}.pubxml.user* . Poiché questo file può contenere informazioni riservate, non deve essere archiviato nel controllo del codice sorgente.

Per una panoramica su come pubblicare un'app Web ASP.NET Core, vedere <xref:host-and-deploy/index>. Le attività e le destinazioni di MSBuild necessarie per pubblicare un'app Web ASP.NET Core sono open source nel [repository DotNet/WebSDK](https://github.com/dotnet/websdk).

I comandi seguenti possono usare i profili di pubblicazione cartella, MSDeploy e [Kudu](https://github.com/projectkudu/kudu/wiki) . Dato che MSDeploy non include il supporto multipiattaforma, le opzioni seguenti di MSDeploy sono supportate solo in Windows.

**Cartella (multipiattaforma):**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

**MSDeploy:**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

**Pacchetto MSDeploy:**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

Negli esempi precedenti:

* `dotnet publish` e `dotnet build` supportano le API Kudu per la pubblicazione in Azure da qualsiasi piattaforma. La pubblicazione con Visual Studio supporta le API Kudu, ma è supportata da WebSDK per la pubblicazione multipiattaforma in Azure.
* Non passare `DeployOnBuild` al `dotnet publish` comando.

Per altre informazioni, vedere [Microsoft.NET.Sdk.Publish](https://github.com/dotnet/websdk#microsoftnetsdkpublish).

Aggiungere un profilo di pubblicazione alla cartella *Properties/PublishProfiles* del progetto con il contenuto seguente:

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

## <a name="folder-publish-example"></a>Esempio di pubblicazione cartella

Quando si esegue la pubblicazione con un profilo denominato *FolderProfile* , usare uno dei comandi seguenti:

```dotnetcli
dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`
```

```dotnetcli
dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

```bash
msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

Il comando [dotnet build](/dotnet/core/tools/dotnet-build) dell'interfaccia della riga di comando di .NET Core chiama `msbuild` per eseguire il processo di compilazione e pubblicazione. I comandi `dotnet build` e `msbuild` sono equivalenti quando si passa un profilo di cartella. Chiamando `msbuild` direttamente in Windows, viene usata la versione .NET Framework di MSBuild. La chiamata di `dotnet build` su un profilo non di tipo cartella:

* Richiama `msbuild`, che usa MSDeploy.
* Genera un errore (anche se in esecuzione in Windows). Per la pubblicazione con un profilo non di tipo cartella, chiamare `msbuild` direttamente.

Il profilo di pubblicazione cartella seguente è stato creato con Visual Studio ed esegue la pubblicazione in una condivisione di rete:

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

Nell'esempio precedente:

* La proprietà `<ExcludeApp_Data>` è presente solo per soddisfare un requisito di XML Schema. La proprietà `<ExcludeApp_Data>` non ha alcun effetto sul processo di pubblicazione, anche se è presente una cartella *App_Data* nella radice del progetto. La cartella *App_Data* non riceve un trattamento speciale, come accade per i progetti ASP.NET 4.x.
* La proprietà `<LastUsedBuildConfiguration>` è impostata su `Release`. Nella pubblicazione da Visual Studio, il valore di `<LastUsedBuildConfiguration>` viene impostato usando il valore, quando viene avviato il processo di pubblicazione. `<LastUsedBuildConfiguration>` è speciale e non deve essere sottoposta a override in un file di MSBuild importato. Questa proprietà, tuttavia, può essere sottoposta a override dalla riga di comando usando uno degli approcci seguenti.
  * Mediante l'interfaccia della riga di comando di .NET Core:

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * Mediante MSBuild:

    ```bash
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  Per altre informazioni, vedere [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx) (MSBuild: come impostare la proprietà di configurazione).

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a>Pubblicare in un endpoint di MSDeploy dalla riga di comando

L'esempio seguente usa un'app Web ASP.NET Core creata da Visual Studio e denominata *AzureWebApp* . Un profilo di pubblicazione app di Azure viene aggiunto con Visual Studio. Per altre informazioni su come creare un profilo, vedere la sezione [Profili di pubblicazione](#publish-profiles).

Per distribuire l'app usando un profilo di pubblicazione, eseguire il comando `msbuild` dal **prompt dei comandi per gli sviluppatori** di Visual Studio. Il prompt dei comandi è disponibile nella cartella *Visual Studio* del menu **Start** della barra delle applicazioni di Windows. Per semplificare l'accesso, è possibile aggiungere il prompt dei comandi al menu **Strumenti** di Visual Studio. Per altre informazioni, vedere [Prompt dei comandi per gli sviluppatori per Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).

MSBuild usa la sintassi dei comandi seguente:

```bash
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* `{PATH}`: Percorso del file di progetto dell'app.
* `{PROFILE}`: Nome del profilo di pubblicazione.
* `{USERNAME}`: Nome utente MSDeploy. Il `{USERNAME}` può essere trovato nel profilo di pubblicazione.
* `{PASSWORD}`: Password MSDeploy. Ottenere `{PASSWORD}` da *{profile}. File PublishSettings* . Scaricare il file *.PublishSettings* da:
  * **Esplora soluzioni** : selezionare **Visualizza**  >  **Cloud Explorer** . Connettersi alla sottoscrizione di Azure. Aprire **Servizi app** . Fare clic con il pulsante destro del mouse sull'app. Selezionare **Scarica profilo di pubblicazione** .
  * Portale di Azure: selezionare **Ottieni profilo di pubblicazione** nel pannello **Panoramica** dell'app Web.

L'esempio seguente usa un profilo di pubblicazione denominato *AzureWebApp - Web Deploy* :

```bash
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

Un profilo di pubblicazione può essere usato anche con il comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) dell'interfaccia della riga di comando di .NET Core da una shell comandi di Windows:

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> Il comando `dotnet msbuild` è multipiattaforma e consente di compilare app ASP.NET Core in macOS e Linux. Tuttavia, MSBuild in macOS e Linux non è in grado di distribuire un'app in Azure o in altri endpoint MSDeploy.

## <a name="set-the-environment"></a>Impostare l'ambiente

Includere la proprietà `<EnvironmentName>` nel profilo di pubblicazione ( *.pubxml* ) o nel file di progetto per impostare l' [ambiente](xref:fundamentals/environments) dell'app:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

Se sono necessarie trasformazioni *web.config* (ad esempio, l'impostazione di variabili di ambiente in base a configurazione, profilo o ambiente), vedere <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="exclude-files"></a>Escludere file

Quando si pubblicano app Web ASP.NET Core vengono inclusi gli asset seguenti:

* Artefatti della compilazione
* Cartelle e file corrispondenti ai criteri GLOB seguenti:
  * `**\*.config` (ad esempio, *web.config* )
  * `**\*.json` (ad esempio, *appsettings.json* )
  * `wwwroot\**`

MSBuild supporta i [criteri GLOB](https://gruntjs.com/configuring-tasks#globbing-patterns). Ad esempio, l'elemento `<Content>` seguente elimina la copia dei file di testo ( *.txt* ) nella cartella *wwwroot\content* e nelle relative sottocartelle:

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Il markup precedente può essere aggiunto a un profilo di pubblicazione o al file *.csproj* . Quando viene aggiunta al file *.csproj* , la regola viene aggiunta a tutti i profili di pubblicazione del progetto.

L' `<MsDeploySkipRules>` elemento seguente esclude tutti i file dalla cartella *wwwroot\content* :

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

`<MsDeploySkipRules>` non elimina le destinazioni da *ignorare* dal sito di distribuzione. Vengono eliminati dal sito di distribuzione i file e le cartelle di destinazione `<Content>`. Si supponga, ad esempio, che un'app Web distribuita contenga i file seguenti:

* *Views/Home/About1.cshtml*
* *Views/Home/About2.cshtml*
* *Views/Home/About3.cshtml*

Se si aggiungono gli elementi `<MsDeploySkipRules>` seguenti, questi file non vengono eliminati nel sito di distribuzione.

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

Gli elementi `<MsDeploySkipRules>` precedenti impediscono che i file *ignorati* vengano distribuiti. Tali file non verranno eliminati una volta distribuiti.

L'elemento `<Content>` seguente elimina i file di destinazione sul sito di distribuzione:

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

L'uso della distribuzione dalla riga di comando con l'elemento `<Content>` precedente produce una variante dell'output seguente:

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\{TARGET FRAMEWORK MONIKER}\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a>File di inclusione

Le sezioni seguenti illustrano approcci diversi per l'inclusione di file in fase di pubblicazione. La sezione relativa all' [inclusione di file generale](#general-file-inclusion) usa l' `DotNetPublishFiles` elemento, fornito da un file di destinazioni di pubblicazione in [Web SDK](xref:razor-pages/web-sdk). La sezione relativa all' [inclusione di file selettivi](#selective-file-inclusion) usa l' `ResolvedFileToPublish` elemento, fornito da un file di destinazioni di pubblicazione nel [.NET Core SDK](/dotnet/core/project-sdk/msbuild-props). Dato che Web SDK dipende da .NET Core SDK, è possibile usare entrambi gli elementi in un progetto ASP.NET Core.

### <a name="general-file-inclusion"></a>Inclusione di file generale

L'elemento `<ItemGroup>` dell'esempio seguente illustra la copia di una cartella che si trova all'esterno della directory di progetto in una cartella del sito pubblicato. Eventuali file aggiunti a `<ItemGroup>` del markup seguente vengono inclusi per impostazione predefinita.

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

Il markup precedente:

* Può essere aggiunto al file con estensione *csproj* o al profilo di pubblicazione. Se viene aggiunto al file *.csproj* , è incluso in ogni profilo di pubblicazione nel progetto.
* Dichiara un elemento `_CustomFiles` per archiviare i file corrispondenti ai criteri GLOB dell'attributo `Include`. La cartella *images* a cui fa riferimento il modello si trova all'esterno della directory del progetto. Una [proprietà riservata](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), denominata `$(MSBuildProjectDirectory)`, viene risolta nel percorso assoluto del file di progetto.
* Fornisce un elenco di file all'elemento `DotNetPublishFiles`. Per impostazione predefinita, l'elemento `<DestinationRelativePath>` dell'elemento è vuoto. Il valore predefinito è sottoposto a override nel markup e usa [metadati degli elementi noti](/visualstudio/msbuild/msbuild-well-known-item-metadata), ad esempio `%(RecursiveDir)`. Il testo interno rappresenta la cartella *wwwroot/images* del sito pubblicato.

### <a name="selective-file-inclusion"></a>Inclusione di file selettiva

Il markup evidenziato nell'esempio seguente dimostra:

* La copia di un file che si trova all'esterno del progetto nella cartella *wwwroot* del sito pubblicato. Il nome del file *ReadMe2.md* viene mantenuto.
* L'esclusione della cartella *wwwroot\Content* .
* L'esclusione di *Views\Home\About2.cshtml* .

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

L'esempio precedente usa l'elemento `ResolvedFileToPublish`, il cui comportamento predefinito consiste nel copiare sempre i file specificati nell'attributo `Include` nel sito pubblicato. Eseguire l'override del comportamento predefinito, includendo un elemento figlio `<CopyToPublishDirectory>` con il testo interno `Never` o `PreserveNewest`. Ad esempio:

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

Per ulteriori esempi di distribuzione, vedere il [file Leggimi di Web SDK](https://github.com/dotnet/sdk/tree/master/src/WebSdk).

## <a name="run-a-target-before-or-after-publishing"></a>Eseguire una destinazione prima o dopo la pubblicazione

Le destinazioni incorporate `BeforePublish` e `AfterPublish` consentono di eseguire una destinazione prima o dopo la destinazione di pubblicazione. Aggiungere gli elementi seguenti al profilo di pubblicazione per registrare i messaggi della console prima e dopo la pubblicazione:

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a>Pubblicazione in un server tramite un certificato non attendibile

Aggiungere la proprietà `<AllowUntrustedCertificate>` con un valore `True` al profilo di pubblicazione:

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a>Servizio Kudu

Per visualizzare i file in una distribuzione di app Web di Servizio app di Azure, usare il [servizio Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service). Accodare il token `scm` al nome dell'app Web. Ad esempio:

| URL                                    | Risultato       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | App Web      |
| `http://mysite.scm.azurewebsites.net/` | Servizio Kudu |

Selezionare la voce di menu [Console di debug](https://github.com/projectkudu/kudu/wiki/Kudu-console) per visualizzare, modificare, eliminare o aggiungere file.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Distribuzione Web](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) semplifica la distribuzione di app Web e siti Web sui server IIS.
* [Repository GitHub Web SDK](https://github.com/dotnet/websdk/issues): problemi di file e funzionalità di richiesta per la distribuzione.
* [Pubblicare un'app Web ASP.NET in una macchina virtuale di Azure da Visual Studio](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
