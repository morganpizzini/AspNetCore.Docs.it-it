---
title: SDK Razor di ASP.NET Core
author: Rick-Anderson
description: Informazioni sul Razor modo in cui le pagine ASP.NET Core rendono più semplici e più produttivi gli scenari incentrati sulla pagina della codifica rispetto all'utilizzo di MVC
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/sdk
ms.openlocfilehash: a49f183c4c037a1654e79bdb672b758684137cbe
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776734"
---
# <a name="aspnet-core-razor-sdk"></a>ASP.NET Core Razor SDK

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Panoramica

[!INCLUDE[](~/includes/2.1-SDK.md)] include MSBuild SDK di `Microsoft.NET.Sdk.Razor` (Razor SDK). Il Razor SDK:

::: moniker range=">= aspnetcore-3.0"

* È necessario per compilare, creare un pacchetto e pubblicare progetti contenenti file [Razor](xref:mvc/views/razor) per ASP.NET Core progetti basati su MVC o [Blazer](xref:blazor/index) .
* Include un set di destinazioni, proprietà ed elementi predefiniti che consentono di personalizzare la compilazione dei file Razor (*. cshtml* o *. Razor*).

Razor SDK include `Content` gli elementi con `Include` attributi impostati sui `**\*.cshtml` modelli glob `**\*.razor` e. I file corrispondenti vengono pubblicati.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Consente di standardizzare l'esperienza in termini di compilazione, creazione dei pacchetti e pubblicazione dei progetti contenenti file [Razor](xref:mvc/views/razor) per i progetti ASP.NET Core basati su MVC.
* Include un set di destinazioni, proprietà e di elementi predefiniti che consentono di personalizzare la compilazione dei file Razor.

Razor SDK include un `Content` elemento con un `Include` attributo impostato sul `**\*.cshtml` modello glob. I file corrispondenti vengono pubblicati.

::: moniker-end

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Usare Razor SDK

La maggior parte delle app Web non è necessaria per fare riferimento in modo esplicito a Razor SDK.

::: moniker range=">= aspnetcore-3.0"

Per usare Razor SDK per compilare librerie di classi contenenti visualizzazioni Razor o Razor Pages, è consigliabile iniziare con il modello di progetto libreria di classi Razor (RCL). Un RCL usato per compilare file blazer (*Razor*) richiede almeno un riferimento al pacchetto [Microsoft. AspNetCore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) . Un RCL usato per compilare visualizzazioni o pagine Razor (file con*estensione cshtml* ) richiede almeno la destinazione `netcoreapp3.0` o una versione successiva e dispone di `FrameworkReference` un oggetto nel [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) nel file di progetto.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Per usare il Razor SDK per compilare librerie di classi contenenti visualizzazioni Razor o Razor Pages:

* Usare `Microsoft.NET.Sdk.Razor` anziché `Microsoft.NET.Sdk`:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* In genere, un riferimento al `Microsoft.AspNetCore.Mvc` pacchetto a è necessario per ricevere dipendenze aggiuntive necessarie per compilare e compilare Razor Pages e visualizzazioni Razor. Come minimo, il progetto deve aggiungere riferimenti a pacchetti:

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  Il `Microsoft.AspNetCore.Razor.Design` pacchetto fornisce le destinazioni e le attività di compilazione Razor per il progetto.

  I pacchetti precedenti sono inclusi in `Microsoft.AspNetCore.Mvc`. Il markup seguente illustra un file di progetto che usa Razor SDK per compilare file Razor per un'app Razor Pages ASP.NET Core:

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> I `Microsoft.AspNetCore.Razor.Design` pacchetti `Microsoft.AspNetCore.Mvc.Razor.Extensions` e sono inclusi nel [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app). Tuttavia, il riferimento al pacchetto `Microsoft.AspNetCore.App` senza versione fornisce un metapacchetto all'app che non include la versione più recente di `Microsoft.AspNetCore.Razor.Design`. I progetti devono fare riferimento a una `Microsoft.AspNetCore.Razor.Design` versione coerente `Microsoft.AspNetCore.Mvc`di (o) in modo che vengano incluse le correzioni più recenti della fase di compilazione per Razor. Per altre informazioni, vedere [questo problema di GitHub](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Proprietà

Le proprietà seguenti controllano il comportamento del Razor SDK come parte della compilazione del progetto:

* `RazorCompileOnBuild`&ndash; Quando `true`, compila ed emette l'assembly Razor come parte della compilazione del progetto. Il valore predefinito è `true`.
* `RazorCompileOnPublish`&ndash; Quando `true`, compila ed emette l'assembly Razor come parte della pubblicazione del progetto. Il valore predefinito è `true`.

Le proprietà e gli elementi nella tabella seguente vengono usati per configurare gli input e l'output per Razor SDK.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> A partire da ASP.NET Core 3,0, le visualizzazioni MVC o Razor Pages non vengono gestite per `RazorCompileOnBuild` impostazione `RazorCompileOnPublish` predefinita se le proprietà o MSBuild nel file di progetto sono disabilitate. Le applicazioni devono aggiungere un riferimento esplicito al pacchetto [Microsoft. AspNetCore. Mvc. Razor. RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) se l'app si basa sulla compilazione di runtime per elaborare i file con *estensione cshtml* .

::: moniker-end

| Items | Descrizione |
| ----- | ----------- |
| `RazorGenerate` | Elementi Item (file con*estensione cshtml* ) che sono input per la generazione di codice. |
| `RazorComponent` | Elementi elemento (file*Razor* ) che sono input per la generazione di codice del componente Razor. |
| `RazorCompile` | Elementi Item (file con*estensione cs* ) che sono input per le destinazioni di compilazione Razor. Usare questa `ItemGroup` impostazione per specificare i file aggiuntivi da compilare nell'assembly Razor. |
| `RazorTargetAssemblyAttribute` | Elementi della voce usati per attributi di generazione del codice per l'assembly Razor. Ad esempio:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Elementi elemento aggiunti come risorse incorporate all'assembly Razor generato. |

::: moniker range=">= aspnetcore-3.0"

| Proprietà | Descrizione |
| -------- | ----------- |
| `RazorTargetName` | Nome di file (senza estensione) dell'assembly generato da Razor. |
| `RazorOutputPath` | Directory di output di Razor. |
| `RazorCompileToolset` | Usato per determinare il set di strumenti utilizzato per compilare l'assembly Razor. I valori validi sono `Implicit`, `RazorSDK` e `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Il valore predefinito è `true`. Quando `true`, include i file *Web. config*, *. JSON*e *. cshtml* come contenuto nel progetto. Quando viene fatto riferimento `Microsoft.NET.Sdk.Web`tramite, vengono inclusi anche i file in *wwwroot* e i file di configurazione. |
| `EnableDefaultRazorGenerateItems` | Quando è `true`, include i file *cshtml* degli elementi di `Content` negli elementi di `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | Quando `true`, genera un file con *estensione cs* contenente gli attributi `RazorAssemblyAttribute` specificati da e include il file nell'output di compilazione. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Quando è `true`, aggiunge un set predefinito di attributi degli assembly a `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Quando `true`, copia `RazorGenerate` gli elementi (file con*estensione cshtml*) nella directory di pubblicazione. In genere, i file Razor non sono necessari per un'app pubblicata se partecipano alla compilazione in fase di compilazione o di pubblicazione. Il valore predefinito è `false`. |
| `PreserveCompilationReferences` | Quando è `true`, copiare gli elementi dell'assembly di riferimento nella directory di pubblicazione. Gli assembly di riferimento non sono in genere necessari per un'app pubblicata se la compilazione Razor viene eseguita in fase di compilazione o di pubblicazione. Impostare su `true` se l'app pubblicata richiede la compilazione in fase di esecuzione. Ad esempio, impostare il valore su `true` se l'app modifica i file *cshtml* in fase di esecuzione o usa le visualizzazioni incorporate. Il valore predefinito è `false`. |
| `IncludeRazorContentInPack` | Se `true`, tutti gli elementi di contenuto Razor (file con*estensione cshtml* ) sono contrassegnati per l'inclusione nel pacchetto NuGet generato. Il valore predefinito è `false`. |
| `EmbedRazorGenerateSources` | Quando è `true`, aggiunge gli elementi (*cshtml*) di RazorGenerate come file incorporati nell'assembly Razor generato. Il valore predefinito è `false`. |
| `UseRazorBuildServer` | Quando è `true`, usa un processo del server di compilazione permanente per ripartire il lavoro di generazione del codice. Valore predefinito è il valore di `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Quando `true`, l'SDK genera attributi aggiuntivi usati da MVC in fase di esecuzione per eseguire l'individuazione delle parti dell'applicazione. |
| `DefaultWebContentItemExcludes` | Modello glob per gli elementi elemento che devono essere esclusi dal gruppo `Content` di elementi nei progetti destinati a Web o Razor SDK |
| `ExcludeConfigFilesFromBuildOutput` | Quando `true`, i file *. config* e *. JSON* non vengono copiati nella directory dell'output di compilazione. |
| `AddRazorSupportForMvc` | Quando `true`, configura Razor SDK per aggiungere il supporto per la configurazione MVC richiesta quando si compilano applicazioni contenenti visualizzazioni MVC o Razor Pages. Questa proprietà viene impostata in modo implicito per i progetti .NET Core 3,0 o versioni successive destinate a Web SDK |
| `RazorLangVersion` | Versione della lingua Razor di destinazione. |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Proprietà | Descrizione |
| -------- | ----------- |
| `RazorTargetName` | Nome file (senza estensione) dell'assembly prodotto da Razor. |
| `RazorOutputPath` | Directory Razor di output. |
| `RazorCompileToolset` | Utilizzato per determinare il set di strumenti utilizzato per Razor compilare l'assembly. I valori validi sono `Implicit`, `RazorSDK` e `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Il valore predefinito è `true`. Quando `true`, include i file *Web. config*, *. JSON*e *. cshtml* come contenuto nel progetto. Quando viene fatto riferimento `Microsoft.NET.Sdk.Web`tramite, vengono inclusi anche i file in *wwwroot* e i file di configurazione. |
| `EnableDefaultRazorGenerateItems` | Quando è `true`, include i file *cshtml* degli elementi di `Content` negli elementi di `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | Quando `true`, genera un file con *estensione cs* contenente gli attributi `RazorAssemblyAttribute` specificati da e include il file nell'output di compilazione. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Quando è `true`, aggiunge un set predefinito di attributi degli assembly a `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Quando `true`, copia `RazorGenerate` gli elementi (file con*estensione cshtml*) nella directory di pubblicazione. In genere Razor , i file non sono necessari per un'app pubblicata se partecipano alla compilazione in fase di compilazione o di pubblicazione. Il valore predefinito è `false`. |
| `CopyRefAssembliesToPublishDirectory` | Quando è `true`, copiare gli elementi dell'assembly di riferimento nella directory di pubblicazione. Gli assembly di riferimento non sono in genere necessari per un' Razor app pubblicata se la compilazione avviene in fase di compilazione o di pubblicazione. Impostare su `true` se l'app pubblicata richiede la compilazione in fase di esecuzione. Ad esempio, impostare il valore su `true` se l'app modifica i file *cshtml* in fase di esecuzione o usa le visualizzazioni incorporate. Il valore predefinito è `false`. |
| `IncludeRazorContentInPack` | Se `true`, tutti Razor gli elementi di contenuto (file con*estensione cshtml* ) sono contrassegnati per l'inclusione nel pacchetto NuGet generato. Il valore predefinito è `false`. |
| `EmbedRazorGenerateSources` | Quando `true`, aggiunge gli elementi RazorGenerate (*. cshtml*) come file incorporati all' Razor assembly generato. Il valore predefinito è `false`. |
| `UseRazorBuildServer` | Quando è `true`, usa un processo del server di compilazione permanente per ripartire il lavoro di generazione del codice. Valore predefinito è il valore di `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Quando `true`, l'SDK genera attributi aggiuntivi usati da MVC in fase di esecuzione per eseguire l'individuazione delle parti dell'applicazione. |
| `DefaultWebContentItemExcludes` | Modello glob per gli elementi elemento che devono essere esclusi dal gruppo `Content` di elementi nei progetti destinati a Web o Razor SDK |
| `ExcludeConfigFilesFromBuildOutput` | Quando `true`, i file *. config* e *. JSON* non vengono copiati nella directory dell'output di compilazione. |
| `AddRazorSupportForMvc` | Quando `true`, configura l' Razor SDK per aggiungere il supporto per la configurazione MVC richiesta quando si compilano applicazioni contenenti visualizzazioni o Razor pagine MVC. Questa proprietà viene impostata in modo implicito per i progetti .NET Core 3,0 o versioni successive destinate a Web SDK |
| `RazorLangVersion` | Versione della Razor lingua di destinazione. |

::: moniker-end

Per altre informazioni sulle proprietà, vedere [proprietà di MSBuild](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Server di destinazione

L' Razor SDK definisce due destinazioni primarie:

* `RazorGenerate`&ndash; Il codice genera file con *estensione cs* da `RazorGenerate` elementi elemento. Utilizzare la `RazorGenerateDependsOn` proprietà per specificare destinazioni aggiuntive che possono essere eseguite prima o dopo questa destinazione.
* `RazorCompile`&ndash; Compila i file con *estensione cs* generati in un Razor assembly. Usare `RazorCompileDependsOn` per specificare destinazioni aggiuntive che possono essere eseguite prima o dopo questa destinazione.
* `RazorComponentGenerate`&ndash; Il codice genera file con *estensione cs* per `RazorComponent` gli elementi Item. Utilizzare la `RazorComponentGenerateDependsOn` proprietà per specificare destinazioni aggiuntive che possono essere eseguite prima o dopo questa destinazione.

### <a name="runtime-compilation-of-razor-views"></a>Compilazione di runtime Razor delle visualizzazioni

* Per impostazione predefinita, Razor l'SDK non pubblica gli assembly di riferimento necessari per eseguire la compilazione del runtime. Vengono pertanto generati errori di compilazione nel caso in cui il modello di applicazione si basi su una compilazione runtime&mdash;ad esempio, l'app usa visualizzazioni incorporate o modifica le visualizzazioni dopo aver pubblicato l'app. Impostare `CopyRefAssembliesToPublishDirectory` su `true` per continuare con la pubblicazione degli assembly di riferimento.

* Per un'app Web, verificare che l'app sia destinata `Microsoft.NET.Sdk.Web` all'SDK.

## <a name="razor-language-version"></a>Razorversione lingua

Quando la destinazione è `Microsoft.NET.Sdk.Web` SDK, la Razor versione del linguaggio viene dedotta dalla versione del Framework di destinazione dell'app. Per i progetti destinati all' `Microsoft.NET.Sdk.Razor` SDK o nel raro caso in cui l'app richieda una Razor versione della lingua diversa da quella del valore derivato, è possibile configurare una versione impostando `<RazorLangVersion>` la proprietà nel file di progetto dell'app:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Razorla versione del linguaggio è strettamente integrata con la versione del runtime per cui è stata creata. La destinazione di una versione di linguaggio non progettata per il runtime non è supportata e probabilmente genera errori di compilazione.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Aggiunte al formato csproj per .NET Core](/dotnet/core/tools/csproj)
* [Elementi di progetto MSBuild comuni](/visualstudio/msbuild/common-msbuild-project-items)
