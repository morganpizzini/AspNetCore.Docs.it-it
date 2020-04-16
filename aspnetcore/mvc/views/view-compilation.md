---
title: Compilazione del file Razor in ASP.NET Core
author: rick-anderson
description: Informazioni sulla compilazione dei file Razor in un'app ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 3d871ab960de28a565280d9e4cb2c597832e2455
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440935"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="c457a-103">Compilazione del file Razor in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c457a-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="c457a-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c457a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="c457a-105">I file Razor con estensione *cshtml* vengono compilati sia in fase di compilazione che in fase di pubblicazione utilizzando [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="c457a-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="c457a-106">La compilazione runtime può essere abilitata facoltativamente configurando il progetto.</span><span class="sxs-lookup"><span data-stu-id="c457a-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="c457a-107">Compilazione Razor</span><span class="sxs-lookup"><span data-stu-id="c457a-107">Razor compilation</span></span>

<span data-ttu-id="c457a-108">La compilazione in fase di compilazione e nella fase di pubblicazione dei file Razor è abilitata per impostazione predefinita da Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="c457a-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="c457a-109">Se abilitata, la compilazione di runtime integra la compilazione in fase di compilazione, consentendo l'aggiornamento dei file Razor in caso di modifica.</span><span class="sxs-lookup"><span data-stu-id="c457a-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="c457a-110">Abilitare la compilazione di runtime alla creazione del progettoEnable runtime compilation at project creation</span><span class="sxs-lookup"><span data-stu-id="c457a-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="c457a-111">Le pagine Razor e i modelli di progetto MVC includono un'opzione per abilitare la compilazione di runtime quando viene creato il progetto.</span><span class="sxs-lookup"><span data-stu-id="c457a-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="c457a-112">Questa opzione è supportata in ASP.NET Core 3.1 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="c457a-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c457a-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c457a-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c457a-114">Nella finestra di dialogo **Crea una nuova applicazione Web ASP.NET Core:**</span><span class="sxs-lookup"><span data-stu-id="c457a-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="c457a-115">Selezionare il modello di progetto **Applicazione Web** o Applicazione **Web (Model-View-Controller).**</span><span class="sxs-lookup"><span data-stu-id="c457a-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="c457a-116">Selezionare la casella di controllo **Abilita compilazione runtime Razor.**</span><span class="sxs-lookup"><span data-stu-id="c457a-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c457a-117">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="c457a-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c457a-118">Utilizzare `-rrc` l'opzione o `--razor-runtime-compilation` modello.</span><span class="sxs-lookup"><span data-stu-id="c457a-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="c457a-119">Ad esempio, il comando seguente crea un nuovo progetto Razor Pages con la compilazione di runtime abilitata:For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span><span class="sxs-lookup"><span data-stu-id="c457a-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="c457a-120">Abilitare la compilazione di runtime in un progetto esistenteEnable runtime compilation in an existing project</span><span class="sxs-lookup"><span data-stu-id="c457a-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="c457a-121">Per abilitare la compilazione di runtime per tutti gli ambienti in un progetto esistente:To enable runtime compilation for all environments in an existing project:</span><span class="sxs-lookup"><span data-stu-id="c457a-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="c457a-122">Installare il pacchetto NuGet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/).</span><span class="sxs-lookup"><span data-stu-id="c457a-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="c457a-123">Aggiornare il `Startup.ConfigureServices` metodo del progetto <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>per includere una chiamata a .</span><span class="sxs-lookup"><span data-stu-id="c457a-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="c457a-124">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c457a-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="c457a-125">Abilitare in modo condizionale la compilazione di runtime in un progetto esistenteConditionally enable runtime compilation in an existing project</span><span class="sxs-lookup"><span data-stu-id="c457a-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="c457a-126">La compilazione di runtime può essere abilitata in modo che sia disponibile solo per lo sviluppo locale.</span><span class="sxs-lookup"><span data-stu-id="c457a-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="c457a-127">L'abilitazione condizionale in questo modo garantisce che l'output pubblicato:</span><span class="sxs-lookup"><span data-stu-id="c457a-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="c457a-128">Utilizza le viste compilate.</span><span class="sxs-lookup"><span data-stu-id="c457a-128">Uses compiled views.</span></span>
* <span data-ttu-id="c457a-129">Non abilita i controllori di file nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="c457a-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="c457a-130">Per abilitare la compilazione di runtime solo nell'ambiente di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="c457a-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="c457a-131">Installare il pacchetto NuGet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/).</span><span class="sxs-lookup"><span data-stu-id="c457a-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="c457a-132">Modificare la `environmentVariables` sezione del profilo di avvio in *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="c457a-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="c457a-133">Verifica `ASPNETCORE_ENVIRONMENT` sia `"Development"`impostato su .</span><span class="sxs-lookup"><span data-stu-id="c457a-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="c457a-134">Impostare `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` su `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span><span class="sxs-lookup"><span data-stu-id="c457a-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="c457a-135">Nell'esempio seguente la compilazione di runtime `IIS Express` è `RazorPagesApp` abilitata nell'ambiente di sviluppo per i profili di avvio e :</span><span class="sxs-lookup"><span data-stu-id="c457a-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="c457a-136">Non sono necessarie modifiche al `Startup` codice nella classe del progetto.</span><span class="sxs-lookup"><span data-stu-id="c457a-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="c457a-137">In fase di esecuzione, ASP.NET Core cerca `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`un [attributo HostingStartup](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) a livello di assembly in .</span><span class="sxs-lookup"><span data-stu-id="c457a-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="c457a-138">L'attributo `HostingStartup` specifica il codice di avvio dell'app da eseguire.</span><span class="sxs-lookup"><span data-stu-id="c457a-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="c457a-139">Tale codice di avvio consente la compilazione di runtime.</span><span class="sxs-lookup"><span data-stu-id="c457a-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-razor-class-library"></a><span data-ttu-id="c457a-140">Abilitare la compilazione di runtime per una libreria di classi RazorEnable runtime compilation for a Razor Class Library</span><span class="sxs-lookup"><span data-stu-id="c457a-140">Enable runtime compilation for a Razor Class Library</span></span>

<span data-ttu-id="c457a-141">Si consideri uno scenario in cui un progetto Razor Pages fa riferimento a una libreria di classi [Razor (RCL)](xref:razor-pages/ui-class) denominata *MyClassLib*.</span><span class="sxs-lookup"><span data-stu-id="c457a-141">Consider a scenario in which a Razor Pages project references a [Razor Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib*.</span></span> <span data-ttu-id="c457a-142">Il file RCL contiene un file *_Layout.cshtml* utilizzato da tutti i progetti MVC e Razor Pages del team.</span><span class="sxs-lookup"><span data-stu-id="c457a-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and Razor Pages projects consume.</span></span> <span data-ttu-id="c457a-143">Si desidera abilitare la compilazione di runtime per il file *_Layout.cshtml* in tale RCL.</span><span class="sxs-lookup"><span data-stu-id="c457a-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="c457a-144">Apportare le seguenti modifiche nel progetto Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="c457a-144">Make the following changes in the Razor Pages project:</span></span>

1. <span data-ttu-id="c457a-145">Abilitare la compilazione di runtime con le istruzioni in [Abilitare in modo condizionale la compilazione](#conditionally-enable-runtime-compilation-in-an-existing-project)di runtime in un progetto esistente.</span><span class="sxs-lookup"><span data-stu-id="c457a-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="c457a-146">Configurare le opzioni `Startup.ConfigureServices`di compilazione runtime in :</span><span class="sxs-lookup"><span data-stu-id="c457a-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="c457a-147">Nel codice precedente viene costruito un percorso assoluto all'RCL *MyClassLib.*</span><span class="sxs-lookup"><span data-stu-id="c457a-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="c457a-148">[L'API PhysicalFileProvider](xref:fundamentals/file-providers#physicalfileprovider) viene utilizzata per individuare le directory e i file in tale percorso assoluto.</span><span class="sxs-lookup"><span data-stu-id="c457a-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="c457a-149">Infine, `PhysicalFileProvider` l'istanza viene aggiunta a una raccolta di provider di file, che consente l'accesso ai file *cshtml* della RCL.</span><span class="sxs-lookup"><span data-stu-id="c457a-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c457a-150">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="c457a-150">Additional resources</span></span>

* <span data-ttu-id="c457a-151">[Proprietà RazorCompileOnBuild e RazorCompileOnPublish.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="c457a-151">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="c457a-152">I file Razor con estensione *cshtml* vengono compilati sia in fase di compilazione che in fase di pubblicazione utilizzando [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="c457a-152">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="c457a-153">Facoltativamente, è possibile abilitare la compilazione in fase di runtime configurando l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="c457a-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="c457a-154">Compilazione Razor</span><span class="sxs-lookup"><span data-stu-id="c457a-154">Razor compilation</span></span>

<span data-ttu-id="c457a-155">La compilazione in fase di compilazione e nella fase di pubblicazione dei file Razor è abilitata per impostazione predefinita da Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="c457a-155">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="c457a-156">Se abilitata, la compilazione di runtime integra la compilazione in fase di compilazione, consentendo l'aggiornamento dei file Razor in caso di modifica.</span><span class="sxs-lookup"><span data-stu-id="c457a-156">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="c457a-157">Compilazione runtime</span><span class="sxs-lookup"><span data-stu-id="c457a-157">Runtime compilation</span></span>

<span data-ttu-id="c457a-158">Per abilitare la compilazione di runtime per tutti gli ambienti e le modalità di configurazione:</span><span class="sxs-lookup"><span data-stu-id="c457a-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="c457a-159">Installare il pacchetto NuGet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/).</span><span class="sxs-lookup"><span data-stu-id="c457a-159">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="c457a-160">Aggiornare il `Startup.ConfigureServices` metodo del progetto <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>per includere una chiamata a .</span><span class="sxs-lookup"><span data-stu-id="c457a-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="c457a-161">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c457a-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="c457a-162">Abilitare in modo condizionale la compilazione di runtimeConditionally enable runtime compilation</span><span class="sxs-lookup"><span data-stu-id="c457a-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="c457a-163">La compilazione di runtime può essere abilitata in modo che sia disponibile solo per lo sviluppo locale.</span><span class="sxs-lookup"><span data-stu-id="c457a-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="c457a-164">L'abilitazione condizionale in questo modo garantisce che l'output pubblicato:</span><span class="sxs-lookup"><span data-stu-id="c457a-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="c457a-165">Utilizza le viste compilate.</span><span class="sxs-lookup"><span data-stu-id="c457a-165">Uses compiled views.</span></span>
* <span data-ttu-id="c457a-166">È di dimensioni più piccole.</span><span class="sxs-lookup"><span data-stu-id="c457a-166">Is smaller in size.</span></span>
* <span data-ttu-id="c457a-167">Non abilita i controllori di file nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="c457a-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="c457a-168">Per abilitare la compilazione di runtime in base all'ambiente e alla modalità di configurazione:</span><span class="sxs-lookup"><span data-stu-id="c457a-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="c457a-169">Fare riferimento in modo condizionale al pacchetto [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) in base al valore attivo: `Configuration`</span><span class="sxs-lookup"><span data-stu-id="c457a-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="c457a-170">Aggiornare il `Startup.ConfigureServices` metodo del progetto `AddRazorRuntimeCompilation`per includere una chiamata a .</span><span class="sxs-lookup"><span data-stu-id="c457a-170">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="c457a-171">Eseguire `AddRazorRuntimeCompilation` in modo condizionale tale che viene `ASPNETCORE_ENVIRONMENT` eseguito `Development`solo in modalità Debug quando la variabile è impostata su :</span><span class="sxs-lookup"><span data-stu-id="c457a-171">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="c457a-172">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="c457a-172">Additional resources</span></span>

* <span data-ttu-id="c457a-173">[Proprietà RazorCompileOnBuild e RazorCompileOnPublish.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="c457a-173">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="c457a-174">Vedere l'esempio di compilazione di [runtime in GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) per un esempio che illustra il funzionamento della compilazione di runtime tra i progetti.</span><span class="sxs-lookup"><span data-stu-id="c457a-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c457a-175">Un file Razor viene compilato in fase di runtime, quando viene richiamata la pagina Razor o la visualizzazione MVC associata.</span><span class="sxs-lookup"><span data-stu-id="c457a-175">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="c457a-176">I file Razor vengono compilati sia in fase di compilazione che in fase di pubblicazione tramite [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="c457a-176">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="c457a-177">Compilazione Razor</span><span class="sxs-lookup"><span data-stu-id="c457a-177">Razor compilation</span></span>

<span data-ttu-id="c457a-178">La compilazione di file Razor in fase di build e pubblicazione è abilitata per impostazione predefinita da Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="c457a-178">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="c457a-179">La modifica dei file Razor dopo che sono stati aggiornati è supportata in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="c457a-179">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="c457a-180">Per impostazione predefinita, vengono distribuiti con l'app solo i file *Views.dll* compilati e non i file *cshtml* o gli assembly di riferimento necessari per compilare i file Razor con l'app.</span><span class="sxs-lookup"><span data-stu-id="c457a-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c457a-181">Lo strumento di precompilazione è stato deprecato e verrà rimosso in ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="c457a-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="c457a-182">È consigliabile eseguire la migrazione a [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="c457a-182">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="c457a-183">Razor SDK è attivo solo se nel file di progetto non sono impostate proprietà specifiche di precompilazione.</span><span class="sxs-lookup"><span data-stu-id="c457a-183">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="c457a-184">Se ad esempio si imposta la proprietà `MvcRazorCompileOnPublish` del file con estensione *csproj* su `true`, Razor SDK viene disabilitato.</span><span class="sxs-lookup"><span data-stu-id="c457a-184">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="c457a-185">Compilazione runtime</span><span class="sxs-lookup"><span data-stu-id="c457a-185">Runtime compilation</span></span>

<span data-ttu-id="c457a-186">La compilazione in fase di build è integrata dalla compilazione in fase di runtime dei file Razor.</span><span class="sxs-lookup"><span data-stu-id="c457a-186">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="c457a-187">ASP.NET Core MVC ricompilerà i file Razor quando il contenuto di un file *cshtml* cambia.</span><span class="sxs-lookup"><span data-stu-id="c457a-187">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c457a-188">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="c457a-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
