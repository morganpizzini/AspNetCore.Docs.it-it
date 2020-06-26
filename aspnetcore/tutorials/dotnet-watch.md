---
title: Sviluppare app ASP.NET Core usando un watcher per file
author: rick-anderson
description: Questa esercitazione illustra come installare e usare lo strumento watcher per file (dotnet watch) dell'interfaccia della riga di comando di .NET Core in un'app ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/dotnet-watch
ms.openlocfilehash: 0c81dc2fd83cb9327dad46dab840d4352de89baf
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85409033"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="f1c81-103">Sviluppare app ASP.NET Core usando un watcher per file</span><span class="sxs-lookup"><span data-stu-id="f1c81-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="f1c81-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="f1c81-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="f1c81-105">[DotNet Watch](https://www.nuget.org/packages/dotnet-watch) è uno strumento che esegue un comando [interfaccia della riga di comando di .NET Core](/dotnet/core/tools) quando i file di origine cambiano.</span><span class="sxs-lookup"><span data-stu-id="f1c81-105">[dotnet watch](https://www.nuget.org/packages/dotnet-watch) is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="f1c81-106">Ad esempio, una modifica di file può attivare la compilazione, l'esecuzione di test o la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="f1c81-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="f1c81-107">Questa esercitazione usa un'API Web esistente con due endpoint, di cui uno restituisce una somma e l'altro un prodotto.</span><span class="sxs-lookup"><span data-stu-id="f1c81-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="f1c81-108">Il metodo del prodotto ha un bug, che verrà corretto in questa esercitazione.</span><span class="sxs-lookup"><span data-stu-id="f1c81-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="f1c81-109">Scaricare l' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span><span class="sxs-lookup"><span data-stu-id="f1c81-109">Download the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="f1c81-110">Questa è costituita da due progetti: *WebApp* (un'API Web di ASP.NET Core) e *WebAppTests* (unit test per l'API Web).</span><span class="sxs-lookup"><span data-stu-id="f1c81-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="f1c81-111">In una shell dei comandi passare alla cartella *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="f1c81-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="f1c81-112">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="f1c81-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="f1c81-113">È possibile usare `dotnet run --project <PROJECT>` per specificare un progetto da eseguire.</span><span class="sxs-lookup"><span data-stu-id="f1c81-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="f1c81-114">Ad esempio, l'esecuzione di `dotnet run --project WebApp` dalla radice dell'app di esempio consentirà di eseguire anche il progetto *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="f1c81-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="f1c81-115">L'output della console visualizza messaggi simili al seguente che indicano che l'app è in esecuzione e in attesa di richieste:</span><span class="sxs-lookup"><span data-stu-id="f1c81-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="f1c81-116">In un Web browser passare a `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span><span class="sxs-lookup"><span data-stu-id="f1c81-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="f1c81-117">Dovrebbe essere visualizzato il risultato `9`.</span><span class="sxs-lookup"><span data-stu-id="f1c81-117">You should see the result of `9`.</span></span>

<span data-ttu-id="f1c81-118">Passare all'API del prodotto (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span><span class="sxs-lookup"><span data-stu-id="f1c81-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="f1c81-119">Restituisce `9` e non `20` come previsto.</span><span class="sxs-lookup"><span data-stu-id="f1c81-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="f1c81-120">Questo problema verrà corretto più avanti nell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="f1c81-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="f1c81-121">Aggiungere `dotnet watch` a un progetto</span><span class="sxs-lookup"><span data-stu-id="f1c81-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="f1c81-122">Lo strumento watcher per file `dotnet watch` è incluso nella versione 2.1.300 di .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="f1c81-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="f1c81-123">Se si usa una versione precedente di .NET Core SDK, i passaggi seguenti sono obbligatori.</span><span class="sxs-lookup"><span data-stu-id="f1c81-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="f1c81-124">Aggiungere un riferimento al pacchetto `Microsoft.DotNet.Watcher.Tools` nel file *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="f1c81-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="f1c81-125">Installare il pacchetto `Microsoft.DotNet.Watcher.Tools` eseguendo il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="f1c81-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="f1c81-126">Eseguire i comandi dell'interfaccia della riga di comando di .NET Core con `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="f1c81-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="f1c81-127">Qualsiasi [comando dell'interfaccia della riga di comando di .NET Core](/dotnet/core/tools#cli-commands) può essere eseguito con `dotnet watch`.</span><span class="sxs-lookup"><span data-stu-id="f1c81-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="f1c81-128">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="f1c81-128">For example:</span></span>

| <span data-ttu-id="f1c81-129">Comando</span><span class="sxs-lookup"><span data-stu-id="f1c81-129">Command</span></span> | <span data-ttu-id="f1c81-130">Comando con watch</span><span class="sxs-lookup"><span data-stu-id="f1c81-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="f1c81-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="f1c81-131">dotnet run</span></span> | <span data-ttu-id="f1c81-132">dotnet watch run</span><span class="sxs-lookup"><span data-stu-id="f1c81-132">dotnet watch run</span></span> |
| <span data-ttu-id="f1c81-133">dotnet run -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="f1c81-133">dotnet run -f netcoreapp2.0</span></span> | <span data-ttu-id="f1c81-134">dotnet watch run -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="f1c81-134">dotnet watch run -f netcoreapp2.0</span></span> |
| <span data-ttu-id="f1c81-135">dotnet run -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="f1c81-135">dotnet run -f netcoreapp2.0 -- --arg1</span></span> | <span data-ttu-id="f1c81-136">dotnet watch run -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="f1c81-136">dotnet watch run -f netcoreapp2.0 -- --arg1</span></span> |
| <span data-ttu-id="f1c81-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="f1c81-137">dotnet test</span></span> | <span data-ttu-id="f1c81-138">dotnet watch test</span><span class="sxs-lookup"><span data-stu-id="f1c81-138">dotnet watch test</span></span> |

<span data-ttu-id="f1c81-139">Eseguire `dotnet watch run` nella cartella *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="f1c81-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="f1c81-140">L'output della console indica che `watch` è stato avviato.</span><span class="sxs-lookup"><span data-stu-id="f1c81-140">The console output indicates `watch` has started.</span></span>

> [!NOTE]
> <span data-ttu-id="f1c81-141">È possibile usare `dotnet watch --project <PROJECT>` per specificare un progetto da controllare.</span><span class="sxs-lookup"><span data-stu-id="f1c81-141">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="f1c81-142">Ad esempio, l'esecuzione di `dotnet watch --project WebApp run` dalla radice dell'app di esempio consentirà di eseguire e controllare anche il progetto *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="f1c81-142">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="f1c81-143">Apportare modifiche con `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="f1c81-143">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="f1c81-144">Assicurarsi che `dotnet watch` sia in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="f1c81-144">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="f1c81-145">Correggere il bug nel metodo `Product` di *MathController.cs* in modo che restituisca il prodotto e non la somma:</span><span class="sxs-lookup"><span data-stu-id="f1c81-145">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="f1c81-146">Salvare il file.</span><span class="sxs-lookup"><span data-stu-id="f1c81-146">Save the file.</span></span> <span data-ttu-id="f1c81-147">L'output della console indica che `dotnet watch` ha rilevato una modifica del file e ha riavviato l'app.</span><span class="sxs-lookup"><span data-stu-id="f1c81-147">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="f1c81-148">Verificare che `http://localhost:<port number>/api/math/product?a=4&b=5` restituisca il risultato corretto.</span><span class="sxs-lookup"><span data-stu-id="f1c81-148">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="f1c81-149">Eseguire test con `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="f1c81-149">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="f1c81-150">Modificare il metodo `Product` di *MathController.cs* in modo che restituisca la somma.</span><span class="sxs-lookup"><span data-stu-id="f1c81-150">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="f1c81-151">Salvare il file.</span><span class="sxs-lookup"><span data-stu-id="f1c81-151">Save the file.</span></span>
1. <span data-ttu-id="f1c81-152">In una shell dei comandi passare alla cartella *WebAppTests*.</span><span class="sxs-lookup"><span data-stu-id="f1c81-152">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="f1c81-153">Eseguire [dotnet restore](/dotnet/core/tools/dotnet-restore).</span><span class="sxs-lookup"><span data-stu-id="f1c81-153">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="f1c81-154">Eseguire `dotnet watch test`.</span><span class="sxs-lookup"><span data-stu-id="f1c81-154">Run `dotnet watch test`.</span></span> <span data-ttu-id="f1c81-155">L'output indica che un test non è stato superato e che il watcher è in attesa di modifiche ai file:</span><span class="sxs-lookup"><span data-stu-id="f1c81-155">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="f1c81-156">Correggere il codice del metodo `Product` in modo che restituisca il prodotto.</span><span class="sxs-lookup"><span data-stu-id="f1c81-156">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="f1c81-157">Salvare il file.</span><span class="sxs-lookup"><span data-stu-id="f1c81-157">Save the file.</span></span>

<span data-ttu-id="f1c81-158">`dotnet watch` rileva la modifica ai file ed esegue di nuovo i test.</span><span class="sxs-lookup"><span data-stu-id="f1c81-158">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="f1c81-159">L'output della console indica che i test sono stati superati.</span><span class="sxs-lookup"><span data-stu-id="f1c81-159">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="f1c81-160">Personalizzare l'elenco dei file da controllare</span><span class="sxs-lookup"><span data-stu-id="f1c81-160">Customize files list to watch</span></span>

<span data-ttu-id="f1c81-161">Per impostazione predefinita, `dotnet-watch` tiene traccia di tutti i file che soddisfano i criteri GLOB seguenti:</span><span class="sxs-lookup"><span data-stu-id="f1c81-161">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="f1c81-162">È possibile aggiungere altri elementi all'elenco di controllo modificando il file con estensione *csproj*.</span><span class="sxs-lookup"><span data-stu-id="f1c81-162">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="f1c81-163">Gli elementi possono essere specificati singolarmente o tramite criteri GLOB.</span><span class="sxs-lookup"><span data-stu-id="f1c81-163">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="f1c81-164">Esclusione di file dal controllo</span><span class="sxs-lookup"><span data-stu-id="f1c81-164">Opt-out of files to be watched</span></span>

<span data-ttu-id="f1c81-165">È possibile configurare `dotnet-watch` in modo che ignori le impostazioni predefinite.</span><span class="sxs-lookup"><span data-stu-id="f1c81-165">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="f1c81-166">Per ignorare file specifici, aggiungere l'attributo `Watch="false"` alla definizione di un elemento nel file con estensione *csproj*:</span><span class="sxs-lookup"><span data-stu-id="f1c81-166">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a><span data-ttu-id="f1c81-167">Progetti di controllo personalizzati</span><span class="sxs-lookup"><span data-stu-id="f1c81-167">Custom watch projects</span></span>

<span data-ttu-id="f1c81-168">`dotnet-watch` non è limitato a progetti C#.</span><span class="sxs-lookup"><span data-stu-id="f1c81-168">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="f1c81-169">È possibile creare progetti di controllo personalizzati per gestire scenari diversi.</span><span class="sxs-lookup"><span data-stu-id="f1c81-169">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="f1c81-170">Si consideri il layout di progetto seguente:</span><span class="sxs-lookup"><span data-stu-id="f1c81-170">Consider the following project layout:</span></span>

* <span data-ttu-id="f1c81-171">**test**</span><span class="sxs-lookup"><span data-stu-id="f1c81-171">**test/**</span></span>
  * <span data-ttu-id="f1c81-172">*UnitTests/UnitTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="f1c81-172">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="f1c81-173">*IntegrationTests/IntegrationTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="f1c81-173">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="f1c81-174">Se l'obiettivo consiste nel controllare entrambi i progetti, creare un file di progetto personalizzato configurato per controllarli entrambi:</span><span class="sxs-lookup"><span data-stu-id="f1c81-174">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

<span data-ttu-id="f1c81-175">Per avviare il controllo dei file per entrambi i progetti, passare alla cartella *test*.</span><span class="sxs-lookup"><span data-stu-id="f1c81-175">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="f1c81-176">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="f1c81-176">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="f1c81-177">VSTest viene eseguito quando un qualsiasi file viene modificato in uno dei progetti di test.</span><span class="sxs-lookup"><span data-stu-id="f1c81-177">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="f1c81-178">`dotnet-watch` in GitHub</span><span class="sxs-lookup"><span data-stu-id="f1c81-178">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="f1c81-179">`dotnet-watch`fa parte del [repository DotNet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch)di GitHub.</span><span class="sxs-lookup"><span data-stu-id="f1c81-179">`dotnet-watch` is part of the GitHub [dotnet/AspNetCore repository](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
