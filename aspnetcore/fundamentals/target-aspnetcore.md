---
title: Usare le API di ASP.NET base in una libreria di classiUse a Core APIs in a class library
author: scottaddie
description: Informazioni su come usare le API di ASP.NET Core in una libreria di classi.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: 5374d7eec4334223a4bba7ee26cb6e2f208ed20b
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977197"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a>Usare le API di ASP.NET base in una libreria di classiUse a Core APIs in a class library

Di [Scott Addie](https://github.com/scottaddie)

Questo documento fornisce indicazioni per l'uso di API di ASP.NET base in una libreria di classi. Per tutte le altre linee guida per la libreria, consultate Linee guida per la [libreria Open-source.](/dotnet/standard/library-guidance/)

## <a name="determine-which-aspnet-core-versions-to-support"></a>Determinare le versioni di ASP.NET Core da supportareDetermine which a Core versions to support

ASP.NET Core aderisce ai criteri di supporto di [.NET Core.](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) Consultare i criteri di supporto per determinare le versioni di ASP.NET Core da supportare in una libreria. Una libreria deve:

* Fare uno sforzo per supportare tutte le versioni ASP.NET Core classificate come *supporto a lungo termine* (LTS).
* Non sentirsi obbligato a supportare ASP.NET versioni di base classificate come *End of Life* (EOL).

Man mano che vengono rese disponibili le versioni di anteprima di ASP.NET Core, le modifiche di rilievo vengono pubblicate nel repository [GitHub aspnet/Annunci.](https://github.com/aspnet/Announcements/issues) Il test di compatibilità delle librerie può essere condotto durante lo sviluppo delle funzionalità del framework.

## <a name="use-the-aspnet-core-shared-framework"></a>Usare il framework condiviso ASP.NET CoreUse the ASP.NET Core shared framework

Con il rilascio di .NET Core 3.0, molti assembly ASP.NET Core non vengono più pubblicati in NuGet come pacchetti. Al contrario, gli `Microsoft.AspNetCore.App` assembly sono inclusi nel framework condiviso, che viene installato con .NET Core SDK e programmi di installazione di runtime. Per un elenco dei pacchetti non più pubblicati, consultate Rimuovere i riferimenti ai [pacchetti obsoleti.](xref:migration/22-to-30#remove-obsolete-package-references)

A partire da .NET Core 3.0, i progetti che utilizzano `Microsoft.NET.Sdk.Web` MSBuild SDK fanno riferimento in modo implicito al framework condiviso. I progetti `Microsoft.NET.Sdk` `Microsoft.NET.Sdk.Razor` che usano l'SDK devono fare riferimento a ASP.NET Core per usare le API di ASP.NET Core nel framework condiviso.

Per fare riferimento a `<FrameworkReference>` ASP.NET Core, aggiungere l'elemento seguente al file di progetto:To reference ASP.NET Core, add the following element to your project file:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

Il riferimento a ASP.NET Core in questo modo è supportato solo per i progetti destinati a .NET Core 3.x.

## <a name="include-blazor-extensibility"></a>Includi estendibilità Blazor

Blazor supporta i modelli di [hosting](xref:blazor/hosting-models)WebAssembly (WASM) e Server. A meno che non vi sia un motivo specifico per non farlo, una libreria di [componenti Razor](xref:blazor/components) dovrebbe supportare entrambi i modelli di hosting. Una libreria di componenti Razor deve utilizzare [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).

### <a name="support-both-hosting-models"></a>Supporta entrambi i modelli di hosting

Per supportare il consumo di componenti Razor da entrambi i progetti [Blazor Server](xref:blazor/hosting-models#blazor-server) e [Blazor WASM,](xref:blazor/hosting-models#blazor-webassembly) utilizzare le istruzioni seguenti per l'editor.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Utilizzare il modello di progetto **Libreria di classi Razor.Use** the Razor Class Library project template. La casella di controllo **Pagine e viste** di supporto del modello deve essere deselezionata.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Eseguire il seguente comando nel terminale integrato:

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

Utilizzare il modello di progetto **Libreria di classi Razor.Use** the Razor Class Library project template.

---

Il progetto generato dal modello esegue le operazioni seguenti:The project generated from the template does the following things:

* Destinazioni .NET Standard 2.0.Targets .NET Standard 2.0.
* Imposta la proprietà `RazorLangVersion` su `3.0`. `3.0`è il valore predefinito per .NET Core 3.x.
* Aggiunge i riferimenti al pacchetto seguenti:
  * [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [Microsoft.AspNetCore.Components.Web](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

Ad esempio:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a>Supportare un modello di hosting specificoSupport a specific hosting model

È molto meno comune supportare un singolo modello di hosting Blazor. Ad esempio, per supportare il consumo di componenti Razor solo dai progetti [Blazor Server:](xref:blazor/hosting-models#blazor-server)

* Destinazione .NET Core 3.x.
* Aggiungere `<FrameworkReference>` un elemento per il framework condiviso.

Ad esempio:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

Per ulteriori informazioni sulle librerie contenenti componenti Razor , vedere ASP.NET librerie di [classi componenti Razor di base](xref:blazor/class-libraries).

## <a name="include-mvc-extensibility"></a>Includere l'estensibilità MVC

In questa sezione vengono descritti i suggerimenti per le raccolte che includono:This section outlines recommendations for libraries that include:

* [Visualizzazioni Razor o Pagine Razor](#razor-views-or-razor-pages)
* [Helper tag](#tag-helpers)
* [Componenti di visualizzazione](#view-components)

In questa sezione non viene illustrato il multitargeting per supportare più versioni di MVC. Per istruzioni sul supporto di più versioni di ASP.NET Core, vedere [Support multiple ASP.NET Core.](#support-multiple-aspnet-core-versions)

### <a name="razor-views-or-razor-pages"></a>Visualizzazioni Razor o Pagine Razor

Un progetto che include [le visualizzazioni Razor](xref:mvc/views/overview) o [le pagine Razor](xref:razor-pages/index) deve utilizzare [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).

Se il progetto è destinato a .NET Core 3.x, richiede:If the project targets .NET Core 3.x, it requires:

* Una `AddRazorSupportForMvc` proprietà MSBuild `true`impostata su .
* Elemento `<FrameworkReference>` per il framework condiviso.

Il modello di progetto Libreria di classi **Razor** soddisfa i requisiti precedenti per i progetti destinati a .NET Core 3.x. Utilizzare le istruzioni seguenti per l'editor.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Utilizzare il modello di progetto **Libreria di classi Razor.Use** the Razor Class Library project template. La casella di controllo **Pagine e viste** di supporto del modello deve essere selezionata.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Eseguire il seguente comando nel terminale integrato:

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

Nessun supporto del modello di progetto in questo momento.

---

Ad esempio:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

Se invece il progetto è destinato a .NET Standard, è necessario un riferimento al pacchetto [Microsoft.AspNetCore.Mvc.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) Il `Microsoft.AspNetCore.Mvc` pacchetto è stato spostato nel framework condiviso in ASP.NET Core 3.0 e pertanto non è più pubblicato. Ad esempio:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a>Helper tag

Un progetto che include gli `Microsoft.NET.Sdk` helper [tag](xref:mvc/views/tag-helpers/intro) deve usare l'SDK. Se la destinazione è .NET Core `<FrameworkReference>` 3.x, aggiungere un elemento per il framework condiviso. Ad esempio:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Se la destinazione è .NET Standard (per supportare versioni precedenti ASP.NET Core 3.x), aggiungere un riferimento al pacchetto a [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor). Il `Microsoft.AspNetCore.Mvc.Razor` pacchetto è stato spostato nel framework condiviso e pertanto non è più pubblicato. Ad esempio:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a>Componenti di visualizzazione

Un progetto che include i `Microsoft.NET.Sdk` componenti di [visualizzazione](xref:mvc/views/view-components) deve utilizzare l'SDK. Se la destinazione è .NET Core `<FrameworkReference>` 3.x, aggiungere un elemento per il framework condiviso. Ad esempio:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Se la destinazione di .NET Standard (per supportare versioni precedenti a ASP.NET Core 3.x), aggiungere un riferimento al pacchetto a [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures). Il `Microsoft.AspNetCore.Mvc.ViewFeatures` pacchetto è stato spostato nel framework condiviso e pertanto non è più pubblicato. Ad esempio:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a>Supporta più versioni ASP.NET Core

Il multitargeting è necessario per creare una libreria che supporta più varianti di ASP.NET Core.Multi-targeting is required to author a library that supports multiple variants of ASP.NET Core. Si consideri uno scenario in cui una libreria di helper tag deve supportare le seguenti varianti di ASP.NET Core:Consider a scenario in which a Tag Helpers library must support the following ASP.NET Core variants:

* ASP.NET Core 2.1 destinato a .NET Framework 4.6.1
* ASP.NET Core 2.x destinato a .NET Core 2.x
* ASP.NET Core 3.x destinato a .NET Core 3.x

Il file di progetto seguente supporta `TargetFrameworks` queste varianti tramite la proprietà:

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

Con il file di progetto precedente:

* Il `Markdig` pacchetto viene aggiunto per tutti i consumer.
* Viene aggiunto un riferimento a [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) per i consumer destinati a .NET Framework 4.6.1 o versione successiva o .NET Core 2.x. La versione 2.1.0 del pacchetto funziona con ASP.NET Core 2.2 a causa della compatibilità con le versioni precedenti.
* Viene fatto riferimento al framework condiviso per i consumer destinati a .NET Core 3.x.The shared framework is referenced for consumers targeting .NET Core 3.x. Il `Microsoft.AspNetCore.Mvc.Razor` pacchetto è incluso nel framework condiviso.

In alternativa, .NET Standard 2.0 potrebbe essere destinato invece di scegliere come target sia .NET Core 2.1 che .NET Framework 4.6.1:

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

Con il file di progetto precedente, esistono le seguenti avvertenze:

* Poiché la libreria contiene solo helper tag, è più semplice scegliere come destinazione le piattaforme specifiche su cui viene eseguito ASP.NET Core: .NET Core e .NET Framework. Tag Helpers can't be used by other .NET Standard 2.0-compliant target frameworks such as Unity, UWP, and Xamarin.
* L'uso di .NET Standard 2.0 da .NET Framework presenta alcuni problemi che sono stati risolti in .NET Framework 4.7.2. È possibile migliorare l'esperienza per i consumer che usano .NET Framework 4.6.1 a 4.7.1 impostando come destinazione .NET Framework 4.6.1.You can improve the experience for consumers using .NET Framework 4.6.1 through 4.7.1 by targeting .NET Framework 4.6.1.

Se la libreria deve chiamare API specifiche della piattaforma, scegliere come destinazione implementazioni .NET specifiche anziché .NET Standard.If your library needs to call platform-specific APIs, target specific .NET implementations instead of .NET Standard. Per ulteriori informazioni, consultate [Multitargeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).

## <a name="use-an-api-that-hasnt-changed"></a>Usare un'API che non è stata modificataUse an API that't changed

Si immagini uno scenario in cui si sta aggiornando una libreria middleware da .NET Core 2.2 a 3.0. Le API middleware di ASP.NET Core in uso nella libreria non sono state modificate tra ASP.NET Core 2.2 e 3.0. Per continuare a supportare la libreria middleware in .NET Core 3.0, attenersi alla seguente procedura:

* Seguire le [linee guida della libreria standard](/dotnet/standard/library-guidance/).
* Aggiungere un riferimento al pacchetto per ogni pacchetto NuGet dell'API se l'assembly corrispondente non esiste nel framework condiviso.

## <a name="use-an-api-that-changed"></a>Usare un'API modificataUse an API that changed

Si immagini uno scenario in cui si sta aggiornando una libreria da .NET Core 2.2 a .NET Core 3.0. Un'API ASP.NET Core in uso nella libreria presenta una modifica sostanziale in ASP.NET Core 3.0.An ASP.NET Core API being used in the library has a [breaking change](/dotnet/core/compatibility/breaking-changes) in ASP.NET Core 3.0. Valutare se la libreria può essere riscritta per non usare l'API interrotta in tutte le versioni.

Se è possibile riscrivere la libreria, eseguire questa operazione e continuare a utilizzare come destinazione un framework di destinazione precedente (ad esempio, .NET Standard 2.0 o .NET Framework 4.6.1) con riferimenti al pacchetto.

Se non è possibile riscrivere la libreria, attenersi alla seguente procedura:

* Aggiungere una destinazione per .NET Core 3.0.Add a target for .NET Core 3.0.
* Aggiungere `<FrameworkReference>` un elemento per il framework condiviso.
* Usare la direttiva per [il preprocessore #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) con il simbolo del framework di destinazione appropriato per compilare il codice in modo condizionale.

Ad esempio, le letture e le scritture sincrone nei flussi di richiesta e risposta HTTP sono disabilitate per impostazione predefinita a partire da ASP.NET Core 3.0.For example, synchronous reads and writes on HTTP request and response streams are disabled by a default as of ASP.NET Core 3.0. ASP.NET Core 2.2 supporta il comportamento sincrono per impostazione predefinita. Si consideri una libreria middleware in cui le letture e le scritture sincrone devono essere abilitate in cui si verificano operazioni di I/O. La libreria deve racchiudere il codice per abilitare le funzionalità sincrone nella direttiva del preprocessore appropriata. Ad esempio:

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a>Usare un'API introdotta nella 3.0Use an API introduced in 3.0

Si supponga di voler usare un'API ASP.NET Core introdotta in ASP.NET Core 3.0. Prendere in considerazione le domande seguenti:

1. La libreria richiede funzionalmente la nuova API?
1. La libreria può implementare questa funzionalità in modo diverso?

Se la libreria richiede funzionalmente l'API e non c'è modo di implementarla di livello inferiore:If the library functionally requires the API and there's no way to implement it down-level:

* Solo .NET Core 3.x di destinazione.
* Aggiungere `<FrameworkReference>` un elemento per il framework condiviso.

Se la libreria può implementare la funzionalità in modo diverso:

* Aggiungere .NET Core 3.x come framework di destinazione.
* Aggiungere `<FrameworkReference>` un elemento per il framework condiviso.
* Usare la direttiva per [il preprocessore #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) con il simbolo del framework di destinazione appropriato per compilare il codice in modo condizionale.

Ad esempio, l'helper <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> tag seguente usa l'interfaccia introdotta in ASP.NET Core 3.0.For example, the following Tag Helper uses the interface introduced in ASP.NET Core 3.0. I consumer destinati a .NET Core 3.0 `NETCOREAPP3_0` eseguono il percorso del codice definito dal simbolo del framework di destinazione. Il tipo di parametro del <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> costruttore di Tag Helper viene modificato in per i consumer di .NET Core 2.1 e .NET Framework 4.6.1. Questa modifica è stata necessaria perché `IHostingEnvironment` ASP.NET Core `IWebHostEnvironment` 3.0 contrassegnato come obsoleto e consigliato come sostituzione.

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

Il seguente file di progetto multi-targeting supporta questo scenario di tag Helper:

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a>Usare un'API rimossa dal framework condivisoUse an API removed from the shared framework

Per usare un ASP.NET assembly Core che è stato rimosso dal framework condiviso, aggiungere il riferimento al pacchetto appropriato. Per un elenco dei pacchetti rimossi dal framework condiviso in ASP.NET Core 3.0, vedere [Remove obsoleto package references](xref:migration/22-to-30#remove-obsolete-package-references).

Ad esempio, per aggiungere il client API Web:

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:razor-pages/ui-class>
* <xref:blazor/class-libraries>
* [Supporto per le implementazioni di .NET](/dotnet/standard/net-standard#net-implementation-support)
* [Criteri di supporto .NET](https://dotnet.microsoft.com/platform/support/policy)
