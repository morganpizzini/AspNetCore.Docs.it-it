---
title: Compilazione del file Razor in ASP.NET Core
author: rick-anderson
description: Informazioni sulla compilazione dei file Razor in un'app ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/13/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 67bbeb88cd944791b522900b69bd10cff38c9f3a
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277269"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Compilazione del file Razor in ASP.NET Core

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.1"

I file Razor con estensione *cshtml* vengono compilati sia in fase di compilazione che in fase di pubblicazione utilizzando [Razor SDK](xref:razor-pages/sdk). La compilazione runtime può essere abilitata facoltativamente configurando il progetto.

## <a name="razor-compilation"></a>Compilazione Razor

La compilazione in fase di compilazione e nella fase di pubblicazione dei file Razor è abilitata per impostazione predefinita da Razor SDK. Se abilitata, la compilazione di runtime integra la compilazione in fase di compilazione, consentendo l'aggiornamento dei file Razor in caso di modifica.

## <a name="enable-runtime-compilation-at-project-creation"></a>Abilitare la compilazione di runtime alla creazione del progettoEnable runtime compilation at project creation

Le pagine Razor e i modelli di progetto MVC includono un'opzione per abilitare la compilazione di runtime quando viene creato il progetto. Questa opzione è supportata in ASP.NET Core 3.1 e versioni successive.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Nella finestra di dialogo **Crea una nuova applicazione Web ASP.NET Core:**

1. Selezionare il modello di progetto **Applicazione Web** o Applicazione **Web (Model-View-Controller).**
1. Selezionare la casella di controllo **Abilita compilazione runtime Razor.**

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

Utilizzare `-rrc` l'opzione o `--razor-runtime-compilation` modello. Ad esempio, il comando seguente crea un nuovo progetto Razor Pages con la compilazione di runtime abilitata:For example, the following command creates a new Razor Pages project with runtime compilation enabled:

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a>Abilitare la compilazione di runtime in un progetto esistenteEnable runtime compilation in an existing project

Per abilitare la compilazione di runtime per tutti gli ambienti in un progetto esistente:To enable runtime compilation for all environments in an existing project:

1. Installare il pacchetto NuGet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/).
1. Aggiornare il `Startup.ConfigureServices` metodo del progetto <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>per includere una chiamata a . Ad esempio:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a>Abilitare in modo condizionale la compilazione di runtime in un progetto esistenteConditionally enable runtime compilation in an existing project

La compilazione di runtime può essere abilitata in modo che sia disponibile solo per lo sviluppo locale. L'abilitazione condizionale in questo modo garantisce che l'output pubblicato:

* Utilizza le viste compilate.
* Non abilita i controllori di file nell'ambiente di produzione.

Per abilitare la compilazione di runtime solo nell'ambiente di sviluppo:

1. Installare il pacchetto NuGet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/).
1. Modificare la `environmentVariables` sezione del profilo di avvio in *launchSettings.json*:
    * Verifica `ASPNETCORE_ENVIRONMENT` sia `"Development"`impostato su .
    * Impostare `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` su `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.

Nell'esempio seguente la compilazione di runtime `IIS Express` è `RazorPagesApp` abilitata nell'ambiente di sviluppo per i profili di avvio e :

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

Non sono necessarie modifiche al `Startup` codice nella classe del progetto. In fase di esecuzione, ASP.NET Core cerca `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`un [attributo HostingStartup](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) a livello di assembly in . L'attributo `HostingStartup` specifica il codice di avvio dell'app da eseguire. Tale codice di avvio consente la compilazione di runtime.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Proprietà RazorCompileOnBuild e RazorCompileOnPublish.](xref:razor-pages/sdk#properties)
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Vedere l'esempio di compilazione di [runtime in GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) per un esempio che illustra il funzionamento della compilazione di runtime tra i progetti.

::: moniker-end

::: moniker range="= aspnetcore-3.0"

I file Razor con estensione *cshtml* vengono compilati sia in fase di compilazione che in fase di pubblicazione utilizzando [Razor SDK](xref:razor-pages/sdk). Facoltativamente, è possibile abilitare la compilazione in fase di runtime configurando l'applicazione.

## <a name="razor-compilation"></a>Compilazione Razor

La compilazione in fase di compilazione e nella fase di pubblicazione dei file Razor è abilitata per impostazione predefinita da Razor SDK. Se abilitata, la compilazione di runtime integra la compilazione in fase di compilazione, consentendo l'aggiornamento dei file Razor in caso di modifica.

## <a name="runtime-compilation"></a>Compilazione runtime

Per abilitare la compilazione di runtime per tutti gli ambienti e le modalità di configurazione:

1. Installare il pacchetto NuGet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/).

1. Aggiornare il `Startup.ConfigureServices` metodo del progetto <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>per includere una chiamata a . Ad esempio:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a>Abilitare in modo condizionale la compilazione di runtimeConditionally enable runtime compilation

La compilazione di runtime può essere abilitata in modo che sia disponibile solo per lo sviluppo locale. L'abilitazione condizionale in questo modo garantisce che l'output pubblicato:

* Utilizza le viste compilate.
* È di dimensioni più piccole.
* Non abilita i controllori di file nell'ambiente di produzione.

Per abilitare la compilazione di runtime in base all'ambiente e alla modalità di configurazione:

1. Fare riferimento in modo condizionale al pacchetto [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) in base al valore attivo: `Configuration`

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. Aggiornare il `Startup.ConfigureServices` metodo del progetto `AddRazorRuntimeCompilation`per includere una chiamata a . Eseguire `AddRazorRuntimeCompilation` in modo condizionale tale che viene `ASPNETCORE_ENVIRONMENT` eseguito `Development`solo in modalità Debug quando la variabile è impostata su :

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>Risorse aggiuntive

* [Proprietà RazorCompileOnBuild e RazorCompileOnPublish.](xref:razor-pages/sdk#properties)
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Vedere l'esempio di compilazione di [runtime in GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) per un esempio che illustra il funzionamento della compilazione di runtime tra i progetti.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Un file Razor viene compilato in fase di runtime, quando viene richiamata la pagina Razor o la visualizzazione MVC associata. I file Razor vengono compilati sia in fase di compilazione che in fase di pubblicazione tramite [Razor SDK](xref:razor-pages/sdk).

## <a name="razor-compilation"></a>Compilazione Razor

La compilazione di file Razor in fase di build e pubblicazione è abilitata per impostazione predefinita da Razor SDK. La modifica dei file Razor dopo che sono stati aggiornati è supportata in fase di compilazione. Per impostazione predefinita, vengono distribuiti con l'app solo i file *Views.dll* compilati e non i file *cshtml* o gli assembly di riferimento necessari per compilare i file Razor con l'app.

> [!IMPORTANT]
> Lo strumento di precompilazione è stato deprecato e verrà rimosso in ASP.NET Core 3.0. È consigliabile eseguire la migrazione a [Razor SDK](xref:razor-pages/sdk).
>
> Razor SDK è attivo solo se nel file di progetto non sono impostate proprietà specifiche di precompilazione. Se ad esempio si imposta la proprietà `MvcRazorCompileOnPublish` del file con estensione *csproj* su `true`, Razor SDK viene disabilitato.

## <a name="runtime-compilation"></a>Compilazione runtime

La compilazione in fase di build è integrata dalla compilazione in fase di runtime dei file Razor. ASP.NET Core MVC ricompilerà i file Razor quando il contenuto di un file *cshtml* cambia.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
