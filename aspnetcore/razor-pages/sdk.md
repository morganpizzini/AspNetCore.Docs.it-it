---
title: SDK di ASP.NET Core Razor
author: Rick-Anderson
description: Informazioni Razor sul modo in cui le pagine ASP.NET Core rendono più semplici e più produttivi gli scenari incentrati sulla pagina della codifica rispetto all'utilizzo di MVC
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
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
uid: razor-pages/sdk
ms.openlocfilehash: b960460a50558a11bc47f9a1844931aa32e3d696
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021419"
---
# <a name="aspnet-core-no-locrazor-sdk"></a>SDK di ASP.NET Core Razor

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Panoramica

[!INCLUDE[](~/includes/2.1-SDK.md)]Include l' `Microsoft.NET.Sdk.Razor` SDK di MSBuild ( Razor SDK). RazorSDK:

::: moniker range=">= aspnetcore-3.0"

* È necessario per compilare, creare pacchetti e pubblicare progetti contenenti [Razor](xref:mvc/views/razor) file per ASP.NET Core progetti basati su MVC o [Blazor](xref:blazor/index) .
* Include un set di destinazioni, proprietà ed elementi predefiniti che consentono di personalizzare la compilazione di Razor file (*. cshtml* o *. Razor*).

L' Razor SDK include `Content` gli elementi con `Include` attributi impostati sui `**\*.cshtml` `**\*.razor` modelli glob e. I file corrispondenti vengono pubblicati.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Standardizza l'esperienza di compilazione, creazione di pacchetti e pubblicazione di progetti contenenti [Razor](xref:mvc/views/razor) file per ASP.NET Core progetti basati su MVC.
* Include un set di destinazioni, proprietà ed elementi predefiniti che consentono di personalizzare la compilazione dei Razor file.

L' Razor SDK include un `Content` elemento con un `Include` attributo impostato sul `**\*.cshtml` modello glob. I file corrispondenti vengono pubblicati.

::: moniker-end

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-no-locrazor-sdk"></a>Usare l' Razor SDK

La maggior parte delle app Web non è necessaria per fare riferimento in modo esplicito all' Razor SDK.

::: moniker range=">= aspnetcore-3.0"

Per usare l' Razor SDK per compilare librerie di classi contenenti Razor viste o Razor pagine, è consigliabile iniziare con il Razor modello di progetto libreria di classi (RCL). Un RCL usato per compilare Blazor i file (*Razor*) richiede almeno un riferimento al pacchetto [Microsoft. AspNetCore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) . Un RCL usato per compilare Razor viste o pagine (file con*estensione cshtml* ) richiede almeno `netcoreapp3.0` la destinazione o una versione successiva e presenta un `FrameworkReference` al [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) nel file di progetto.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Per utilizzare l' Razor SDK per compilare librerie di classi contenenti Razor viste o Razor pagine:

* Usare `Microsoft.NET.Sdk.Razor` anziché `Microsoft.NET.Sdk`:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* In genere, per `Microsoft.AspNetCore.Mvc` ricevere dipendenze aggiuntive necessarie per compilare e compilare Razor pagine e visualizzazioni è necessario un riferimento a un pacchetto a Razor . Come minimo, il progetto deve aggiungere riferimenti a pacchetti:

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  Il `Microsoft.AspNetCore.Razor.Design` pacchetto fornisce le Razor attività e le destinazioni di compilazione per il progetto.

  I pacchetti precedenti sono inclusi in `Microsoft.AspNetCore.Mvc`. Il markup seguente illustra un file di progetto che usa l' Razor SDK per compilare Razor i file per un'app di ASP.NET Core Razor pages:

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> I `Microsoft.AspNetCore.Razor.Design` `Microsoft.AspNetCore.Mvc.Razor.Extensions` pacchetti e sono inclusi nel [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app). Tuttavia, il riferimento al pacchetto senza versione `Microsoft.AspNetCore.App` fornisce un metapacchetto all'app che non include la versione più recente di `Microsoft.AspNetCore.Razor.Design` . I progetti devono fare riferimento a una versione coerente di `Microsoft.AspNetCore.Razor.Design` (o `Microsoft.AspNetCore.Mvc` ) in modo da includere le correzioni più recenti della fase di compilazione per Razor . Per altre informazioni, vedere [questo problema di GitHub](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Proprietà

Le proprietà seguenti controllano il Razor comportamento dell'SDK come parte della compilazione di un progetto:

* `RazorCompileOnBuild`: Quando `true` , compila ed emette l' Razor assembly come parte della compilazione del progetto. Il valore predefinito è `true`.
* `RazorCompileOnPublish`: Quando `true` , compila ed emette l' Razor assembly come parte della pubblicazione del progetto. Il valore predefinito è `true`.

Le proprietà e gli elementi nella tabella seguente vengono usati per configurare gli input e l'output per l' Razor SDK.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> A partire da ASP.NET Core 3,0, le viste o Razor le pagine MVC non vengono gestite per impostazione predefinita se le `RazorCompileOnBuild` `RazorCompileOnPublish` proprietà o MSBuild nel file di progetto sono disabilitate. Le applicazioni devono aggiungere un riferimento esplicito a [Microsoft. AspNetCore. Mvc. Razor Pacchetto RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) se l'app si basa sulla compilazione di runtime per elaborare i file *. cshtml* .

::: moniker-end

| Elementi | Descrizione |
| ----- | ----------- |
| `RazorGenerate` | Elementi Item (file con*estensione cshtml* ) che sono input per la generazione di codice. |
| `RazorComponent` | Elementi elemento (file*Razor* ) che sono input per la Razor generazione di codice componente. |
| `RazorCompile` | Elementi Item (file con*estensione cs* ) che sono input per le Razor destinazioni di compilazione. Usare questa `ItemGroup` impostazione per specificare i file aggiuntivi da compilare nell' Razor assembly. |
| `RazorTargetAssemblyAttribute` | Elementi Item utilizzati per generare gli attributi per l' Razor assembly. Ad esempio:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Elementi elemento aggiunti come risorse incorporate all' Razor assembly generato. |

::: moniker range=">= aspnetcore-3.0"

| Proprietà | Descrizione |
| -------- | ----------- |
| `RazorTargetName` | Nome file (senza estensione) dell'assembly prodotto da Razor . |
| `RazorOutputPath` | RazorDirectory di output. |
| `RazorCompileToolset` | Utilizzato per determinare il set di strumenti utilizzato per compilare l' Razor assembly. I valori validi sono `Implicit`, `RazorSDK` e `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Il valore predefinito è `true`. Quando `true` , include i file *web.config*, *. JSON*e *. cshtml* come contenuto nel progetto. Quando viene fatto riferimento tramite `Microsoft.NET.Sdk.Web` , vengono inclusi anche i file in *wwwroot* e i file di configurazione. |
| `EnableDefaultRazorGenerateItems` | Quando è `true`, include i file *cshtml* degli elementi di `Content` negli elementi di `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | Quando `true` , genera un file con *estensione cs* contenente gli attributi specificati da `RazorAssemblyAttribute` e include il file nell'output di compilazione. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Quando è `true`, aggiunge un set predefinito di attributi degli assembly a `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Quando `true` , copia `RazorGenerate` gli elementi (file con*estensione cshtml*) nella directory di pubblicazione. In genere, Razor i file non sono necessari per un'app pubblicata se partecipano alla compilazione in fase di compilazione o di pubblicazione. Il valore predefinito è `false`. |
| `PreserveCompilationReferences` | Quando è `true`, copiare gli elementi dell'assembly di riferimento nella directory di pubblicazione. Gli assembly di riferimento non sono in genere necessari per un'app pubblicata se la Razor compilazione avviene in fase di compilazione o di pubblicazione. Impostare su `true` se l'app pubblicata richiede la compilazione in fase di esecuzione. Ad esempio, impostare il valore su `true` se l'app modifica i file *cshtml* in fase di esecuzione o usa le visualizzazioni incorporate. Il valore predefinito è `false`. |
| `IncludeRazorContentInPack` | Se `true` , tutti Razor gli elementi di contenuto (file con*estensione cshtml* ) sono contrassegnati per l'inclusione nel pacchetto NuGet generato. Il valore predefinito è `false`. |
| `EmbedRazorGenerateSources` | Quando `true` , aggiunge Razor gli elementi di generazione (*. cshtml*) come file incorporati all' Razor assembly generato. Il valore predefinito è `false`. |
| `UseRazorBuildServer` | Quando è `true`, usa un processo del server di compilazione permanente per ripartire il lavoro di generazione del codice. Valore predefinito è il valore di `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Quando `true` , l'SDK genera attributi aggiuntivi usati da MVC in fase di esecuzione per eseguire l'individuazione delle parti dell'applicazione. |
| `DefaultWebContentItemExcludes` | Modello glob per gli elementi elemento che devono essere esclusi dal `Content` gruppo di elementi nei progetti destinati a Web o Razor SDK |
| `ExcludeConfigFilesFromBuildOutput` | Quando `true` , i file *. config* e *. JSON* non vengono copiati nella directory dell'output di compilazione. |
| `AddRazorSupportForMvc` | Quando `true` , configura l' Razor SDK per aggiungere il supporto per la configurazione MVC richiesta quando si compilano applicazioni contenenti visualizzazioni o Razor pagine MVC. Questa proprietà viene impostata in modo implicito per i progetti .NET Core 3,0 o versioni successive destinate a Web SDK |
| `RazorLangVersion` | Versione della lingua di Razor destinazione. |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Proprietà | Descrizione |
| -------- | ----------- |
| `RazorTargetName` | Nome file (senza estensione) dell'assembly prodotto da Razor . |
| `RazorOutputPath` | RazorDirectory di output. |
| `RazorCompileToolset` | Utilizzato per determinare il set di strumenti utilizzato per compilare l' Razor assembly. I valori validi sono `Implicit`, `RazorSDK` e `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Il valore predefinito è `true`. Quando `true` , include i file *web.config*, *. JSON*e *. cshtml* come contenuto nel progetto. Quando viene fatto riferimento tramite `Microsoft.NET.Sdk.Web` , vengono inclusi anche i file in *wwwroot* e i file di configurazione. |
| `EnableDefaultRazorGenerateItems` | Quando è `true`, include i file *cshtml* degli elementi di `Content` negli elementi di `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | Quando `true` , genera un file con *estensione cs* contenente gli attributi specificati da `RazorAssemblyAttribute` e include il file nell'output di compilazione. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Quando è `true`, aggiunge un set predefinito di attributi degli assembly a `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Quando `true` , copia `RazorGenerate` gli elementi (file con*estensione cshtml*) nella directory di pubblicazione. In genere, Razor i file non sono necessari per un'app pubblicata se partecipano alla compilazione in fase di compilazione o di pubblicazione. Il valore predefinito è `false`. |
| `CopyRefAssembliesToPublishDirectory` | Quando è `true`, copiare gli elementi dell'assembly di riferimento nella directory di pubblicazione. Gli assembly di riferimento non sono in genere necessari per un'app pubblicata se la Razor compilazione avviene in fase di compilazione o di pubblicazione. Impostare su `true` se l'app pubblicata richiede la compilazione in fase di esecuzione. Ad esempio, impostare il valore su `true` se l'app modifica i file *cshtml* in fase di esecuzione o usa le visualizzazioni incorporate. Il valore predefinito è `false`. |
| `IncludeRazorContentInPack` | Se `true` , tutti Razor gli elementi di contenuto (file con*estensione cshtml* ) sono contrassegnati per l'inclusione nel pacchetto NuGet generato. Il valore predefinito è `false`. |
| `EmbedRazorGenerateSources` | Quando `true` , aggiunge Razor gli elementi di generazione (*. cshtml*) come file incorporati all' Razor assembly generato. Il valore predefinito è `false`. |
| `UseRazorBuildServer` | Quando è `true`, usa un processo del server di compilazione permanente per ripartire il lavoro di generazione del codice. Valore predefinito è il valore di `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Quando `true` , l'SDK genera attributi aggiuntivi usati da MVC in fase di esecuzione per eseguire l'individuazione delle parti dell'applicazione. |
| `DefaultWebContentItemExcludes` | Modello glob per gli elementi elemento che devono essere esclusi dal `Content` gruppo di elementi nei progetti destinati a Web o Razor SDK |
| `ExcludeConfigFilesFromBuildOutput` | Quando `true` , i file *. config* e *. JSON* non vengono copiati nella directory dell'output di compilazione. |
| `AddRazorSupportForMvc` | Quando `true` , configura l' Razor SDK per aggiungere il supporto per la configurazione MVC richiesta quando si compilano applicazioni contenenti visualizzazioni o Razor pagine MVC. Questa proprietà viene impostata in modo implicito per i progetti .NET Core 3,0 o versioni successive destinate a Web SDK |
| `RazorLangVersion` | Versione della lingua di Razor destinazione. |

::: moniker-end

Per altre informazioni sulle proprietà, vedere [proprietà di MSBuild](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Destinazioni

L' Razor SDK definisce due destinazioni primarie:

* `RazorGenerate`: Il codice genera file con *estensione cs* da `RazorGenerate` elementi elemento. Utilizzare la `RazorGenerateDependsOn` proprietà per specificare destinazioni aggiuntive che possono essere eseguite prima o dopo questa destinazione.
* `RazorCompile`: Compila i file con *estensione cs* generati in un Razor assembly. Usare `RazorCompileDependsOn` per specificare destinazioni aggiuntive che possono essere eseguite prima o dopo questa destinazione.
* `RazorComponentGenerate`: Il codice genera file con *estensione cs* per `RazorComponent` gli elementi elemento. Utilizzare la `RazorComponentGenerateDependsOn` proprietà per specificare destinazioni aggiuntive che possono essere eseguite prima o dopo questa destinazione.

### <a name="runtime-compilation-of-no-locrazor-views"></a>Compilazione di runtime delle Razor visualizzazioni

* Per impostazione predefinita, l' Razor SDK non pubblica gli assembly di riferimento necessari per eseguire la compilazione del runtime. Vengono pertanto generati errori di compilazione nel caso in cui il modello di applicazione si basi su una compilazione runtime&mdash;ad esempio, l'app usa visualizzazioni incorporate o modifica le visualizzazioni dopo aver pubblicato l'app. Impostare `CopyRefAssembliesToPublishDirectory` su `true` per continuare con la pubblicazione degli assembly di riferimento.

* Per un'app Web, verificare che l'app sia destinata all' `Microsoft.NET.Sdk.Web` SDK.

## <a name="no-locrazor-language-version"></a>Razorversione lingua

Quando la destinazione `Microsoft.NET.Sdk.Web` è SDK, la Razor versione del linguaggio viene dedotta dalla versione del Framework di destinazione dell'app. Per i progetti destinati all' `Microsoft.NET.Sdk.Razor` SDK o nel raro caso in cui l'app richieda una versione della lingua diversa da quella Razor del valore derivato, è possibile configurare una versione impostando la `<RazorLangVersion>` proprietà nel file di progetto dell'app:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Razorla versione del linguaggio è strettamente integrata con la versione del runtime per cui è stata creata. La destinazione di una versione di linguaggio non progettata per il runtime non è supportata e probabilmente genera errori di compilazione.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Aggiunte al formato csproj per .NET Core](/dotnet/core/tools/csproj)
* [Elementi di progetto MSBuild comuni](/visualstudio/msbuild/common-msbuild-project-items)
