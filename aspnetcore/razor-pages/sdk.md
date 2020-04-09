---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: Informazioni su come Razor Pages in ASP.NET Core semplifica e rende più produttiva la scrittura di codice incentrata sulle pagine rispetto a MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
no-loc:
- Blazor
uid: razor-pages/sdk
ms.openlocfilehash: 2284131ce2d45ec6bc01ce38f91e2c951b108605
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80321003"
---
# <a name="aspnet-core-razor-sdk"></a>ASP.NET Core Razor SDK

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Panoramica

[!INCLUDE[](~/includes/2.1-SDK.md)] include MSBuild SDK di `Microsoft.NET.Sdk.Razor` (Razor SDK). Il Razor SDK:

::: moniker range=">= aspnetcore-3.0"

* È necessario compilare, creare pacchetti e pubblicare progetti contenenti file [Razor](xref:mvc/views/razor) per ASP.NET Core mvc-based o [Blazor](xref:blazor/index) progetti.
* Include un set di destinazioni, proprietà ed elementi predefiniti che consentono di personalizzare la compilazione dei file Razor (*.cshtml* o *.razor*).

Razor SDK `Content` include `Include` elementi con `**\*.cshtml` attributi `**\*.razor` impostati su modelli e globbing. I file corrispondenti vengono pubblicati.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Consente di standardizzare l'esperienza in termini di compilazione, creazione dei pacchetti e pubblicazione dei progetti contenenti file [Razor](xref:mvc/views/razor) per i progetti ASP.NET Core basati su MVC.
* Include un set di destinazioni, proprietà e di elementi predefiniti che consentono di personalizzare la compilazione dei file Razor.

Razor SDK include `Content` un `Include` elemento con `**\*.cshtml` un attributo impostato sul modello di globbing. I file corrispondenti vengono pubblicati.

::: moniker-end

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Utilizzare Razor SDK

La maggior parte delle app Web non è necessaria per fare riferimento in modo esplicito all'SDK Razor.

::: moniker range=">= aspnetcore-3.0"

Per utilizzare Razor SDK per compilare librerie di classi contenenti visualizzazioni Razor o pagine Razor, è consigliabile iniziare con il modello di progetto libreria di classi Razor (RCL). Un RCL utilizzato per compilare file Blazor (*.razor*) richiede in minima parte un riferimento al pacchetto [Microsoft.AspNetCore.Components.](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) Una RCL utilizzata per creare visualizzazioni o pagine Razor (file*con estensione cshtml)* richiede almeno la destinazione `netcoreapp3.0` o versione successiva e include un `FrameworkReference` [metadocumento Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) nel file di progetto.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Per usare il Razor SDK per compilare librerie di classi contenenti visualizzazioni Razor o Razor Pages:

* Usare `Microsoft.NET.Sdk.Razor` anziché `Microsoft.NET.Sdk`:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* In genere, un `Microsoft.AspNetCore.Mvc` riferimento al pacchetto è necessario per ricevere dipendenze aggiuntive necessarie per compilare e compilare pagine Razor e Razor visualizzazioni. Come minimo, il progetto deve aggiungere riferimenti al pacchetto a:A minimum, your project should add package references to:

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  Il `Microsoft.AspNetCore.Razor.Design` pacchetto fornisce le attività di compilazione Razor e le destinazioni per il progetto.

  I pacchetti precedenti sono inclusi in `Microsoft.AspNetCore.Mvc`. Il markup seguente mostra un file di progetto che usa Razor SDK per creare file Razor per un'app ASP.NET Core Razor Pages:

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> I `Microsoft.AspNetCore.Razor.Design` `Microsoft.AspNetCore.Mvc.Razor.Extensions` pacchetti e sono inclusi nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Tuttavia, il `Microsoft.AspNetCore.App` riferimento al pacchetto senza versione fornisce un metapacchetto all'app che non include la versione più recente di `Microsoft.AspNetCore.Razor.Design`. I progetti devono fare `Microsoft.AspNetCore.Razor.Design` riferimento `Microsoft.AspNetCore.Mvc`a una versione coerente di (o ) in modo da includere le correzioni in fase di compilazione più recenti per Razor. Per altre informazioni, vedere [questo problema in GitHub](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Proprietà

Le proprietà seguenti controllano il comportamento del Razor SDK come parte della compilazione del progetto:

* `RazorCompileOnBuild`&ndash; Quando `true`, compila ed emette l'assembly Razor come parte della compilazione del progetto. L'impostazione predefinita è `true`.
* `RazorCompileOnPublish`&ndash; Quando `true`, compila ed genera l'assembly Razor come parte della pubblicazione del progetto. L'impostazione predefinita è `true`.

Le proprietà e gli elementi nella tabella seguente vengono utilizzati per configurare gli input e l'output nell'SDK Razor.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> A partire da ASP.NET Core 3.0, le visualizzazioni MVC `RazorCompileOnBuild` o `RazorCompileOnPublish` le pagine Razor non vengono servite per impostazione predefinita se le proprietà o MSBuild nel file di progetto sono disabilitate. Le applicazioni devono aggiungere un riferimento esplicito al pacchetto [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) se l'app si basa sulla compilazione in fase di esecuzione per elaborare i file *con estensione cshtml.*

::: moniker-end

| Items | Descrizione |
| ----- | ----------- |
| `RazorGenerate` | Elementi elemento (file*con estensione cshtml)* che sono input per la generazione di codice. |
| `RazorComponent` | Elementi dell'elemento (file*con estensione razor)* che vengono immessi per la generazione di codice del componente Razor. |
| `RazorCompile` | Elementi elemento (file*con estensione cs)* che vengono immessi nelle destinazioni di compilazione Razor.Item elements ( .cs files) that are inputs to Razor compilation targets. Consente `ItemGroup` di specificare file aggiuntivi da compilare nell'assembly Razor. |
| `RazorTargetAssemblyAttribute` | Elementi della voce usati per attributi di generazione del codice per l'assembly Razor. Ad esempio:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Elementi dell'elemento aggiunti come risorse incorporate all'assembly Razor generato. |

::: moniker range=">= aspnetcore-3.0"

| Proprietà | Descrizione |
| -------- | ----------- |
| `RazorTargetName` | Nome di file (senza estensione) dell'assembly generato da Razor. |
| `RazorOutputPath` | Directory di output di Razor. |
| `RazorCompileToolset` | Usato per determinare il set di strumenti utilizzato per compilare l'assembly Razor. I valori validi sono `Implicit`, `RazorSDK` e `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Il valore predefinito è `true`. Quando `true`, include i file *web.config*, *.json*e *.cshtml* come contenuto del progetto. Quando si `Microsoft.NET.Sdk.Web`fa riferimento tramite , sono inclusi anche i file in *wwwroot* e i file di configurazione. |
| `EnableDefaultRazorGenerateItems` | Quando è `true`, include i file *cshtml* degli elementi di `Content` negli elementi di `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | Quando `true`, genera un file con `RazorAssemblyAttribute` estensione *cs* contenente gli attributi specificati da e include il file nell'output di compilazione. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Quando è `true`, aggiunge un set predefinito di attributi degli assembly a `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Quando `true`, `RazorGenerate` copia i file degli elementi (*con estensione cshtml*) nella directory di pubblicazione. In genere, i file Razor non sono necessari per un'app pubblicata se partecipano alla compilazione in fase di compilazione o di pubblicazione. L'impostazione predefinita è `false`. |
| `PreserveCompilationReferences` | Quando è `true`, copiare gli elementi dell'assembly di riferimento nella directory di pubblicazione. In genere, gli assembly di riferimento non sono necessari per un'app pubblicata se la compilazione Razor si verifica in fase di compilazione o di pubblicazione. Impostare `true` su se l'app pubblicata richiede la compilazione in fase di esecuzione. Ad esempio, imposta `true` il valore su se l'app modifica i file *con estensione cshtml* in fase di esecuzione o utilizza visualizzazioni incorporate. L'impostazione predefinita è `false`. |
| `IncludeRazorContentInPack` | Quando `true`, tutti gli elementi di contenuto Razor (file*con estensione cshtml)* sono contrassegnati per l'inclusione nel pacchetto NuGet generato. L'impostazione predefinita è `false`. |
| `EmbedRazorGenerateSources` | Quando è `true`, aggiunge gli elementi (*cshtml*) di RazorGenerate come file incorporati nell'assembly Razor generato. L'impostazione predefinita è `false`. |
| `UseRazorBuildServer` | Quando è `true`, usa un processo del server di compilazione permanente per ripartire il lavoro di generazione del codice. Valore predefinito è il valore di `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Quando `true`, l'SDK genera attributi aggiuntivi utilizzati da MVC in fase di esecuzione per eseguire l'individuazione delle parti dell'applicazione. |
| `DefaultWebContentItemExcludes` | Un modello di globbing per gli elementi `Content` elemento che devono essere esclusi dal gruppo di elementi nei progetti destinati al Web o Razor SDK |
| `ExcludeConfigFilesFromBuildOutput` | Quando, `true`i file *con estensione config* e *json* non vengono copiati nella directory di output di compilazione. |
| `AddRazorSupportForMvc` | Quando `true`, configura Razor SDK per aggiungere il supporto per la configurazione MVC necessaria durante la compilazione di applicazioni contenenti visualizzazioni MVC o pagine Razor. Questa proprietà viene impostata in modo implicito per i progetti .NET Core 3.0 o versioni successive destinati a Web SDK |
| `RazorLangVersion` | Versione della lingua Razor di destinazione. |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Proprietà | Descrizione |
| -------- | ----------- |
| `RazorTargetName` | Nome di file (senza estensione) dell'assembly generato da Razor. |
| `RazorOutputPath` | Directory di output di Razor. |
| `RazorCompileToolset` | Usato per determinare il set di strumenti utilizzato per compilare l'assembly Razor. I valori validi sono `Implicit`, `RazorSDK` e `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Il valore predefinito è `true`. Quando `true`, include i file *web.config*, *.json*e *.cshtml* come contenuto del progetto. Quando si `Microsoft.NET.Sdk.Web`fa riferimento tramite , sono inclusi anche i file in *wwwroot* e i file di configurazione. |
| `EnableDefaultRazorGenerateItems` | Quando è `true`, include i file *cshtml* degli elementi di `Content` negli elementi di `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | Quando `true`, genera un file con `RazorAssemblyAttribute` estensione *cs* contenente gli attributi specificati da e include il file nell'output di compilazione. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Quando è `true`, aggiunge un set predefinito di attributi degli assembly a `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Quando `true`, `RazorGenerate` copia i file degli elementi (*con estensione cshtml*) nella directory di pubblicazione. In genere, i file Razor non sono necessari per un'app pubblicata se partecipano alla compilazione in fase di compilazione o di pubblicazione. L'impostazione predefinita è `false`. |
| `CopyRefAssembliesToPublishDirectory` | Quando è `true`, copiare gli elementi dell'assembly di riferimento nella directory di pubblicazione. In genere, gli assembly di riferimento non sono necessari per un'app pubblicata se la compilazione Razor si verifica in fase di compilazione o di pubblicazione. Impostare `true` su se l'app pubblicata richiede la compilazione in fase di esecuzione. Ad esempio, imposta `true` il valore su se l'app modifica i file *con estensione cshtml* in fase di esecuzione o utilizza visualizzazioni incorporate. L'impostazione predefinita è `false`. |
| `IncludeRazorContentInPack` | Quando `true`, tutti gli elementi di contenuto Razor (file*con estensione cshtml)* sono contrassegnati per l'inclusione nel pacchetto NuGet generato. L'impostazione predefinita è `false`. |
| `EmbedRazorGenerateSources` | Quando è `true`, aggiunge gli elementi (*cshtml*) di RazorGenerate come file incorporati nell'assembly Razor generato. L'impostazione predefinita è `false`. |
| `UseRazorBuildServer` | Quando è `true`, usa un processo del server di compilazione permanente per ripartire il lavoro di generazione del codice. Valore predefinito è il valore di `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Quando `true`, l'SDK genera attributi aggiuntivi utilizzati da MVC in fase di esecuzione per eseguire l'individuazione delle parti dell'applicazione. |
| `DefaultWebContentItemExcludes` | Un modello di globbing per gli elementi `Content` elemento che devono essere esclusi dal gruppo di elementi nei progetti destinati al Web o Razor SDK |
| `ExcludeConfigFilesFromBuildOutput` | Quando, `true`i file *con estensione config* e *json* non vengono copiati nella directory di output di compilazione. |
| `AddRazorSupportForMvc` | Quando `true`, configura Razor SDK per aggiungere il supporto per la configurazione MVC necessaria durante la compilazione di applicazioni contenenti visualizzazioni MVC o pagine Razor. Questa proprietà viene impostata in modo implicito per i progetti .NET Core 3.0 o versioni successive destinati a Web SDK |
| `RazorLangVersion` | Versione della lingua Razor di destinazione. |

::: moniker-end

Per ulteriori informazioni sulle proprietà, vedere [Proprietà MSBuild](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Server di destinazione

Il Razor SDK definisce due obiettivi principali:

* `RazorGenerate`&ndash; Il codice genera `RazorGenerate` file con estensione *cs* dagli elementi dell'elemento. Utilizzare `RazorGenerateDependsOn` la proprietà per specificare destinazioni aggiuntive che possono essere eseguite prima o dopo questa destinazione.
* `RazorCompile`&ndash; Compila i file con estensione cs generati in un assembly Razor.Compiles generated *.cs* files in to a Razor assembly. Utilizzare `RazorCompileDependsOn` l'opzione per specificare destinazioni aggiuntive che possono essere eseguite prima o dopo questa destinazione.
* `RazorComponentGenerate`&ndash; Il codice genera `RazorComponent` file con estensione *cs* per gli elementi dell'elemento. Utilizzare `RazorComponentGenerateDependsOn` la proprietà per specificare destinazioni aggiuntive che possono essere eseguite prima o dopo questa destinazione.

### <a name="runtime-compilation-of-razor-views"></a>Compilazione runtime di visualizzazioni Razor

* Per impostazione predefinita, Razor SDK non pubblica gli assembly di riferimento necessari per eseguire la compilazione runtime. Vengono pertanto generati errori di compilazione nel caso in cui il modello di applicazione si basi su una compilazione runtime&mdash;ad esempio, l'app usa visualizzazioni incorporate o modifica le visualizzazioni dopo aver pubblicato l'app. Impostare `CopyRefAssembliesToPublishDirectory` su `true` per continuare con la pubblicazione degli assembly di riferimento.

* Per un'app Web, assicurati che `Microsoft.NET.Sdk.Web` l'app sia destinata all'SDK.

## <a name="razor-language-version"></a>Versione in lingua Razor

Quando scegli `Microsoft.NET.Sdk.Web` come destinazione l'SDK, la versione in lingua Razor viene dedotta dalla versione del framework di destinazione dell'app. Per i progetti `Microsoft.NET.Sdk.Razor` destinati all'SDK o nel raro caso in cui l'app richieda una versione `<RazorLangVersion>` in lingua Razor diversa da quella del valore dedotto, è possibile configurare una versione impostando la proprietà nel file di progetto dell'app:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

La versione in lingua di Razor è strettamente integrata con la versione del runtime per cui è stata creata. La destinazione di una versione del linguaggio non progettata per il runtime non è supportata e probabilmente produce errori di compilazione.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Aggiunte al formato csproj per .NET Core](/dotnet/core/tools/csproj)
* [Elementi di progetto MSBuild comuniCommon MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items)
