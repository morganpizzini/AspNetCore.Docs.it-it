---
title: Compilazione del file Razor in ASP.NET Core
author: rick-anderson
description: Informazioni sulla compilazione dei file Razor in un'app ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 4/8/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 0afd39fdb5a6f570e0e78ad54f6c436460bad3a6
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223959"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="37ff2-103">Compilazione del file Razor in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="37ff2-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="37ff2-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="37ff2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="37ff2-105">I file Razor con estensione *cshtml* vengono compilati sia in fase di compilazione che in fase di pubblicazione utilizzando [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="37ff2-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="37ff2-106">Facoltativamente, è possibile abilitare la compilazione in fase di runtime configurando l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="37ff2-106">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="37ff2-107">Compilazione Razor</span><span class="sxs-lookup"><span data-stu-id="37ff2-107">Razor compilation</span></span>

<span data-ttu-id="37ff2-108">La compilazione di file Razor in fase di build e pubblicazione è abilitata per impostazione predefinita da Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="37ff2-108">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="37ff2-109">Quando abilitata, la compilazione in fase di runtime funge da complemento alla compilazione in fase di build, consentendo di aggiornare i file Razor in caso di modifica.</span><span class="sxs-lookup"><span data-stu-id="37ff2-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they are edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="37ff2-110">Compilazione runtime</span><span class="sxs-lookup"><span data-stu-id="37ff2-110">Runtime compilation</span></span>

<span data-ttu-id="37ff2-111">Per abilitare la compilazione di runtime per tutti gli ambienti e le modalità di configurazione:</span><span class="sxs-lookup"><span data-stu-id="37ff2-111">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="37ff2-112">Installare il pacchetto NuGet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/).</span><span class="sxs-lookup"><span data-stu-id="37ff2-112">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="37ff2-113">Aggiornare il `Startup.ConfigureServices` metodo del progetto <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>per includere una chiamata a .</span><span class="sxs-lookup"><span data-stu-id="37ff2-113">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="37ff2-114">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="37ff2-114">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="37ff2-115">Abilitare in modo condizionale la compilazione di runtimeConditionally enable runtime compilation</span><span class="sxs-lookup"><span data-stu-id="37ff2-115">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="37ff2-116">La compilazione di runtime può essere abilitata in modo che sia disponibile solo per lo sviluppo locale.</span><span class="sxs-lookup"><span data-stu-id="37ff2-116">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="37ff2-117">L'abilitazione condizionale in questo modo garantisce che l'output pubblicato:</span><span class="sxs-lookup"><span data-stu-id="37ff2-117">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="37ff2-118">Utilizza le viste compilate.</span><span class="sxs-lookup"><span data-stu-id="37ff2-118">Uses compiled views.</span></span>
* <span data-ttu-id="37ff2-119">È di dimensioni più piccole.</span><span class="sxs-lookup"><span data-stu-id="37ff2-119">Is smaller in size.</span></span>
* <span data-ttu-id="37ff2-120">Non abilita i controllori di file nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="37ff2-120">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="37ff2-121">Per abilitare la compilazione di runtime in base all'ambiente e alla modalità di configurazione:</span><span class="sxs-lookup"><span data-stu-id="37ff2-121">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="37ff2-122">Fare riferimento in modo condizionale al pacchetto [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) in base al valore attivo: `Configuration`</span><span class="sxs-lookup"><span data-stu-id="37ff2-122">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="37ff2-123">Aggiornare il `Startup.ConfigureServices` metodo del progetto `AddRazorRuntimeCompilation`per includere una chiamata a .</span><span class="sxs-lookup"><span data-stu-id="37ff2-123">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="37ff2-124">Eseguire `AddRazorRuntimeCompilation` in modo condizionale tale che viene `ASPNETCORE_ENVIRONMENT` eseguito `Development`solo in modalità Debug quando la variabile è impostata su :</span><span class="sxs-lookup"><span data-stu-id="37ff2-124">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

  [!code-csharp[](~/mvc/views/view-compilation/sample/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="37ff2-125">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="37ff2-125">Additional resources</span></span>

* <span data-ttu-id="37ff2-126">[Proprietà RazorCompileOnBuild e RazorCompileOnPublish.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="37ff2-126">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="37ff2-127">Vedere [l'esempio runtimecompilation in GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) per un esempio che mostra il funzionamento della compilazione di runtime tra i progetti.</span><span class="sxs-lookup"><span data-stu-id="37ff2-127">See the [runtimecompilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="37ff2-128">Un file Razor viene compilato in fase di runtime, quando viene richiamata la pagina Razor o la visualizzazione MVC associata.</span><span class="sxs-lookup"><span data-stu-id="37ff2-128">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="37ff2-129">I file Razor vengono compilati sia in fase di compilazione che in fase di pubblicazione tramite [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="37ff2-129">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="37ff2-130">Compilazione Razor</span><span class="sxs-lookup"><span data-stu-id="37ff2-130">Razor compilation</span></span>

<span data-ttu-id="37ff2-131">La compilazione di file Razor in fase di build e pubblicazione è abilitata per impostazione predefinita da Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="37ff2-131">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="37ff2-132">La modifica dei file Razor dopo che sono stati aggiornati è supportata in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="37ff2-132">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="37ff2-133">Per impostazione predefinita, vengono distribuiti con l'app solo i file *Views.dll* compilati e non i file *cshtml* o gli assembly di riferimento necessari per compilare i file Razor con l'app.</span><span class="sxs-lookup"><span data-stu-id="37ff2-133">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="37ff2-134">Lo strumento di precompilazione è stato deprecato e verrà rimosso in ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="37ff2-134">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="37ff2-135">È consigliabile eseguire la migrazione a [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="37ff2-135">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="37ff2-136">Razor SDK è attivo solo se nel file di progetto non sono impostate proprietà specifiche di precompilazione.</span><span class="sxs-lookup"><span data-stu-id="37ff2-136">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="37ff2-137">Se ad esempio si imposta la proprietà `MvcRazorCompileOnPublish` del file con estensione *csproj* su `true`, Razor SDK viene disabilitato.</span><span class="sxs-lookup"><span data-stu-id="37ff2-137">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="37ff2-138">Compilazione runtime</span><span class="sxs-lookup"><span data-stu-id="37ff2-138">Runtime compilation</span></span>

<span data-ttu-id="37ff2-139">La compilazione in fase di build è integrata dalla compilazione in fase di runtime dei file Razor.</span><span class="sxs-lookup"><span data-stu-id="37ff2-139">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="37ff2-140">ASP.NET Core MVC ricompilerà i file Razor quando il contenuto di un file *cshtml* cambia.</span><span class="sxs-lookup"><span data-stu-id="37ff2-140">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="37ff2-141">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="37ff2-141">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
