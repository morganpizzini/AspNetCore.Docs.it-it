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
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="dfed2-103">Compilazione del file Razor in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dfed2-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="dfed2-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="dfed2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="dfed2-105">I file Razor con estensione *cshtml* vengono compilati sia in fase di compilazione che in fase di pubblicazione utilizzando [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="dfed2-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="dfed2-106">La compilazione runtime può essere abilitata facoltativamente configurando il progetto.</span><span class="sxs-lookup"><span data-stu-id="dfed2-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="dfed2-107">Compilazione Razor</span><span class="sxs-lookup"><span data-stu-id="dfed2-107">Razor compilation</span></span>

<span data-ttu-id="dfed2-108">La compilazione in fase di compilazione e nella fase di pubblicazione dei file Razor è abilitata per impostazione predefinita da Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="dfed2-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="dfed2-109">Se abilitata, la compilazione di runtime integra la compilazione in fase di compilazione, consentendo l'aggiornamento dei file Razor in caso di modifica.</span><span class="sxs-lookup"><span data-stu-id="dfed2-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="dfed2-110">Abilitare la compilazione di runtime alla creazione del progettoEnable runtime compilation at project creation</span><span class="sxs-lookup"><span data-stu-id="dfed2-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="dfed2-111">Le pagine Razor e i modelli di progetto MVC includono un'opzione per abilitare la compilazione di runtime quando viene creato il progetto.</span><span class="sxs-lookup"><span data-stu-id="dfed2-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="dfed2-112">Questa opzione è supportata in ASP.NET Core 3.1 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="dfed2-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dfed2-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dfed2-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dfed2-114">Nella finestra di dialogo **Crea una nuova applicazione Web ASP.NET Core:**</span><span class="sxs-lookup"><span data-stu-id="dfed2-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="dfed2-115">Selezionare il modello di progetto **Applicazione Web** o Applicazione **Web (Model-View-Controller).**</span><span class="sxs-lookup"><span data-stu-id="dfed2-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="dfed2-116">Selezionare la casella di controllo **Abilita compilazione runtime Razor.**</span><span class="sxs-lookup"><span data-stu-id="dfed2-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="dfed2-117">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="dfed2-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="dfed2-118">Utilizzare `-rrc` l'opzione o `--razor-runtime-compilation` modello.</span><span class="sxs-lookup"><span data-stu-id="dfed2-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="dfed2-119">Ad esempio, il comando seguente crea un nuovo progetto Razor Pages con la compilazione di runtime abilitata:For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span><span class="sxs-lookup"><span data-stu-id="dfed2-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="dfed2-120">Abilitare la compilazione di runtime in un progetto esistenteEnable runtime compilation in an existing project</span><span class="sxs-lookup"><span data-stu-id="dfed2-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="dfed2-121">Per abilitare la compilazione di runtime per tutti gli ambienti in un progetto esistente:To enable runtime compilation for all environments in an existing project:</span><span class="sxs-lookup"><span data-stu-id="dfed2-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="dfed2-122">Installare il pacchetto NuGet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/).</span><span class="sxs-lookup"><span data-stu-id="dfed2-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="dfed2-123">Aggiornare il `Startup.ConfigureServices` metodo del progetto <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>per includere una chiamata a .</span><span class="sxs-lookup"><span data-stu-id="dfed2-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="dfed2-124">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="dfed2-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="dfed2-125">Abilitare in modo condizionale la compilazione di runtime in un progetto esistenteConditionally enable runtime compilation in an existing project</span><span class="sxs-lookup"><span data-stu-id="dfed2-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="dfed2-126">La compilazione di runtime può essere abilitata in modo che sia disponibile solo per lo sviluppo locale.</span><span class="sxs-lookup"><span data-stu-id="dfed2-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="dfed2-127">L'abilitazione condizionale in questo modo garantisce che l'output pubblicato:</span><span class="sxs-lookup"><span data-stu-id="dfed2-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="dfed2-128">Utilizza le viste compilate.</span><span class="sxs-lookup"><span data-stu-id="dfed2-128">Uses compiled views.</span></span>
* <span data-ttu-id="dfed2-129">Non abilita i controllori di file nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="dfed2-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="dfed2-130">Per abilitare la compilazione di runtime solo nell'ambiente di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="dfed2-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="dfed2-131">Installare il pacchetto NuGet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/).</span><span class="sxs-lookup"><span data-stu-id="dfed2-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="dfed2-132">Modificare la `environmentVariables` sezione del profilo di avvio in *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="dfed2-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="dfed2-133">Verifica `ASPNETCORE_ENVIRONMENT` sia `"Development"`impostato su .</span><span class="sxs-lookup"><span data-stu-id="dfed2-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="dfed2-134">Impostare `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` su `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span><span class="sxs-lookup"><span data-stu-id="dfed2-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="dfed2-135">Nell'esempio seguente la compilazione di runtime `IIS Express` è `RazorPagesApp` abilitata nell'ambiente di sviluppo per i profili di avvio e :</span><span class="sxs-lookup"><span data-stu-id="dfed2-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="dfed2-136">Non sono necessarie modifiche al `Startup` codice nella classe del progetto.</span><span class="sxs-lookup"><span data-stu-id="dfed2-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="dfed2-137">In fase di esecuzione, ASP.NET Core cerca `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`un [attributo HostingStartup](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) a livello di assembly in .</span><span class="sxs-lookup"><span data-stu-id="dfed2-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="dfed2-138">L'attributo `HostingStartup` specifica il codice di avvio dell'app da eseguire.</span><span class="sxs-lookup"><span data-stu-id="dfed2-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="dfed2-139">Tale codice di avvio consente la compilazione di runtime.</span><span class="sxs-lookup"><span data-stu-id="dfed2-139">That startup code enables runtime compilation.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dfed2-140">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="dfed2-140">Additional resources</span></span>

* <span data-ttu-id="dfed2-141">[Proprietà RazorCompileOnBuild e RazorCompileOnPublish.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="dfed2-141">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="dfed2-142">Vedere l'esempio di compilazione di [runtime in GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) per un esempio che illustra il funzionamento della compilazione di runtime tra i progetti.</span><span class="sxs-lookup"><span data-stu-id="dfed2-142">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="dfed2-143">I file Razor con estensione *cshtml* vengono compilati sia in fase di compilazione che in fase di pubblicazione utilizzando [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="dfed2-143">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="dfed2-144">Facoltativamente, è possibile abilitare la compilazione in fase di runtime configurando l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="dfed2-144">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="dfed2-145">Compilazione Razor</span><span class="sxs-lookup"><span data-stu-id="dfed2-145">Razor compilation</span></span>

<span data-ttu-id="dfed2-146">La compilazione in fase di compilazione e nella fase di pubblicazione dei file Razor è abilitata per impostazione predefinita da Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="dfed2-146">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="dfed2-147">Se abilitata, la compilazione di runtime integra la compilazione in fase di compilazione, consentendo l'aggiornamento dei file Razor in caso di modifica.</span><span class="sxs-lookup"><span data-stu-id="dfed2-147">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="dfed2-148">Compilazione runtime</span><span class="sxs-lookup"><span data-stu-id="dfed2-148">Runtime compilation</span></span>

<span data-ttu-id="dfed2-149">Per abilitare la compilazione di runtime per tutti gli ambienti e le modalità di configurazione:</span><span class="sxs-lookup"><span data-stu-id="dfed2-149">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="dfed2-150">Installare il pacchetto NuGet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/).</span><span class="sxs-lookup"><span data-stu-id="dfed2-150">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="dfed2-151">Aggiornare il `Startup.ConfigureServices` metodo del progetto <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>per includere una chiamata a .</span><span class="sxs-lookup"><span data-stu-id="dfed2-151">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="dfed2-152">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="dfed2-152">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="dfed2-153">Abilitare in modo condizionale la compilazione di runtimeConditionally enable runtime compilation</span><span class="sxs-lookup"><span data-stu-id="dfed2-153">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="dfed2-154">La compilazione di runtime può essere abilitata in modo che sia disponibile solo per lo sviluppo locale.</span><span class="sxs-lookup"><span data-stu-id="dfed2-154">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="dfed2-155">L'abilitazione condizionale in questo modo garantisce che l'output pubblicato:</span><span class="sxs-lookup"><span data-stu-id="dfed2-155">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="dfed2-156">Utilizza le viste compilate.</span><span class="sxs-lookup"><span data-stu-id="dfed2-156">Uses compiled views.</span></span>
* <span data-ttu-id="dfed2-157">È di dimensioni più piccole.</span><span class="sxs-lookup"><span data-stu-id="dfed2-157">Is smaller in size.</span></span>
* <span data-ttu-id="dfed2-158">Non abilita i controllori di file nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="dfed2-158">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="dfed2-159">Per abilitare la compilazione di runtime in base all'ambiente e alla modalità di configurazione:</span><span class="sxs-lookup"><span data-stu-id="dfed2-159">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="dfed2-160">Fare riferimento in modo condizionale al pacchetto [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) in base al valore attivo: `Configuration`</span><span class="sxs-lookup"><span data-stu-id="dfed2-160">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="dfed2-161">Aggiornare il `Startup.ConfigureServices` metodo del progetto `AddRazorRuntimeCompilation`per includere una chiamata a .</span><span class="sxs-lookup"><span data-stu-id="dfed2-161">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="dfed2-162">Eseguire `AddRazorRuntimeCompilation` in modo condizionale tale che viene `ASPNETCORE_ENVIRONMENT` eseguito `Development`solo in modalità Debug quando la variabile è impostata su :</span><span class="sxs-lookup"><span data-stu-id="dfed2-162">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="dfed2-163">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="dfed2-163">Additional resources</span></span>

* <span data-ttu-id="dfed2-164">[Proprietà RazorCompileOnBuild e RazorCompileOnPublish.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="dfed2-164">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="dfed2-165">Vedere l'esempio di compilazione di [runtime in GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) per un esempio che illustra il funzionamento della compilazione di runtime tra i progetti.</span><span class="sxs-lookup"><span data-stu-id="dfed2-165">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dfed2-166">Un file Razor viene compilato in fase di runtime, quando viene richiamata la pagina Razor o la visualizzazione MVC associata.</span><span class="sxs-lookup"><span data-stu-id="dfed2-166">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="dfed2-167">I file Razor vengono compilati sia in fase di compilazione che in fase di pubblicazione tramite [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="dfed2-167">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="dfed2-168">Compilazione Razor</span><span class="sxs-lookup"><span data-stu-id="dfed2-168">Razor compilation</span></span>

<span data-ttu-id="dfed2-169">La compilazione di file Razor in fase di build e pubblicazione è abilitata per impostazione predefinita da Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="dfed2-169">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="dfed2-170">La modifica dei file Razor dopo che sono stati aggiornati è supportata in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="dfed2-170">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="dfed2-171">Per impostazione predefinita, vengono distribuiti con l'app solo i file *Views.dll* compilati e non i file *cshtml* o gli assembly di riferimento necessari per compilare i file Razor con l'app.</span><span class="sxs-lookup"><span data-stu-id="dfed2-171">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="dfed2-172">Lo strumento di precompilazione è stato deprecato e verrà rimosso in ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="dfed2-172">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="dfed2-173">È consigliabile eseguire la migrazione a [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="dfed2-173">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="dfed2-174">Razor SDK è attivo solo se nel file di progetto non sono impostate proprietà specifiche di precompilazione.</span><span class="sxs-lookup"><span data-stu-id="dfed2-174">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="dfed2-175">Se ad esempio si imposta la proprietà `MvcRazorCompileOnPublish` del file con estensione *csproj* su `true`, Razor SDK viene disabilitato.</span><span class="sxs-lookup"><span data-stu-id="dfed2-175">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="dfed2-176">Compilazione runtime</span><span class="sxs-lookup"><span data-stu-id="dfed2-176">Runtime compilation</span></span>

<span data-ttu-id="dfed2-177">La compilazione in fase di build è integrata dalla compilazione in fase di runtime dei file Razor.</span><span class="sxs-lookup"><span data-stu-id="dfed2-177">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="dfed2-178">ASP.NET Core MVC ricompilerà i file Razor quando il contenuto di un file *cshtml* cambia.</span><span class="sxs-lookup"><span data-stu-id="dfed2-178">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dfed2-179">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="dfed2-179">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
