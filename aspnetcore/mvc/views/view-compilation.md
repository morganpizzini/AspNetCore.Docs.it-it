---
title: Razor compilazione di file in ASP.NET Core
author: rick-anderson
description: Informazioni Razor sul modo in cui viene eseguita la compilazione dei file in un'app ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
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
uid: mvc/views/view-compilation
ms.openlocfilehash: 77ca96b329136ee044ab6fc5f6b5ebb5b67fe64c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059078"
---
# <a name="no-locrazor-file-compilation-in-aspnet-core"></a>Razor compilazione di file in ASP.NET Core

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.1"

Razori file con estensione *cshtml* vengono compilati in fase di compilazione e di pubblicazione tramite l' [ Razor SDK](xref:razor-pages/sdk). La compilazione di runtime può essere facoltativamente abilitata configurando il progetto.

## <a name="no-locrazor-compilation"></a>Razor compilazione

La compilazione della fase di compilazione e della pubblicazione dei Razor file è abilitata per impostazione predefinita dall' Razor SDK. Quando è abilitata, la compilazione del Runtime integra la compilazione in fase di compilazione, consentendo l' Razor aggiornamento dei file se vengono modificati.

## <a name="enable-runtime-compilation-at-project-creation"></a>Abilita compilazione runtime durante la creazione del progetto

Le Razor pagine e i modelli di progetto MVC includono un'opzione per abilitare la compilazione di runtime quando viene creato il progetto. Questa opzione è supportata in ASP.NET Core 3,1 e versioni successive.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Nella finestra di dialogo **Crea un nuovo ASP.NET Core applicazione Web** :

1. Selezionare il modello di progetto applicazione **Web** o **applicazione Web (Model-View-Controller)** .
1. Selezionare la casella di controllo **Abilita Razor compilazione Runtime** .

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

Usare l' `-rrc` `--razor-runtime-compilation` opzione del modello o. Ad esempio, il comando seguente crea un nuovo Razor progetto di pagine con la compilazione di runtime abilitata:

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a>Abilitare la compilazione di runtime in un progetto esistente

Per abilitare la compilazione in fase di esecuzione per tutti gli ambienti in un progetto esistente:

1. Installare [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) Pacchetto NuGet RuntimeCompilation.
1. Aggiornare il metodo del progetto `Startup.ConfigureServices` in modo da includere una chiamata a <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> . Ad esempio:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a>Abilitare in modo condizionale la compilazione di runtime in un progetto esistente

È possibile abilitare la compilazione di runtime in modo che sia disponibile solo per lo sviluppo locale. L'abilitazione condizionale in questo modo garantisce che l'output pubblicato:

* USA viste compilate.
* Non Abilita i controlli file nell'ambiente di produzione.

Per abilitare la compilazione di runtime solo nell'ambiente di sviluppo:

1. Installare [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) Pacchetto NuGet RuntimeCompilation.
1. Modificare la sezione del profilo `environmentVariables` di avvio nella *launchSettings.js* :
    * Verify `ASPNETCORE_ENVIRONMENT` è impostato su `"Development"` .
    * Impostare `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` su `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.

Nell'esempio seguente, la compilazione del runtime è abilitata nell'ambiente di sviluppo per i `IIS Express` `RazorPagesApp` profili di avvio e:

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

Non sono necessarie modifiche al codice nella classe del progetto `Startup` . In fase di esecuzione ASP.NET Core Cerca un [attributo HostingStartup a livello di assembly](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` . L' `HostingStartup` attributo specifica il codice di avvio dell'app da eseguire. Il codice di avvio consente la compilazione del runtime.

## <a name="enable-runtime-compilation-for-a-no-locrazor-class-library"></a>Abilitare la compilazione di runtime per una Razor libreria di classi

Si consideri uno scenario in cui un Razor progetto di pagine fa riferimento a una [ Razor libreria di classi (RCL)](xref:razor-pages/ui-class) denominata *MyClassLib* . Il RCL contiene un file *_Layout. cshtml* che tutti i progetti MVC e Razor pages del team utilizzano. Si vuole abilitare la compilazione in fase di esecuzione per il file *_Layout. cshtml* in tale RCL. Apportare le modifiche seguenti nel Razor progetto Pages:

1. Abilitare la compilazione di runtime con le istruzioni in [abilitare in modo condizionale la compilazione di runtime in un progetto esistente](#conditionally-enable-runtime-compilation-in-an-existing-project).
1. Configurare le opzioni di compilazione Runtime in `Startup.ConfigureServices` :

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    Nel codice precedente viene costruito un percorso assoluto di *MyClassLib* RCL. L' [API PhysicalFileProvider](xref:fundamentals/file-providers#physicalfileprovider) viene usata per individuare le directory e i file in quel percorso assoluto. Infine, l' `PhysicalFileProvider` istanza di viene aggiunta a una raccolta di provider di file, che consente l'accesso ai file con *estensione cshtml* di RCL.

## <a name="additional-resources"></a>Risorse aggiuntive

* Proprietà [ Razor CompileOnBuild e Razor CompileOnPublish](xref:razor-pages/sdk#properties) .
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Razori file con estensione *cshtml* vengono compilati in fase di compilazione e di pubblicazione tramite l' [ Razor SDK](xref:razor-pages/sdk). Facoltativamente, è possibile abilitare la compilazione in fase di runtime configurando l'applicazione.

## <a name="no-locrazor-compilation"></a>Razor compilazione

La compilazione della fase di compilazione e della pubblicazione dei Razor file è abilitata per impostazione predefinita dall' Razor SDK. Quando è abilitata, la compilazione del Runtime integra la compilazione in fase di compilazione, consentendo l' Razor aggiornamento dei file se vengono modificati.

## <a name="runtime-compilation"></a>Compilazione runtime

Per abilitare la compilazione in fase di esecuzione per tutti gli ambienti e le modalità di configurazione:

1. Installare [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) Pacchetto NuGet RuntimeCompilation.

1. Aggiornare il metodo del progetto `Startup.ConfigureServices` in modo da includere una chiamata a <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> . Ad esempio:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a>Abilitare in modo condizionale la compilazione del runtime

È possibile abilitare la compilazione di runtime in modo che sia disponibile solo per lo sviluppo locale. L'abilitazione condizionale in questo modo garantisce che l'output pubblicato:

* USA viste compilate.
* Dimensioni minori.
* Non Abilita i controlli file nell'ambiente di produzione.

Per abilitare la compilazione in fase di esecuzione in base all'ambiente e alla modalità di configurazione:

1. Riferimenti condizionali a [Microsoft. AspNetCore. Mvc. Razor . RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) il pacchetto in base al `Configuration` valore attivo:

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. Aggiornare il metodo del progetto `Startup.ConfigureServices` in modo da includere una chiamata a `AddRazorRuntimeCompilation` . Eseguire `AddRazorRuntimeCompilation` in modo condizionale in modo che venga eseguita solo in modalità di debug quando la `ASPNETCORE_ENVIRONMENT` variabile è impostata su `Development` :

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>Risorse aggiuntive

* Proprietà [ Razor CompileOnBuild e Razor CompileOnPublish](xref:razor-pages/sdk#properties) .
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Vedere l' [esempio di compilazione di runtime su GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) per un esempio che illustra come eseguire la compilazione di runtime tra progetti.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Un Razor file viene compilato in fase di esecuzione, quando Razor viene richiamata la pagina associata o la visualizzazione MVC. Razori file vengono compilati in fase di compilazione e di pubblicazione utilizzando l' [ Razor SDK](xref:razor-pages/sdk).

## <a name="no-locrazor-compilation"></a>Razor compilazione

La compilazione della compilazione e della pubblicazione dei Razor file è abilitata per impostazione predefinita dall' Razor SDK. La modifica dei Razor file dopo che sono stati aggiornati è supportata in fase di compilazione. Per impostazione predefinita, solo i file di *Views.dll* compilati e non *cshtml* o gli assembly di riferimento necessari per compilare Razor i file vengono distribuiti con l'app.

> [!IMPORTANT]
> Lo strumento di precompilazione è stato deprecato e verrà rimosso in ASP.NET Core 3.0. Si consiglia di eseguire la migrazione a [ Razor SDK](xref:razor-pages/sdk).
>
> L' Razor SDK è efficace solo quando non sono impostate proprietà specifiche per la precompilazione nel file di progetto. Ad esempio, l'impostazione della proprietà del file con estensione *csproj* `MvcRazorCompileOnPublish` su `true` Disabilita l' Razor SDK.

## <a name="runtime-compilation"></a>Compilazione runtime

La compilazione in fase di compilazione è completata dalla compilazione di file in fase di esecuzione Razor . ASP.NET Core MVC ricompila Razor i file quando viene modificato il contenuto di un file con *estensione cshtml* .

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
