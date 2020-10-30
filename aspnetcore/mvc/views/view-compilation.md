---
title: ':::no-loc(Razor)::: compilazione di file in ASP.NET Core'
author: rick-anderson
description: "Informazioni :::no-loc(Razor)::: sul modo in cui viene eseguita la compilazione dei file in un'app ASP.NET Core."
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/views/view-compilation
ms.openlocfilehash: 77ca96b329136ee044ab6fc5f6b5ebb5b67fe64c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059078"
---
# <a name="no-locrazor-file-compilation-in-aspnet-core"></a><span data-ttu-id="46333-103">:::no-loc(Razor)::: compilazione di file in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="46333-103">:::no-loc(Razor)::: file compilation in ASP.NET Core</span></span>

<span data-ttu-id="46333-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="46333-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="46333-105">:::no-loc(Razor):::i file con estensione *cshtml* vengono compilati in fase di compilazione e di pubblicazione tramite l' [ :::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="46333-105">:::no-loc(Razor)::: files with a *.cshtml* extension are compiled at both build and publish time using the [:::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="46333-106">La compilazione di runtime può essere facoltativamente abilitata configurando il progetto.</span><span class="sxs-lookup"><span data-stu-id="46333-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="46333-107">:::no-loc(Razor)::: compilazione</span><span class="sxs-lookup"><span data-stu-id="46333-107">:::no-loc(Razor)::: compilation</span></span>

<span data-ttu-id="46333-108">La compilazione della fase di compilazione e della pubblicazione dei :::no-loc(Razor)::: file è abilitata per impostazione predefinita dall' :::no-loc(Razor)::: SDK.</span><span class="sxs-lookup"><span data-stu-id="46333-108">Build-time and publish-time compilation of :::no-loc(Razor)::: files is enabled by default by the :::no-loc(Razor)::: SDK.</span></span> <span data-ttu-id="46333-109">Quando è abilitata, la compilazione del Runtime integra la compilazione in fase di compilazione, consentendo l' :::no-loc(Razor)::: aggiornamento dei file se vengono modificati.</span><span class="sxs-lookup"><span data-stu-id="46333-109">When enabled, runtime compilation complements build-time compilation, allowing :::no-loc(Razor)::: files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="46333-110">Abilita compilazione runtime durante la creazione del progetto</span><span class="sxs-lookup"><span data-stu-id="46333-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="46333-111">Le :::no-loc(Razor)::: pagine e i modelli di progetto MVC includono un'opzione per abilitare la compilazione di runtime quando viene creato il progetto.</span><span class="sxs-lookup"><span data-stu-id="46333-111">The :::no-loc(Razor)::: Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="46333-112">Questa opzione è supportata in ASP.NET Core 3,1 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="46333-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46333-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46333-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="46333-114">Nella finestra di dialogo **Crea un nuovo ASP.NET Core applicazione Web** :</span><span class="sxs-lookup"><span data-stu-id="46333-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="46333-115">Selezionare il modello di progetto applicazione **Web** o **applicazione Web (Model-View-Controller)** .</span><span class="sxs-lookup"><span data-stu-id="46333-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="46333-116">Selezionare la casella di controllo **Abilita :::no-loc(Razor)::: compilazione Runtime** .</span><span class="sxs-lookup"><span data-stu-id="46333-116">Select the **Enable :::no-loc(Razor)::: runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="46333-117">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="46333-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="46333-118">Usare l' `-rrc` `--razor-runtime-compilation` opzione del modello o.</span><span class="sxs-lookup"><span data-stu-id="46333-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="46333-119">Ad esempio, il comando seguente crea un nuovo :::no-loc(Razor)::: progetto di pagine con la compilazione di runtime abilitata:</span><span class="sxs-lookup"><span data-stu-id="46333-119">For example, the following command creates a new :::no-loc(Razor)::: Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="46333-120">Abilitare la compilazione di runtime in un progetto esistente</span><span class="sxs-lookup"><span data-stu-id="46333-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="46333-121">Per abilitare la compilazione in fase di esecuzione per tutti gli ambienti in un progetto esistente:</span><span class="sxs-lookup"><span data-stu-id="46333-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="46333-122">Installare [Microsoft. AspNetCore. Mvc. :::no-loc(Razor)::: . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) Pacchetto NuGet RuntimeCompilation.</span><span class="sxs-lookup"><span data-stu-id="46333-122">Install the [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="46333-123">Aggiornare il metodo del progetto `Startup.ConfigureServices` in modo da includere una chiamata a <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Razor):::RuntimeCompilationMvcBuilderExtensions.Add:::no-loc(Razor):::RuntimeCompilation*> .</span><span class="sxs-lookup"><span data-stu-id="46333-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Razor):::RuntimeCompilationMvcBuilderExtensions.Add:::no-loc(Razor):::RuntimeCompilation*>.</span></span> <span data-ttu-id="46333-124">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="46333-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Add:::no-loc(Razor):::Pages()
            .Add:::no-loc(Razor):::RuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="46333-125">Abilitare in modo condizionale la compilazione di runtime in un progetto esistente</span><span class="sxs-lookup"><span data-stu-id="46333-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="46333-126">È possibile abilitare la compilazione di runtime in modo che sia disponibile solo per lo sviluppo locale.</span><span class="sxs-lookup"><span data-stu-id="46333-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="46333-127">L'abilitazione condizionale in questo modo garantisce che l'output pubblicato:</span><span class="sxs-lookup"><span data-stu-id="46333-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="46333-128">USA viste compilate.</span><span class="sxs-lookup"><span data-stu-id="46333-128">Uses compiled views.</span></span>
* <span data-ttu-id="46333-129">Non Abilita i controlli file nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="46333-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="46333-130">Per abilitare la compilazione di runtime solo nell'ambiente di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="46333-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="46333-131">Installare [Microsoft. AspNetCore. Mvc. :::no-loc(Razor)::: . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) Pacchetto NuGet RuntimeCompilation.</span><span class="sxs-lookup"><span data-stu-id="46333-131">Install the [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="46333-132">Modificare la sezione del profilo `environmentVariables` di avvio nella *launchSettings.js* :</span><span class="sxs-lookup"><span data-stu-id="46333-132">Modify the launch profile `environmentVariables` section in *launchSettings.json* :</span></span>
    * <span data-ttu-id="46333-133">Verify `ASPNETCORE_ENVIRONMENT` è impostato su `"Development"` .</span><span class="sxs-lookup"><span data-stu-id="46333-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="46333-134">Impostare `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` su `"Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation"`.</span><span class="sxs-lookup"><span data-stu-id="46333-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation"`.</span></span>

<span data-ttu-id="46333-135">Nell'esempio seguente, la compilazione del runtime è abilitata nell'ambiente di sviluppo per i `IIS Express` `:::no-loc(Razor):::PagesApp` profili di avvio e:</span><span class="sxs-lookup"><span data-stu-id="46333-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `:::no-loc(Razor):::PagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="46333-136">Non sono necessarie modifiche al codice nella classe del progetto `Startup` .</span><span class="sxs-lookup"><span data-stu-id="46333-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="46333-137">In fase di esecuzione ASP.NET Core Cerca un [attributo HostingStartup a livello di assembly](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation` .</span><span class="sxs-lookup"><span data-stu-id="46333-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation`.</span></span> <span data-ttu-id="46333-138">L' `HostingStartup` attributo specifica il codice di avvio dell'app da eseguire.</span><span class="sxs-lookup"><span data-stu-id="46333-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="46333-139">Il codice di avvio consente la compilazione del runtime.</span><span class="sxs-lookup"><span data-stu-id="46333-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-no-locrazor-class-library"></a><span data-ttu-id="46333-140">Abilitare la compilazione di runtime per una :::no-loc(Razor)::: libreria di classi</span><span class="sxs-lookup"><span data-stu-id="46333-140">Enable runtime compilation for a :::no-loc(Razor)::: Class Library</span></span>

<span data-ttu-id="46333-141">Si consideri uno scenario in cui un :::no-loc(Razor)::: progetto di pagine fa riferimento a una [ :::no-loc(Razor)::: libreria di classi (RCL)](xref:razor-pages/ui-class) denominata *MyClassLib* .</span><span class="sxs-lookup"><span data-stu-id="46333-141">Consider a scenario in which a :::no-loc(Razor)::: Pages project references a [:::no-loc(Razor)::: Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib* .</span></span> <span data-ttu-id="46333-142">Il RCL contiene un file *_Layout. cshtml* che tutti i progetti MVC e :::no-loc(Razor)::: pages del team utilizzano.</span><span class="sxs-lookup"><span data-stu-id="46333-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and :::no-loc(Razor)::: Pages projects consume.</span></span> <span data-ttu-id="46333-143">Si vuole abilitare la compilazione in fase di esecuzione per il file *_Layout. cshtml* in tale RCL.</span><span class="sxs-lookup"><span data-stu-id="46333-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="46333-144">Apportare le modifiche seguenti nel :::no-loc(Razor)::: progetto Pages:</span><span class="sxs-lookup"><span data-stu-id="46333-144">Make the following changes in the :::no-loc(Razor)::: Pages project:</span></span>

1. <span data-ttu-id="46333-145">Abilitare la compilazione di runtime con le istruzioni in [abilitare in modo condizionale la compilazione di runtime in un progetto esistente](#conditionally-enable-runtime-compilation-in-an-existing-project).</span><span class="sxs-lookup"><span data-stu-id="46333-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="46333-146">Configurare le opzioni di compilazione Runtime in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="46333-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="46333-147">Nel codice precedente viene costruito un percorso assoluto di *MyClassLib* RCL.</span><span class="sxs-lookup"><span data-stu-id="46333-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="46333-148">L' [API PhysicalFileProvider](xref:fundamentals/file-providers#physicalfileprovider) viene usata per individuare le directory e i file in quel percorso assoluto.</span><span class="sxs-lookup"><span data-stu-id="46333-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="46333-149">Infine, l' `PhysicalFileProvider` istanza di viene aggiunta a una raccolta di provider di file, che consente l'accesso ai file con *estensione cshtml* di RCL.</span><span class="sxs-lookup"><span data-stu-id="46333-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="46333-150">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="46333-150">Additional resources</span></span>

* <span data-ttu-id="46333-151">Proprietà [ :::no-loc(Razor)::: CompileOnBuild e :::no-loc(Razor)::: CompileOnPublish](xref:razor-pages/sdk#properties) .</span><span class="sxs-lookup"><span data-stu-id="46333-151">[:::no-loc(Razor):::CompileOnBuild and :::no-loc(Razor):::CompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="46333-152">:::no-loc(Razor):::i file con estensione *cshtml* vengono compilati in fase di compilazione e di pubblicazione tramite l' [ :::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="46333-152">:::no-loc(Razor)::: files with a *.cshtml* extension are compiled at both build and publish time using the [:::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="46333-153">Facoltativamente, è possibile abilitare la compilazione in fase di runtime configurando l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="46333-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="46333-154">:::no-loc(Razor)::: compilazione</span><span class="sxs-lookup"><span data-stu-id="46333-154">:::no-loc(Razor)::: compilation</span></span>

<span data-ttu-id="46333-155">La compilazione della fase di compilazione e della pubblicazione dei :::no-loc(Razor)::: file è abilitata per impostazione predefinita dall' :::no-loc(Razor)::: SDK.</span><span class="sxs-lookup"><span data-stu-id="46333-155">Build-time and publish-time compilation of :::no-loc(Razor)::: files is enabled by default by the :::no-loc(Razor)::: SDK.</span></span> <span data-ttu-id="46333-156">Quando è abilitata, la compilazione del Runtime integra la compilazione in fase di compilazione, consentendo l' :::no-loc(Razor)::: aggiornamento dei file se vengono modificati.</span><span class="sxs-lookup"><span data-stu-id="46333-156">When enabled, runtime compilation complements build-time compilation, allowing :::no-loc(Razor)::: files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="46333-157">Compilazione runtime</span><span class="sxs-lookup"><span data-stu-id="46333-157">Runtime compilation</span></span>

<span data-ttu-id="46333-158">Per abilitare la compilazione in fase di esecuzione per tutti gli ambienti e le modalità di configurazione:</span><span class="sxs-lookup"><span data-stu-id="46333-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="46333-159">Installare [Microsoft. AspNetCore. Mvc. :::no-loc(Razor)::: . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) Pacchetto NuGet RuntimeCompilation.</span><span class="sxs-lookup"><span data-stu-id="46333-159">Install the [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="46333-160">Aggiornare il metodo del progetto `Startup.ConfigureServices` in modo da includere una chiamata a <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Razor):::RuntimeCompilationMvcBuilderExtensions.Add:::no-loc(Razor):::RuntimeCompilation*> .</span><span class="sxs-lookup"><span data-stu-id="46333-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Razor):::RuntimeCompilationMvcBuilderExtensions.Add:::no-loc(Razor):::RuntimeCompilation*>.</span></span> <span data-ttu-id="46333-161">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="46333-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Add:::no-loc(Razor):::Pages()
            .Add:::no-loc(Razor):::RuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="46333-162">Abilitare in modo condizionale la compilazione del runtime</span><span class="sxs-lookup"><span data-stu-id="46333-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="46333-163">È possibile abilitare la compilazione di runtime in modo che sia disponibile solo per lo sviluppo locale.</span><span class="sxs-lookup"><span data-stu-id="46333-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="46333-164">L'abilitazione condizionale in questo modo garantisce che l'output pubblicato:</span><span class="sxs-lookup"><span data-stu-id="46333-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="46333-165">USA viste compilate.</span><span class="sxs-lookup"><span data-stu-id="46333-165">Uses compiled views.</span></span>
* <span data-ttu-id="46333-166">Dimensioni minori.</span><span class="sxs-lookup"><span data-stu-id="46333-166">Is smaller in size.</span></span>
* <span data-ttu-id="46333-167">Non Abilita i controlli file nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="46333-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="46333-168">Per abilitare la compilazione in fase di esecuzione in base all'ambiente e alla modalità di configurazione:</span><span class="sxs-lookup"><span data-stu-id="46333-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="46333-169">Riferimenti condizionali a [Microsoft. AspNetCore. Mvc. :::no-loc(Razor)::: . RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) il pacchetto in base al `Configuration` valore attivo:</span><span class="sxs-lookup"><span data-stu-id="46333-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="46333-170">Aggiornare il metodo del progetto `Startup.ConfigureServices` in modo da includere una chiamata a `Add:::no-loc(Razor):::RuntimeCompilation` .</span><span class="sxs-lookup"><span data-stu-id="46333-170">Update the project's `Startup.ConfigureServices` method to include a call to `Add:::no-loc(Razor):::RuntimeCompilation`.</span></span> <span data-ttu-id="46333-171">Eseguire `Add:::no-loc(Razor):::RuntimeCompilation` in modo condizionale in modo che venga eseguita solo in modalità di debug quando la `ASPNETCORE_ENVIRONMENT` variabile è impostata su `Development` :</span><span class="sxs-lookup"><span data-stu-id="46333-171">Conditionally execute `Add:::no-loc(Razor):::RuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="46333-172">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="46333-172">Additional resources</span></span>

* <span data-ttu-id="46333-173">Proprietà [ :::no-loc(Razor)::: CompileOnBuild e :::no-loc(Razor)::: CompileOnPublish](xref:razor-pages/sdk#properties) .</span><span class="sxs-lookup"><span data-stu-id="46333-173">[:::no-loc(Razor):::CompileOnBuild and :::no-loc(Razor):::CompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="46333-174">Vedere l' [esempio di compilazione di runtime su GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) per un esempio che illustra come eseguire la compilazione di runtime tra progetti.</span><span class="sxs-lookup"><span data-stu-id="46333-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="46333-175">Un :::no-loc(Razor)::: file viene compilato in fase di esecuzione, quando :::no-loc(Razor)::: viene richiamata la pagina associata o la visualizzazione MVC.</span><span class="sxs-lookup"><span data-stu-id="46333-175">A :::no-loc(Razor)::: file is compiled at runtime, when the associated :::no-loc(Razor)::: Page or MVC view is invoked.</span></span> <span data-ttu-id="46333-176">:::no-loc(Razor):::i file vengono compilati in fase di compilazione e di pubblicazione utilizzando l' [ :::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="46333-176">:::no-loc(Razor)::: files are compiled at both build and publish time using the [:::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="46333-177">:::no-loc(Razor)::: compilazione</span><span class="sxs-lookup"><span data-stu-id="46333-177">:::no-loc(Razor)::: compilation</span></span>

<span data-ttu-id="46333-178">La compilazione della compilazione e della pubblicazione dei :::no-loc(Razor)::: file è abilitata per impostazione predefinita dall' :::no-loc(Razor)::: SDK.</span><span class="sxs-lookup"><span data-stu-id="46333-178">Build- and publish-time compilation of :::no-loc(Razor)::: files is enabled by default by the :::no-loc(Razor)::: SDK.</span></span> <span data-ttu-id="46333-179">La modifica dei :::no-loc(Razor)::: file dopo che sono stati aggiornati è supportata in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="46333-179">Editing :::no-loc(Razor)::: files after they're updated is supported at build time.</span></span> <span data-ttu-id="46333-180">Per impostazione predefinita, solo i file di *Views.dll* compilati e non *cshtml* o gli assembly di riferimento necessari per compilare :::no-loc(Razor)::: i file vengono distribuiti con l'app.</span><span class="sxs-lookup"><span data-stu-id="46333-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile :::no-loc(Razor)::: files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="46333-181">Lo strumento di precompilazione è stato deprecato e verrà rimosso in ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="46333-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="46333-182">Si consiglia di eseguire la migrazione a [ :::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="46333-182">We recommend migrating to [:::no-loc(Razor)::: Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="46333-183">L' :::no-loc(Razor)::: SDK è efficace solo quando non sono impostate proprietà specifiche per la precompilazione nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="46333-183">The :::no-loc(Razor)::: SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="46333-184">Ad esempio, l'impostazione della proprietà del file con estensione *csproj* `Mvc:::no-loc(Razor):::CompileOnPublish` su `true` Disabilita l' :::no-loc(Razor)::: SDK.</span><span class="sxs-lookup"><span data-stu-id="46333-184">For instance, setting the *.csproj* file's `Mvc:::no-loc(Razor):::CompileOnPublish` property to `true` disables the :::no-loc(Razor)::: SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="46333-185">Compilazione runtime</span><span class="sxs-lookup"><span data-stu-id="46333-185">Runtime compilation</span></span>

<span data-ttu-id="46333-186">La compilazione in fase di compilazione è completata dalla compilazione di file in fase di esecuzione :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="46333-186">Build-time compilation is supplemented by runtime compilation of :::no-loc(Razor)::: files.</span></span> <span data-ttu-id="46333-187">ASP.NET Core MVC ricompila :::no-loc(Razor)::: i file quando viene modificato il contenuto di un file con *estensione cshtml* .</span><span class="sxs-lookup"><span data-stu-id="46333-187">ASP.NET Core MVC will recompile :::no-loc(Razor)::: files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="46333-188">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="46333-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
