---
title: Compilazione del file Razor in ASP.NET Core
author: rick-anderson
description: Informazioni sulla compilazione dei file Razor in un'app ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 4/8/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 7f329ffb4c63e8699663f49720145984bb8802fd
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994600"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Compilazione del file Razor in ASP.NET Core

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

I file Razor con estensione *cshtml* vengono compilati sia in fase di compilazione che in fase di pubblicazione utilizzando [Razor SDK](xref:razor-pages/sdk). Facoltativamente, è possibile abilitare la compilazione in fase di runtime configurando l'applicazione.

## <a name="razor-compilation"></a>Compilazione Razor

La compilazione di file Razor in fase di build e pubblicazione è abilitata per impostazione predefinita da Razor SDK. Quando abilitata, la compilazione in fase di runtime funge da complemento alla compilazione in fase di build, consentendo di aggiornare i file Razor in caso di modifica.

## <a name="runtime-compilation"></a>Compilazione runtime

Per abilitare la compilazione di runtime per tutti gli ambienti e le modalità di configurazione:

1. Installare il pacchetto NuGet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/).

1. Aggiornare il `Startup.ConfigureServices` metodo del progetto `AddRazorRuntimeCompilation`per includere una chiamata a . Ad esempio:

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

    ```csharp
    public IWebHostEnvironment Env { get; set; }

    public void ConfigureServices(IServiceCollection services)
    {
        IMvcBuilder builder = services.AddRazorPages();

    #if DEBUG
        if (Env.IsDevelopment())
        {
            builder.AddRazorRuntimeCompilation();
        }
    #endif

        // code omitted for brevity
    }
    ```

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

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