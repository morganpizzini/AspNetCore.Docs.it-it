---
title: Profili di pubblicazione di Visual Studio (con estensione pubxml) per la distribuzione di app ASP.NET Core
author: rick-anderson
description: Informazioni su come creare profili di pubblicazione in Visual Studio e usarli per la gestione delle distribuzioni di app ASP.NET Core in varie destinazioni.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/28/2020
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
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: 98dfd539171807cbf94d4ac8746458152c809495
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057570"
---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a><span data-ttu-id="d8519-103">Profili di pubblicazione di Visual Studio (con estensione pubxml) per la distribuzione di app ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8519-103">Visual Studio publish profiles (.pubxml) for ASP.NET Core app deployment</span></span>

<span data-ttu-id="d8519-104">Di [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d8519-104">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d8519-105">Questo documento descrive come usare Visual Studio 2019 o versioni successive per la creazione e l'uso di profili di pubblicazione.</span><span class="sxs-lookup"><span data-stu-id="d8519-105">This document focuses on using Visual Studio 2019 or later to create and use publish profiles.</span></span> <span data-ttu-id="d8519-106">I profili di pubblicazione creati con Visual Studio possono essere usati con MSBuild e Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d8519-106">The publish profiles created with Visual Studio can be used with MSBuild and Visual Studio.</span></span> <span data-ttu-id="d8519-107">Per istruzioni sulla pubblicazione in Azure, vedere <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="d8519-107">For instructions on publishing to Azure, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

<span data-ttu-id="d8519-108">Il `dotnet new mvc` comando produce un file di progetto contenente l' [ \<Project> elemento](/visualstudio/msbuild/project-element-msbuild)di livello radice seguente:</span><span class="sxs-lookup"><span data-stu-id="d8519-108">The `dotnet new mvc` command produces a project file containing the following root-level [\<Project> element](/visualstudio/msbuild/project-element-msbuild):</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

<span data-ttu-id="d8519-109">L'attributo `Sdk` dell'elemento `<Project>` precedente importa le [proprietà](/visualstudio/msbuild/msbuild-properties) e le [destinazioni](/visualstudio/msbuild/msbuild-targets) di MSBuild rispettivamente da *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* e *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets* .</span><span class="sxs-lookup"><span data-stu-id="d8519-109">The preceding `<Project>` element's `Sdk` attribute imports the MSBuild [properties](/visualstudio/msbuild/msbuild-properties) and [targets](/visualstudio/msbuild/msbuild-targets) from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* and *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets* , respectively.</span></span> <span data-ttu-id="d8519-110">Il percorso predefinito per `$(MSBuildSDKsPath)` (con Visual Studio 2019 Enterprise) è la cartella *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* .</span><span class="sxs-lookup"><span data-stu-id="d8519-110">The default location for `$(MSBuildSDKsPath)` (with Visual Studio 2019 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="d8519-111">`Microsoft.NET.Sdk.Web`([Web SDK](xref:razor-pages/web-sdk)) dipende da altri SDK, tra cui `Microsoft.NET.Sdk` ([.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) e `Microsoft.NET.Sdk.:::no-loc(Razor):::` ([ :::no-loc(Razor)::: SDK](xref:razor-pages/sdk)).</span><span class="sxs-lookup"><span data-stu-id="d8519-111">`Microsoft.NET.Sdk.Web` ([Web SDK](xref:razor-pages/web-sdk)) depends on other SDKs, including `Microsoft.NET.Sdk` ([.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) and `Microsoft.NET.Sdk.:::no-loc(Razor):::` ([:::no-loc(Razor)::: SDK](xref:razor-pages/sdk)).</span></span> <span data-ttu-id="d8519-112">Vengono importate le proprietà e le destinazioni di MSBuild associate a ogni SDK dipendente.</span><span class="sxs-lookup"><span data-stu-id="d8519-112">The MSBuild properties and targets associated with each dependent SDK are imported.</span></span> <span data-ttu-id="d8519-113">Le destinazioni di pubblicazione importano il set appropriato di destinazioni in base al metodo di pubblicazione usato.</span><span class="sxs-lookup"><span data-stu-id="d8519-113">Publish targets import the appropriate set of targets based on the publish method used.</span></span>

<span data-ttu-id="d8519-114">Quando MSBuild o Visual Studio carica un progetto, vengono eseguite le azioni di alto livello seguenti:</span><span class="sxs-lookup"><span data-stu-id="d8519-114">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="d8519-115">Compilare un progetto</span><span class="sxs-lookup"><span data-stu-id="d8519-115">Build project</span></span>
* <span data-ttu-id="d8519-116">Calcolare i file da pubblicare</span><span class="sxs-lookup"><span data-stu-id="d8519-116">Compute files to publish</span></span>
* <span data-ttu-id="d8519-117">Pubblicare i file nella destinazione</span><span class="sxs-lookup"><span data-stu-id="d8519-117">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="d8519-118">Calcolare gli elementi del progetto</span><span class="sxs-lookup"><span data-stu-id="d8519-118">Compute project items</span></span>

<span data-ttu-id="d8519-119">Quando viene caricato il progetto, vengono calcolati gli [elementi del progetto di MSBuild](/visualstudio/msbuild/common-msbuild-project-items) (file).</span><span class="sxs-lookup"><span data-stu-id="d8519-119">When the project is loaded, the [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) (files) are computed.</span></span> <span data-ttu-id="d8519-120">Il tipo di elemento determina la modalità di elaborazione del file.</span><span class="sxs-lookup"><span data-stu-id="d8519-120">The item type determines how the file is processed.</span></span> <span data-ttu-id="d8519-121">Per impostazione predefinita, i file *cs* sono inclusi nell'elenco di elementi `Compile`.</span><span class="sxs-lookup"><span data-stu-id="d8519-121">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="d8519-122">I file presenti nell'elenco di elementi `Compile` vengono compilati.</span><span class="sxs-lookup"><span data-stu-id="d8519-122">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="d8519-123">L'elenco di elementi `Content` contiene i file che sono pubblicati in aggiunta agli output di compilazione.</span><span class="sxs-lookup"><span data-stu-id="d8519-123">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="d8519-124">Per impostazione predefinita, i file corrispondenti ai criteri `wwwroot\**`, `**\*.config` e `**\*.json` vengono inclusi nell'elenco di elementi `Content`.</span><span class="sxs-lookup"><span data-stu-id="d8519-124">By default, files matching the patterns `wwwroot\**`, `**\*.config`, and `**\*.json` are included in the `Content` item list.</span></span> <span data-ttu-id="d8519-125">Il  [criterio GLOB](https://gruntjs.com/configuring-tasks#globbing-patterns)`wwwroot\**`, ad esempio, specifica tutti i file nella cartella *wwwroot* e relative sottocartelle.</span><span class="sxs-lookup"><span data-stu-id="d8519-125">For example, the `wwwroot\**` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder and its subfolders.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d8519-126">SDK [Web](xref:razor-pages/web-sdk) importa l' [ :::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="d8519-126">The [Web SDK](xref:razor-pages/web-sdk) imports the [:::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="d8519-127">Di conseguenza, anche i file corrispondenti ai criteri `**\*.cshtml` e `**\*.razor` vengono inclusi nell'elenco di elementi `Content`.</span><span class="sxs-lookup"><span data-stu-id="d8519-127">As a result, files matching the patterns `**\*.cshtml` and `**\*.razor` are also included in the `Content` item list.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="d8519-128">SDK [Web](xref:razor-pages/web-sdk) importa l' [ :::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="d8519-128">The [Web SDK](xref:razor-pages/web-sdk) imports the [:::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="d8519-129">Di conseguenza, anche i file corrispondenti al criterio `**\*.cshtml` vengono inclusi nell'elenco di elementi `Content`.</span><span class="sxs-lookup"><span data-stu-id="d8519-129">As a result, files matching the `**\*.cshtml` pattern are also included in the `Content` item list.</span></span>

::: moniker-end

<span data-ttu-id="d8519-130">Per aggiungere esplicitamente un file all'elenco di pubblicazione, aggiungere il file direttamente nel file con estensione *csproj* , come illustrato nella sezione [File di inclusione](#include-files).</span><span class="sxs-lookup"><span data-stu-id="d8519-130">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in the [Include Files](#include-files) section.</span></span>

<span data-ttu-id="d8519-131">Quando si seleziona il pulsante **Pubblica** in Visual Studio o quando si pubblica dalla riga di comando:</span><span class="sxs-lookup"><span data-stu-id="d8519-131">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="d8519-132">Vengono calcolati le proprietà/gli elementi (i file necessari per compilare).</span><span class="sxs-lookup"><span data-stu-id="d8519-132">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="d8519-133">**Solo Visual Studio** : i pacchetti NuGet vengono ripristinati.</span><span class="sxs-lookup"><span data-stu-id="d8519-133">**Visual Studio only** : NuGet packages are restored.</span></span> <span data-ttu-id="d8519-134">(Il ripristino deve essere esplicito da parte dell'utente nell'interfaccia della riga di comando.)</span><span class="sxs-lookup"><span data-stu-id="d8519-134">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="d8519-135">Il progetto viene compilato.</span><span class="sxs-lookup"><span data-stu-id="d8519-135">The project builds.</span></span>
* <span data-ttu-id="d8519-136">Vengono calcolati gli elementi di pubblicazione (i file necessari per pubblicare).</span><span class="sxs-lookup"><span data-stu-id="d8519-136">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="d8519-137">Il progetto viene pubblicato (i file calcolati vengono copiati nella destinazione di pubblicazione).</span><span class="sxs-lookup"><span data-stu-id="d8519-137">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="d8519-138">Quando un progetto ASP.NET Core fa riferimento a `Microsoft.NET.Sdk.Web` nel file di progetto, nella radice della directory dell'app Web viene posizionato un file *app_offline.htm* .</span><span class="sxs-lookup"><span data-stu-id="d8519-138">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="d8519-139">Quando il file è presente, il modulo ASP.NET Core arresta normalmente l'app e rende disponibile il file *app_offline.htm* durante la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="d8519-139">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="d8519-140">Per altre informazioni, vedere la [Guida di riferimento per la configurazione del modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="d8519-140">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="d8519-141">Pubblicazione di base dalla riga di comando</span><span class="sxs-lookup"><span data-stu-id="d8519-141">Basic command-line publishing</span></span>

<span data-ttu-id="d8519-142">La pubblicazione dalla riga di comando funziona su tutte le piattaforme supportate da .NET Core e non richiede Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d8519-142">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="d8519-143">Negli esempi seguenti il comando [dotnet publish](/dotnet/core/tools/dotnet-publish) dell'interfaccia della riga di comando di .NET Core viene eseguito dalla directory del progetto (che contiene il file con estensione *csproj* ).</span><span class="sxs-lookup"><span data-stu-id="d8519-143">In the following examples, the .NET Core CLI's [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="d8519-144">Se la cartella del progetto non è la directory di lavoro corrente, passare in modo esplicito il percorso del file di progetto.</span><span class="sxs-lookup"><span data-stu-id="d8519-144">If the project folder isn't the current working directory, explicitly pass in the project file path.</span></span> <span data-ttu-id="d8519-145">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="d8519-145">For example:</span></span>

```dotnetcli
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="d8519-146">Eseguire i comandi seguenti per creare e pubblicare un'app Web:</span><span class="sxs-lookup"><span data-stu-id="d8519-146">Run the following commands to create and publish a web app:</span></span>

```dotnetcli
dotnet new mvc
dotnet publish
```

<span data-ttu-id="d8519-147">Il comando `dotnet publish` produce una variazione dell'output seguente:</span><span class="sxs-lookup"><span data-stu-id="d8519-147">The `dotnet publish` command produces a variation of the following output:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

<span data-ttu-id="d8519-148">Il formato della cartella di pubblicazione predefinito è *bin\Debug\\{MONIKER FRAMEWORK DI DESTINAZIONE}\publish\\* .</span><span class="sxs-lookup"><span data-stu-id="d8519-148">The default publish folder format is *bin\Debug\\{TARGET FRAMEWORK MONIKER}\publish\\* .</span></span> <span data-ttu-id="d8519-149">Ad esempio, *bin\Debug\netcoreapp2.2\publish\\* .</span><span class="sxs-lookup"><span data-stu-id="d8519-149">For example, *bin\Debug\netcoreapp2.2\publish\\* .</span></span>

<span data-ttu-id="d8519-150">Il comando seguente specifica un build `Release` e la directory di pubblicazione:</span><span class="sxs-lookup"><span data-stu-id="d8519-150">The following command specifies a `Release` build and the publishing directory:</span></span>

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="d8519-151">Il comando `dotnet publish` chiama MSBuild che richiama la destinazione `Publish`.</span><span class="sxs-lookup"><span data-stu-id="d8519-151">The `dotnet publish` command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="d8519-152">Tutti i parametri passati a `dotnet publish` vengono passati a MSBuild.</span><span class="sxs-lookup"><span data-stu-id="d8519-152">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="d8519-153">I parametri `-c` e `-o` sono mappati rispettivamente alle proprietà `Configuration` e `OutputPath` di MSBuild.</span><span class="sxs-lookup"><span data-stu-id="d8519-153">The `-c` and `-o` parameters map to MSBuild's `Configuration` and `OutputPath` properties, respectively.</span></span>

<span data-ttu-id="d8519-154">È possibile passare le proprietà di MSBuild usando uno dei formati seguenti:</span><span class="sxs-lookup"><span data-stu-id="d8519-154">MSBuild properties can be passed using either of the following formats:</span></span>

* `-p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="d8519-155">Ad esempio, il comando seguente pubblica una build `Release` in una condivisione di rete.</span><span class="sxs-lookup"><span data-stu-id="d8519-155">For example, the following command publishes a `Release` build to a network share.</span></span> <span data-ttu-id="d8519-156">La condivisione di rete è specificata con le barre ( *//r8/* ) e funziona su tutte le piattaforme .NET Core supportate.</span><span class="sxs-lookup"><span data-stu-id="d8519-156">The network share is specified with forward slashes ( *//r8/* ) and works on all .NET Core supported platforms.</span></span>

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

<span data-ttu-id="d8519-157">Verificare che l'app pubblicata per la distribuzione non sia in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="d8519-157">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="d8519-158">I file nella cartella *publish* sono bloccati durante l'esecuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="d8519-158">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="d8519-159">La distribuzione non viene eseguita perché non è possibile copiare i file bloccati.</span><span class="sxs-lookup"><span data-stu-id="d8519-159">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="d8519-160">Profili di pubblicazione</span><span class="sxs-lookup"><span data-stu-id="d8519-160">Publish profiles</span></span>

<span data-ttu-id="d8519-161">Questa sezione usa Visual Studio 2019 o versioni successive per creare un profilo di pubblicazione.</span><span class="sxs-lookup"><span data-stu-id="d8519-161">This section uses Visual Studio 2019 or later to create a publishing profile.</span></span> <span data-ttu-id="d8519-162">Dopo la creazione del profilo, la pubblicazione è disponibile da Visual Studio o dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="d8519-162">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span> <span data-ttu-id="d8519-163">I profili di pubblicazione possono semplificare il processo di pubblicazione e può essere presente un numero illimitato di profili.</span><span class="sxs-lookup"><span data-stu-id="d8519-163">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span>

<span data-ttu-id="d8519-164">Creare un profilo di pubblicazione in Visual Studio scegliendo uno dei seguenti percorsi:</span><span class="sxs-lookup"><span data-stu-id="d8519-164">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="d8519-165">Fare clic con il pulsante destro del mouse in **Esplora soluzioni** , quindi scegliere **Pubblica** .</span><span class="sxs-lookup"><span data-stu-id="d8519-165">Right-click the project in **Solution Explorer** and select **Publish** .</span></span>
* <span data-ttu-id="d8519-166">Selezionare **Pubblica {NOME PROGETTO}** dal menu **Compila** .</span><span class="sxs-lookup"><span data-stu-id="d8519-166">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="d8519-167">Verrà visualizzata la scheda **Pubblica** della pagina di capacità dell'app.</span><span class="sxs-lookup"><span data-stu-id="d8519-167">The **Publish** tab of the app capabilities page is displayed.</span></span> <span data-ttu-id="d8519-168">Se il progetto non ha un profilo di pubblicazione, viene visualizzata la pagina **Selezionare una destinazione di pubblicazione** .</span><span class="sxs-lookup"><span data-stu-id="d8519-168">If the project lacks a publish profile, the **Pick a publish target** page is displayed.</span></span> <span data-ttu-id="d8519-169">Viene richiesto di selezionare una delle destinazioni di pubblicazione seguenti:</span><span class="sxs-lookup"><span data-stu-id="d8519-169">You're asked to select one of the following publish targets:</span></span>

* <span data-ttu-id="d8519-170">Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="d8519-170">Azure App Service</span></span>
* <span data-ttu-id="d8519-171">Servizio app di Azure in Linux</span><span class="sxs-lookup"><span data-stu-id="d8519-171">Azure App Service on Linux</span></span>
* <span data-ttu-id="d8519-172">Macchine virtuali di Azure</span><span class="sxs-lookup"><span data-stu-id="d8519-172">Azure Virtual Machines</span></span>
* <span data-ttu-id="d8519-173">Cartella</span><span class="sxs-lookup"><span data-stu-id="d8519-173">Folder</span></span>
* <span data-ttu-id="d8519-174">IIS, FTP, Distribuzione Web (per qualsiasi server Web)</span><span class="sxs-lookup"><span data-stu-id="d8519-174">IIS, FTP, Web Deploy (for any web server)</span></span>
* <span data-ttu-id="d8519-175">Importa profilo</span><span class="sxs-lookup"><span data-stu-id="d8519-175">Import Profile</span></span>

<span data-ttu-id="d8519-176">Per determinare la destinazione di pubblicazione più appropriata, vedere [Quali sono le opzioni di pubblicazione più adatte](/visualstudio/ide/not-in-toc/web-publish-options).</span><span class="sxs-lookup"><span data-stu-id="d8519-176">To determine the most appropriate publish target, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="d8519-177">Quando è selezionata la destinazione di pubblicazione **Cartella** , specificare il percorso di una cartella in cui archiviare gli asset pubblicati.</span><span class="sxs-lookup"><span data-stu-id="d8519-177">When the **Folder** publish target is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="d8519-178">Il percorso della cartella predefinita è *bin\\{CONFIGURAZIONE PROGETTO}\\{MONIKER FRAMEWORK DI DESTINAZIONE}\publish\\* .</span><span class="sxs-lookup"><span data-stu-id="d8519-178">The default folder path is *bin\\{PROJECT CONFIGURATION}\\{TARGET FRAMEWORK MONIKER}\publish\\* .</span></span> <span data-ttu-id="d8519-179">Ad esempio, *bin\Release\netcoreapp2.2\publish\\* .</span><span class="sxs-lookup"><span data-stu-id="d8519-179">For example, *bin\Release\netcoreapp2.2\publish\\* .</span></span> <span data-ttu-id="d8519-180">Selezionare il pulsante **Crea profilo** per terminare.</span><span class="sxs-lookup"><span data-stu-id="d8519-180">Select the **Create Profile** button to finish.</span></span>

<span data-ttu-id="d8519-181">Dopo aver creato un profilo di pubblicazione, il contenuto della scheda **Pubblica** cambia.</span><span class="sxs-lookup"><span data-stu-id="d8519-181">Once a publish profile is created, the **Publish** tab's content changes.</span></span> <span data-ttu-id="d8519-182">Il nuovo profilo creato viene visualizzato in un elenco a discesa.</span><span class="sxs-lookup"><span data-stu-id="d8519-182">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="d8519-183">Sotto l'elenco a discesa selezionare **Crea nuovo profilo** per creare un altro nuovo profilo.</span><span class="sxs-lookup"><span data-stu-id="d8519-183">Below the drop-down list, select **Create new profile** to create another new profile.</span></span>

<span data-ttu-id="d8519-184">Lo strumento di pubblicazione di Visual Studio produce un file MSBuild *Properties/PublishProfiles/{PROFILE NAME}.pubxml* che descrive il profilo di pubblicazione.</span><span class="sxs-lookup"><span data-stu-id="d8519-184">Visual Studio's publish tool produces a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file describing the publish profile.</span></span> <span data-ttu-id="d8519-185">Il file con estensione *pubxml* :</span><span class="sxs-lookup"><span data-stu-id="d8519-185">The *.pubxml* file:</span></span>

* <span data-ttu-id="d8519-186">Contiene le impostazioni di configurazione di pubblicazione e viene utilizzato dal processo di pubblicazione.</span><span class="sxs-lookup"><span data-stu-id="d8519-186">Contains publish configuration settings and is consumed by the publishing process.</span></span>
* <span data-ttu-id="d8519-187">Può essere modificato per personalizzare il processo di compilazione e pubblicazione.</span><span class="sxs-lookup"><span data-stu-id="d8519-187">Can be modified to customize the build and publish process.</span></span>

<span data-ttu-id="d8519-188">Quando si esegue la pubblicazione in una destinazione di Azure, il file *.pubxml* contiene l'identificatore della sottoscrizione Azure.</span><span class="sxs-lookup"><span data-stu-id="d8519-188">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="d8519-189">Con tale tipo di destinazione, è sconsigliato aggiungere questo file al controllo del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="d8519-189">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="d8519-190">Quando si pubblica in una destinazione non Azure, è possibile archiviare il file *.pubxml* .</span><span class="sxs-lookup"><span data-stu-id="d8519-190">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="d8519-191">Le informazioni riservate, come la password di pubblicazione, vengono crittografate a livello di singolo utente/computer.</span><span class="sxs-lookup"><span data-stu-id="d8519-191">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="d8519-192">Sono memorizzate nel file *Properties/PublishProfiles/{NOME PROFILO}.pubxml.user* .</span><span class="sxs-lookup"><span data-stu-id="d8519-192">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="d8519-193">Poiché questo file può contenere informazioni riservate, non deve essere archiviato nel controllo del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="d8519-193">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="d8519-194">Per una panoramica su come pubblicare un'app Web ASP.NET Core, vedere <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="d8519-194">For an overview of how to publish an ASP.NET Core web app, see <xref:host-and-deploy/index>.</span></span> <span data-ttu-id="d8519-195">Le attività e le destinazioni di MSBuild necessarie per pubblicare un'app Web ASP.NET Core sono open source nel [repository DotNet/WebSDK](https://github.com/dotnet/websdk).</span><span class="sxs-lookup"><span data-stu-id="d8519-195">The MSBuild tasks and targets necessary to publish an ASP.NET Core web app are open-source in the [dotnet/websdk repository](https://github.com/dotnet/websdk).</span></span>

<span data-ttu-id="d8519-196">I comandi seguenti possono usare i profili di pubblicazione cartella, MSDeploy e [Kudu](https://github.com/projectkudu/kudu/wiki) .</span><span class="sxs-lookup"><span data-stu-id="d8519-196">The following commands can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles.</span></span> <span data-ttu-id="d8519-197">Dato che MSDeploy non include il supporto multipiattaforma, le opzioni seguenti di MSDeploy sono supportate solo in Windows.</span><span class="sxs-lookup"><span data-stu-id="d8519-197">Because MSDeploy lacks cross-platform support, the following MSDeploy options are supported only on Windows.</span></span>

<span data-ttu-id="d8519-198">**Cartella (multipiattaforma):**</span><span class="sxs-lookup"><span data-stu-id="d8519-198">**Folder (works cross-platform):**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="d8519-199">**MSDeploy:**</span><span class="sxs-lookup"><span data-stu-id="d8519-199">**MSDeploy:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="d8519-200">**Pacchetto MSDeploy:**</span><span class="sxs-lookup"><span data-stu-id="d8519-200">**MSDeploy package:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="d8519-201">Negli esempi precedenti:</span><span class="sxs-lookup"><span data-stu-id="d8519-201">In the preceding examples:</span></span>

* <span data-ttu-id="d8519-202">`dotnet publish` e `dotnet build` supportano le API Kudu per la pubblicazione in Azure da qualsiasi piattaforma.</span><span class="sxs-lookup"><span data-stu-id="d8519-202">`dotnet publish` and `dotnet build` support Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="d8519-203">La pubblicazione con Visual Studio supporta le API Kudu, ma è supportata da WebSDK per la pubblicazione multipiattaforma in Azure.</span><span class="sxs-lookup"><span data-stu-id="d8519-203">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>
* <span data-ttu-id="d8519-204">Non passare `DeployOnBuild` al `dotnet publish` comando.</span><span class="sxs-lookup"><span data-stu-id="d8519-204">Don't pass `DeployOnBuild` to the `dotnet publish` command.</span></span>

<span data-ttu-id="d8519-205">Per altre informazioni, vedere [Microsoft.NET.Sdk.Publish](https://github.com/dotnet/websdk#microsoftnetsdkpublish).</span><span class="sxs-lookup"><span data-stu-id="d8519-205">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/dotnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="d8519-206">Aggiungere un profilo di pubblicazione alla cartella *Properties/PublishProfiles* del progetto con il contenuto seguente:</span><span class="sxs-lookup"><span data-stu-id="d8519-206">Add a publish profile to the project's *Properties/PublishProfiles* folder with the following content:</span></span>

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

## <a name="folder-publish-example"></a><span data-ttu-id="d8519-207">Esempio di pubblicazione cartella</span><span class="sxs-lookup"><span data-stu-id="d8519-207">Folder publish example</span></span>

<span data-ttu-id="d8519-208">Quando si esegue la pubblicazione con un profilo denominato *FolderProfile* , usare uno dei comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="d8519-208">When publishing with a profile named *FolderProfile* , use any of the following commands:</span></span>

```dotnetcli
dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`
```

```dotnetcli
dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

```bash
msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

<span data-ttu-id="d8519-209">Il comando [dotnet build](/dotnet/core/tools/dotnet-build) dell'interfaccia della riga di comando di .NET Core chiama `msbuild` per eseguire il processo di compilazione e pubblicazione.</span><span class="sxs-lookup"><span data-stu-id="d8519-209">The .NET Core CLI's [dotnet build](/dotnet/core/tools/dotnet-build) command calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="d8519-210">I comandi `dotnet build` e `msbuild` sono equivalenti quando si passa un profilo di cartella.</span><span class="sxs-lookup"><span data-stu-id="d8519-210">The `dotnet build` and `msbuild` commands are equivalent when passing in a folder profile.</span></span> <span data-ttu-id="d8519-211">Chiamando `msbuild` direttamente in Windows, viene usata la versione .NET Framework di MSBuild.</span><span class="sxs-lookup"><span data-stu-id="d8519-211">When calling `msbuild` directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="d8519-212">La chiamata di `dotnet build` su un profilo non di tipo cartella:</span><span class="sxs-lookup"><span data-stu-id="d8519-212">Calling `dotnet build` on a non-folder profile:</span></span>

* <span data-ttu-id="d8519-213">Richiama `msbuild`, che usa MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="d8519-213">Invokes `msbuild`, which uses MSDeploy.</span></span>
* <span data-ttu-id="d8519-214">Genera un errore (anche se in esecuzione in Windows).</span><span class="sxs-lookup"><span data-stu-id="d8519-214">Results in a failure (even when running on Windows).</span></span> <span data-ttu-id="d8519-215">Per la pubblicazione con un profilo non di tipo cartella, chiamare `msbuild` direttamente.</span><span class="sxs-lookup"><span data-stu-id="d8519-215">To publish with a non-folder profile, call `msbuild` directly.</span></span>

<span data-ttu-id="d8519-216">Il profilo di pubblicazione cartella seguente è stato creato con Visual Studio ed esegue la pubblicazione in una condivisione di rete:</span><span class="sxs-lookup"><span data-stu-id="d8519-216">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

<span data-ttu-id="d8519-217">Nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="d8519-217">In the preceding example:</span></span>

* <span data-ttu-id="d8519-218">La proprietà `<ExcludeApp_Data>` è presente solo per soddisfare un requisito di XML Schema.</span><span class="sxs-lookup"><span data-stu-id="d8519-218">The `<ExcludeApp_Data>` property is present merely to satisfy an XML schema requirement.</span></span> <span data-ttu-id="d8519-219">La proprietà `<ExcludeApp_Data>` non ha alcun effetto sul processo di pubblicazione, anche se è presente una cartella *App_Data* nella radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="d8519-219">The `<ExcludeApp_Data>` property has no effect on the publish process, even if there's an *App_Data* folder in the project root.</span></span> <span data-ttu-id="d8519-220">La cartella *App_Data* non riceve un trattamento speciale, come accade per i progetti ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="d8519-220">The *App_Data* folder doesn't receive special treatment as it does in ASP.NET 4.x projects.</span></span>
* <span data-ttu-id="d8519-221">La proprietà `<LastUsedBuildConfiguration>` è impostata su `Release`.</span><span class="sxs-lookup"><span data-stu-id="d8519-221">The `<LastUsedBuildConfiguration>` property is set to `Release`.</span></span> <span data-ttu-id="d8519-222">Nella pubblicazione da Visual Studio, il valore di `<LastUsedBuildConfiguration>` viene impostato usando il valore, quando viene avviato il processo di pubblicazione.</span><span class="sxs-lookup"><span data-stu-id="d8519-222">When publishing from Visual Studio, the value of `<LastUsedBuildConfiguration>` is set using the value when the publish process is started.</span></span> <span data-ttu-id="d8519-223">`<LastUsedBuildConfiguration>` è speciale e non deve essere sottoposta a override in un file di MSBuild importato.</span><span class="sxs-lookup"><span data-stu-id="d8519-223">`<LastUsedBuildConfiguration>` is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="d8519-224">Questa proprietà, tuttavia, può essere sottoposta a override dalla riga di comando usando uno degli approcci seguenti.</span><span class="sxs-lookup"><span data-stu-id="d8519-224">This property can, however, be overridden from the command line using one of the following approaches.</span></span>
  * <span data-ttu-id="d8519-225">Mediante l'interfaccia della riga di comando di .NET Core:</span><span class="sxs-lookup"><span data-stu-id="d8519-225">Using the .NET Core CLI:</span></span>

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * <span data-ttu-id="d8519-226">Mediante MSBuild:</span><span class="sxs-lookup"><span data-stu-id="d8519-226">Using MSBuild:</span></span>

    ```bash
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  <span data-ttu-id="d8519-227">Per altre informazioni, vedere [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx) (MSBuild: come impostare la proprietà di configurazione).</span><span class="sxs-lookup"><span data-stu-id="d8519-227">For more information, see [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span></span>

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="d8519-228">Pubblicare in un endpoint di MSDeploy dalla riga di comando</span><span class="sxs-lookup"><span data-stu-id="d8519-228">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="d8519-229">L'esempio seguente usa un'app Web ASP.NET Core creata da Visual Studio e denominata *AzureWebApp* .</span><span class="sxs-lookup"><span data-stu-id="d8519-229">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp* .</span></span> <span data-ttu-id="d8519-230">Un profilo di pubblicazione app di Azure viene aggiunto con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d8519-230">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="d8519-231">Per altre informazioni su come creare un profilo, vedere la sezione [Profili di pubblicazione](#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="d8519-231">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="d8519-232">Per distribuire l'app usando un profilo di pubblicazione, eseguire il comando `msbuild` dal **prompt dei comandi per gli sviluppatori** di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d8519-232">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt** .</span></span> <span data-ttu-id="d8519-233">Il prompt dei comandi è disponibile nella cartella *Visual Studio* del menu **Start** della barra delle applicazioni di Windows.</span><span class="sxs-lookup"><span data-stu-id="d8519-233">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="d8519-234">Per semplificare l'accesso, è possibile aggiungere il prompt dei comandi al menu **Strumenti** di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d8519-234">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="d8519-235">Per altre informazioni, vedere [Prompt dei comandi per gli sviluppatori per Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="d8519-235">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="d8519-236">MSBuild usa la sintassi dei comandi seguente:</span><span class="sxs-lookup"><span data-stu-id="d8519-236">MSBuild uses the following command syntax:</span></span>

```bash
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="d8519-237">`{PATH}`: Percorso del file di progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="d8519-237">`{PATH}`: Path to the app's project file.</span></span>
* <span data-ttu-id="d8519-238">`{PROFILE}`: Nome del profilo di pubblicazione.</span><span class="sxs-lookup"><span data-stu-id="d8519-238">`{PROFILE}`: Name of the publish profile.</span></span>
* <span data-ttu-id="d8519-239">`{USERNAME}`: Nome utente MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="d8519-239">`{USERNAME}`: MSDeploy username.</span></span> <span data-ttu-id="d8519-240">Il `{USERNAME}` può essere trovato nel profilo di pubblicazione.</span><span class="sxs-lookup"><span data-stu-id="d8519-240">The `{USERNAME}` can be found in the publish profile.</span></span>
* <span data-ttu-id="d8519-241">`{PASSWORD}`: Password MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="d8519-241">`{PASSWORD}`: MSDeploy password.</span></span> <span data-ttu-id="d8519-242">Ottenere `{PASSWORD}` da *{profile}. File PublishSettings* .</span><span class="sxs-lookup"><span data-stu-id="d8519-242">Obtain the `{PASSWORD}` from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="d8519-243">Scaricare il file *.PublishSettings* da:</span><span class="sxs-lookup"><span data-stu-id="d8519-243">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="d8519-244">**Esplora soluzioni** : selezionare **Visualizza**  >  **Cloud Explorer** .</span><span class="sxs-lookup"><span data-stu-id="d8519-244">**Solution Explorer** : Select **View** > **Cloud Explorer** .</span></span> <span data-ttu-id="d8519-245">Connettersi alla sottoscrizione di Azure.</span><span class="sxs-lookup"><span data-stu-id="d8519-245">Connect with your Azure subscription.</span></span> <span data-ttu-id="d8519-246">Aprire **Servizi app** .</span><span class="sxs-lookup"><span data-stu-id="d8519-246">Open **App Services** .</span></span> <span data-ttu-id="d8519-247">Fare clic con il pulsante destro del mouse sull'app.</span><span class="sxs-lookup"><span data-stu-id="d8519-247">Right-click the app.</span></span> <span data-ttu-id="d8519-248">Selezionare **Scarica profilo di pubblicazione** .</span><span class="sxs-lookup"><span data-stu-id="d8519-248">Select **Download Publish Profile** .</span></span>
  * <span data-ttu-id="d8519-249">Portale di Azure: selezionare **Ottieni profilo di pubblicazione** nel pannello **Panoramica** dell'app Web.</span><span class="sxs-lookup"><span data-stu-id="d8519-249">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="d8519-250">L'esempio seguente usa un profilo di pubblicazione denominato *AzureWebApp - Web Deploy* :</span><span class="sxs-lookup"><span data-stu-id="d8519-250">The following example uses a publish profile named *AzureWebApp - Web Deploy* :</span></span>

```bash
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="d8519-251">Un profilo di pubblicazione può essere usato anche con il comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) dell'interfaccia della riga di comando di .NET Core da una shell comandi di Windows:</span><span class="sxs-lookup"><span data-stu-id="d8519-251">A publish profile can also be used with the .NET Core CLI's [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command shell:</span></span>

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> <span data-ttu-id="d8519-252">Il comando `dotnet msbuild` è multipiattaforma e consente di compilare app ASP.NET Core in macOS e Linux.</span><span class="sxs-lookup"><span data-stu-id="d8519-252">The `dotnet msbuild` command is a cross-platform command and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="d8519-253">Tuttavia, MSBuild in macOS e Linux non è in grado di distribuire un'app in Azure o in altri endpoint MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="d8519-253">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoints.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="d8519-254">Impostare l'ambiente</span><span class="sxs-lookup"><span data-stu-id="d8519-254">Set the environment</span></span>

<span data-ttu-id="d8519-255">Includere la proprietà `<EnvironmentName>` nel profilo di pubblicazione ( *.pubxml* ) o nel file di progetto per impostare l' [ambiente](xref:fundamentals/environments) dell'app:</span><span class="sxs-lookup"><span data-stu-id="d8519-255">Include the `<EnvironmentName>` property in the publish profile ( *.pubxml* ) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="d8519-256">Se sono necessarie trasformazioni *web.config* (ad esempio, l'impostazione di variabili di ambiente in base a configurazione, profilo o ambiente), vedere <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="d8519-256">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="d8519-257">Escludere file</span><span class="sxs-lookup"><span data-stu-id="d8519-257">Exclude files</span></span>

<span data-ttu-id="d8519-258">Quando si pubblicano app Web ASP.NET Core vengono inclusi gli asset seguenti:</span><span class="sxs-lookup"><span data-stu-id="d8519-258">When publishing ASP.NET Core web apps, the following assets are included:</span></span>

* <span data-ttu-id="d8519-259">Artefatti della compilazione</span><span class="sxs-lookup"><span data-stu-id="d8519-259">Build artifacts</span></span>
* <span data-ttu-id="d8519-260">Cartelle e file corrispondenti ai criteri GLOB seguenti:</span><span class="sxs-lookup"><span data-stu-id="d8519-260">Folders and files matching the following globbing patterns:</span></span>
  * <span data-ttu-id="d8519-261">`**\*.config` (ad esempio, *web.config* )</span><span class="sxs-lookup"><span data-stu-id="d8519-261">`**\*.config` (for example, *web.config* )</span></span>
  * <span data-ttu-id="d8519-262">`**\*.json` (ad esempio, *:::no-loc(appsettings.json):::* )</span><span class="sxs-lookup"><span data-stu-id="d8519-262">`**\*.json` (for example, *:::no-loc(appsettings.json):::* )</span></span>
  * `wwwroot\**`

<span data-ttu-id="d8519-263">MSBuild supporta i [criteri GLOB](https://gruntjs.com/configuring-tasks#globbing-patterns).</span><span class="sxs-lookup"><span data-stu-id="d8519-263">MSBuild supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="d8519-264">Ad esempio, l'elemento `<Content>` seguente elimina la copia dei file di testo ( *.txt* ) nella cartella *wwwroot\content* e nelle relative sottocartelle:</span><span class="sxs-lookup"><span data-stu-id="d8519-264">For example, the following `<Content>` element suppresses the copying of text ( *.txt* ) files in the *wwwroot\content* folder and its subfolders:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="d8519-265">Il markup precedente può essere aggiunto a un profilo di pubblicazione o al file *.csproj* .</span><span class="sxs-lookup"><span data-stu-id="d8519-265">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="d8519-266">Quando viene aggiunta al file *.csproj* , la regola viene aggiunta a tutti i profili di pubblicazione del progetto.</span><span class="sxs-lookup"><span data-stu-id="d8519-266">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="d8519-267">L' `<MsDeploySkipRules>` elemento seguente esclude tutti i file dalla cartella *wwwroot\content* :</span><span class="sxs-lookup"><span data-stu-id="d8519-267">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot\content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="d8519-268">`<MsDeploySkipRules>` non elimina le destinazioni da *ignorare* dal sito di distribuzione.</span><span class="sxs-lookup"><span data-stu-id="d8519-268">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="d8519-269">Vengono eliminati dal sito di distribuzione i file e le cartelle di destinazione `<Content>`.</span><span class="sxs-lookup"><span data-stu-id="d8519-269">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="d8519-270">Si supponga, ad esempio, che un'app Web distribuita contenga i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="d8519-270">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="d8519-271">*Views/Home/About1.cshtml*</span><span class="sxs-lookup"><span data-stu-id="d8519-271">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="d8519-272">*Views/Home/About2.cshtml*</span><span class="sxs-lookup"><span data-stu-id="d8519-272">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="d8519-273">*Views/Home/About3.cshtml*</span><span class="sxs-lookup"><span data-stu-id="d8519-273">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="d8519-274">Se si aggiungono gli elementi `<MsDeploySkipRules>` seguenti, questi file non vengono eliminati nel sito di distribuzione.</span><span class="sxs-lookup"><span data-stu-id="d8519-274">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="d8519-275">Gli elementi `<MsDeploySkipRules>` precedenti impediscono che i file *ignorati* vengano distribuiti.</span><span class="sxs-lookup"><span data-stu-id="d8519-275">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="d8519-276">Tali file non verranno eliminati una volta distribuiti.</span><span class="sxs-lookup"><span data-stu-id="d8519-276">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="d8519-277">L'elemento `<Content>` seguente elimina i file di destinazione sul sito di distribuzione:</span><span class="sxs-lookup"><span data-stu-id="d8519-277">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="d8519-278">L'uso della distribuzione dalla riga di comando con l'elemento `<Content>` precedente produce una variante dell'output seguente:</span><span class="sxs-lookup"><span data-stu-id="d8519-278">Using command-line deployment with the preceding `<Content>` element yields a variation of the following output:</span></span>

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\{TARGET FRAMEWORK MONIKER}\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a><span data-ttu-id="d8519-279">File di inclusione</span><span class="sxs-lookup"><span data-stu-id="d8519-279">Include files</span></span>

<span data-ttu-id="d8519-280">Le sezioni seguenti illustrano approcci diversi per l'inclusione di file in fase di pubblicazione.</span><span class="sxs-lookup"><span data-stu-id="d8519-280">The following sections outline different approaches for file inclusion at publish time.</span></span> <span data-ttu-id="d8519-281">La sezione relativa all' [inclusione di file generale](#general-file-inclusion) usa l' `DotNetPublishFiles` elemento, fornito da un file di destinazioni di pubblicazione in [Web SDK](xref:razor-pages/web-sdk).</span><span class="sxs-lookup"><span data-stu-id="d8519-281">The [General file inclusion](#general-file-inclusion) section uses the `DotNetPublishFiles` item, which is provided by a publish targets file in the [Web SDK](xref:razor-pages/web-sdk).</span></span> <span data-ttu-id="d8519-282">La sezione relativa all' [inclusione di file selettivi](#selective-file-inclusion) usa l' `ResolvedFileToPublish` elemento, fornito da un file di destinazioni di pubblicazione nel [.NET Core SDK](/dotnet/core/project-sdk/msbuild-props).</span><span class="sxs-lookup"><span data-stu-id="d8519-282">The [Selective file inclusion](#selective-file-inclusion) section uses the `ResolvedFileToPublish` item, which is provided by a publish targets file in the [.NET Core SDK](/dotnet/core/project-sdk/msbuild-props).</span></span> <span data-ttu-id="d8519-283">Dato che Web SDK dipende da .NET Core SDK, è possibile usare entrambi gli elementi in un progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d8519-283">Because the Web SDK depends on the .NET Core SDK, either item can be used in an ASP.NET Core project.</span></span>

### <a name="general-file-inclusion"></a><span data-ttu-id="d8519-284">Inclusione di file generale</span><span class="sxs-lookup"><span data-stu-id="d8519-284">General file inclusion</span></span>

<span data-ttu-id="d8519-285">L'elemento `<ItemGroup>` dell'esempio seguente illustra la copia di una cartella che si trova all'esterno della directory di progetto in una cartella del sito pubblicato.</span><span class="sxs-lookup"><span data-stu-id="d8519-285">The following example's `<ItemGroup>` element demonstrates copying a folder located outside of the project directory to a folder of the published site.</span></span> <span data-ttu-id="d8519-286">Eventuali file aggiunti a `<ItemGroup>` del markup seguente vengono inclusi per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="d8519-286">Any files added to the following markup's `<ItemGroup>` are included by default.</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="d8519-287">Il markup precedente:</span><span class="sxs-lookup"><span data-stu-id="d8519-287">The preceding markup:</span></span>

* <span data-ttu-id="d8519-288">Può essere aggiunto al file con estensione *csproj* o al profilo di pubblicazione.</span><span class="sxs-lookup"><span data-stu-id="d8519-288">Can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="d8519-289">Se viene aggiunto al file *.csproj* , è incluso in ogni profilo di pubblicazione nel progetto.</span><span class="sxs-lookup"><span data-stu-id="d8519-289">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>
* <span data-ttu-id="d8519-290">Dichiara un elemento `_CustomFiles` per archiviare i file corrispondenti ai criteri GLOB dell'attributo `Include`.</span><span class="sxs-lookup"><span data-stu-id="d8519-290">Declares a `_CustomFiles` item to store files matching the `Include` attribute's globbing pattern.</span></span> <span data-ttu-id="d8519-291">La cartella *images* a cui fa riferimento il modello si trova all'esterno della directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="d8519-291">The *images* folder referenced in the pattern is located outside of the project directory.</span></span> <span data-ttu-id="d8519-292">Una [proprietà riservata](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), denominata `$(MSBuildProjectDirectory)`, viene risolta nel percorso assoluto del file di progetto.</span><span class="sxs-lookup"><span data-stu-id="d8519-292">A [reserved property](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), named `$(MSBuildProjectDirectory)`, resolves to the project file's absolute path.</span></span>
* <span data-ttu-id="d8519-293">Fornisce un elenco di file all'elemento `DotNetPublishFiles`.</span><span class="sxs-lookup"><span data-stu-id="d8519-293">Provides a list of files to the `DotNetPublishFiles` item.</span></span> <span data-ttu-id="d8519-294">Per impostazione predefinita, l'elemento `<DestinationRelativePath>` dell'elemento è vuoto.</span><span class="sxs-lookup"><span data-stu-id="d8519-294">By default, the item's `<DestinationRelativePath>` element is empty.</span></span> <span data-ttu-id="d8519-295">Il valore predefinito è sottoposto a override nel markup e usa [metadati degli elementi noti](/visualstudio/msbuild/msbuild-well-known-item-metadata), ad esempio `%(RecursiveDir)`.</span><span class="sxs-lookup"><span data-stu-id="d8519-295">The default value is overridden in the markup and uses [well-known item metadata](/visualstudio/msbuild/msbuild-well-known-item-metadata) such as `%(RecursiveDir)`.</span></span> <span data-ttu-id="d8519-296">Il testo interno rappresenta la cartella *wwwroot/images* del sito pubblicato.</span><span class="sxs-lookup"><span data-stu-id="d8519-296">The inner text represents the *wwwroot/images* folder of the published site.</span></span>

### <a name="selective-file-inclusion"></a><span data-ttu-id="d8519-297">Inclusione di file selettiva</span><span class="sxs-lookup"><span data-stu-id="d8519-297">Selective file inclusion</span></span>

<span data-ttu-id="d8519-298">Il markup evidenziato nell'esempio seguente dimostra:</span><span class="sxs-lookup"><span data-stu-id="d8519-298">The highlighted markup in the following example demonstrates:</span></span>

* <span data-ttu-id="d8519-299">La copia di un file che si trova all'esterno del progetto nella cartella *wwwroot* del sito pubblicato.</span><span class="sxs-lookup"><span data-stu-id="d8519-299">Copying a file located outside of the project into the published site's *wwwroot* folder.</span></span> <span data-ttu-id="d8519-300">Il nome del file *ReadMe2.md* viene mantenuto.</span><span class="sxs-lookup"><span data-stu-id="d8519-300">The file name of *ReadMe2.md* is maintained.</span></span>
* <span data-ttu-id="d8519-301">L'esclusione della cartella *wwwroot\Content* .</span><span class="sxs-lookup"><span data-stu-id="d8519-301">Excluding the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="d8519-302">L'esclusione di *Views\Home\About2.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="d8519-302">Excluding *Views\Home\About2.cshtml* .</span></span>

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

<span data-ttu-id="d8519-303">L'esempio precedente usa l'elemento `ResolvedFileToPublish`, il cui comportamento predefinito consiste nel copiare sempre i file specificati nell'attributo `Include` nel sito pubblicato.</span><span class="sxs-lookup"><span data-stu-id="d8519-303">The preceding example uses the `ResolvedFileToPublish` item, whose default behavior is to always copy the files provided in the `Include` attribute to the published site.</span></span> <span data-ttu-id="d8519-304">Eseguire l'override del comportamento predefinito, includendo un elemento figlio `<CopyToPublishDirectory>` con il testo interno `Never` o `PreserveNewest`.</span><span class="sxs-lookup"><span data-stu-id="d8519-304">Override the default behavior by including a `<CopyToPublishDirectory>` child element with inner text of either `Never` or `PreserveNewest`.</span></span> <span data-ttu-id="d8519-305">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="d8519-305">For example:</span></span>

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

<span data-ttu-id="d8519-306">Per ulteriori esempi di distribuzione, vedere il [file Leggimi di Web SDK](https://github.com/dotnet/sdk/tree/master/src/WebSdk).</span><span class="sxs-lookup"><span data-stu-id="d8519-306">For more deployment samples, see the [Web SDK README file](https://github.com/dotnet/sdk/tree/master/src/WebSdk).</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="d8519-307">Eseguire una destinazione prima o dopo la pubblicazione</span><span class="sxs-lookup"><span data-stu-id="d8519-307">Run a target before or after publishing</span></span>

<span data-ttu-id="d8519-308">Le destinazioni incorporate `BeforePublish` e `AfterPublish` consentono di eseguire una destinazione prima o dopo la destinazione di pubblicazione.</span><span class="sxs-lookup"><span data-stu-id="d8519-308">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="d8519-309">Aggiungere gli elementi seguenti al profilo di pubblicazione per registrare i messaggi della console prima e dopo la pubblicazione:</span><span class="sxs-lookup"><span data-stu-id="d8519-309">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="d8519-310">Pubblicazione in un server tramite un certificato non attendibile</span><span class="sxs-lookup"><span data-stu-id="d8519-310">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="d8519-311">Aggiungere la proprietà `<AllowUntrustedCertificate>` con un valore `True` al profilo di pubblicazione:</span><span class="sxs-lookup"><span data-stu-id="d8519-311">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="d8519-312">Servizio Kudu</span><span class="sxs-lookup"><span data-stu-id="d8519-312">The Kudu service</span></span>

<span data-ttu-id="d8519-313">Per visualizzare i file in una distribuzione di app Web di Servizio app di Azure, usare il [servizio Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span><span class="sxs-lookup"><span data-stu-id="d8519-313">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="d8519-314">Accodare il token `scm` al nome dell'app Web.</span><span class="sxs-lookup"><span data-stu-id="d8519-314">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="d8519-315">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="d8519-315">For example:</span></span>

| <span data-ttu-id="d8519-316">URL</span><span class="sxs-lookup"><span data-stu-id="d8519-316">URL</span></span>                                    | <span data-ttu-id="d8519-317">Risultato</span><span class="sxs-lookup"><span data-stu-id="d8519-317">Result</span></span>       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | <span data-ttu-id="d8519-318">App Web</span><span class="sxs-lookup"><span data-stu-id="d8519-318">Web App</span></span>      |
| `http://mysite.scm.azurewebsites.net/` | <span data-ttu-id="d8519-319">Servizio Kudu</span><span class="sxs-lookup"><span data-stu-id="d8519-319">Kudu service</span></span> |

<span data-ttu-id="d8519-320">Selezionare la voce di menu [Console di debug](https://github.com/projectkudu/kudu/wiki/Kudu-console) per visualizzare, modificare, eliminare o aggiungere file.</span><span class="sxs-lookup"><span data-stu-id="d8519-320">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d8519-321">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="d8519-321">Additional resources</span></span>

* <span data-ttu-id="d8519-322">[Distribuzione Web](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) semplifica la distribuzione di app Web e siti Web sui server IIS.</span><span class="sxs-lookup"><span data-stu-id="d8519-322">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="d8519-323">[Repository GitHub Web SDK](https://github.com/dotnet/websdk/issues): problemi di file e funzionalità di richiesta per la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="d8519-323">[Web SDK GitHub repository](https://github.com/dotnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="d8519-324">Pubblicare un'app Web ASP.NET in una macchina virtuale di Azure da Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8519-324">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
