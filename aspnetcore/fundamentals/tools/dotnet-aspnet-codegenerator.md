---
title: Comando dotnet aspnet-codegenerator
author: rick-anderson
description: Il comando dotnet aspnet-codegenerator esegue lo scaffolding dei progetti ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/16/2020
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
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 8844b0014cac58f414d79df4c64bc0efac75bfe1
ms.sourcegitcommit: d29535ea0b4197443fd884aaa6e5b4b763d04fc7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920703"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="2d6ae-103">dotnet aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="2d6ae-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="2d6ae-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2d6ae-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2d6ae-105">`dotnet aspnet-codegenerator` - Esegue il motore di scaffolding di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="2d6ae-106">`dotnet aspnet-codegenerator` è necessario solo per eseguire lo scaffolding dalla riga di comando. Non è necessario per usare lo scaffolding con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

## <a name="install-and-update-aspnet-codegenerator"></a><span data-ttu-id="2d6ae-107">Installare e aggiornare ASPNET-CodeGenerator</span><span class="sxs-lookup"><span data-stu-id="2d6ae-107">Install and update aspnet-codegenerator</span></span>

<span data-ttu-id="2d6ae-108">Installare [.NET SDK](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="2d6ae-108">Install the [.NET SDK](https://dotnet.microsoft.com/download).</span></span>

<span data-ttu-id="2d6ae-109">`dotnet-aspnet-codegenerator` è uno [strumento global](/dotnet/core/tools/global-tools) che deve essere installato.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="2d6ae-110">Il comando seguente installa l'ultima versione stabile dello strumento `dotnet-aspnet-codegenerator`:</span><span class="sxs-lookup"><span data-stu-id="2d6ae-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="2d6ae-111">Il comando seguente aggiorna `dotnet-aspnet-codegenerator` alla versione stabile più recente disponibile dai .NET Core SDK installati:</span><span class="sxs-lookup"><span data-stu-id="2d6ae-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="uninstall-aspnet-codegenerator"></a><span data-ttu-id="2d6ae-112">Disinstalla ASPNET-CodeGenerator</span><span class="sxs-lookup"><span data-stu-id="2d6ae-112">Uninstall aspnet-codegenerator</span></span>

<span data-ttu-id="2d6ae-113">Potrebbe essere necessario disinstallare `aspnet-codegenerator` per risolvere i problemi.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-113">It may be necessary to uninstall the `aspnet-codegenerator` to resolve problems.</span></span> <span data-ttu-id="2d6ae-114">Se, ad esempio, è stata installata una versione di anteprima di `aspnet-codegenerator` , disinstallarla prima di installare la versione rilasciata.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-114">For example, if you installed a preview version of `aspnet-codegenerator`, uninstall it before installing the released version.</span></span>

<span data-ttu-id="2d6ae-115">I comandi seguenti consentono di disinstallare lo `dotnet-aspnet-codegenerator` strumento e installare la versione stabile più recente:</span><span class="sxs-lookup"><span data-stu-id="2d6ae-115">The following commands uninstall the `dotnet-aspnet-codegenerator` tool and installs the latest stable version:</span></span>

```dotnetcli
dotnet tool uninstall -g dotnet-aspnet-codegenerator
dotnet tool install -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="2d6ae-116">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="2d6ae-116">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="2d6ae-117">Description</span><span class="sxs-lookup"><span data-stu-id="2d6ae-117">Description</span></span>

<span data-ttu-id="2d6ae-118">Il comando globale `dotnet aspnet-codegenerator` esegue il generatore di codice e il motore di scaffolding di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-118">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="2d6ae-119">Argomenti</span><span class="sxs-lookup"><span data-stu-id="2d6ae-119">Arguments</span></span>

`generator`

<span data-ttu-id="2d6ae-120">Il generatore di codice da eseguire.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-120">The code generator to run.</span></span> <span data-ttu-id="2d6ae-121">Sono disponibili i generatori seguenti:</span><span class="sxs-lookup"><span data-stu-id="2d6ae-121">The following generators are available:</span></span>

| <span data-ttu-id="2d6ae-122">Generator</span><span class="sxs-lookup"><span data-stu-id="2d6ae-122">Generator</span></span>  | <span data-ttu-id="2d6ae-123">Operazione</span><span class="sxs-lookup"><span data-stu-id="2d6ae-123">Operation</span></span>                                                            |
| ---------- | -------------------------------------------------------------------- |
| <span data-ttu-id="2d6ae-124">area</span><span class="sxs-lookup"><span data-stu-id="2d6ae-124">area</span></span>       | [<span data-ttu-id="2d6ae-125">Esegue lo scaffolding di un'area</span><span class="sxs-lookup"><span data-stu-id="2d6ae-125">Scaffolds an Area</span></span>](xref:mvc/controllers/areas)                      |
| <span data-ttu-id="2d6ae-126">controller</span><span class="sxs-lookup"><span data-stu-id="2d6ae-126">controller</span></span> | [<span data-ttu-id="2d6ae-127">Esegue lo scaffolding di un controller</span><span class="sxs-lookup"><span data-stu-id="2d6ae-127">Scaffolds a controller</span></span>](xref:tutorials/first-mvc-app/adding-model)  |
| <span data-ttu-id="2d6ae-128">identity</span><span class="sxs-lookup"><span data-stu-id="2d6ae-128">identity</span></span>   | [<span data-ttu-id="2d6ae-129">Esegue lo scaffolding Identity</span><span class="sxs-lookup"><span data-stu-id="2d6ae-129">Scaffolds Identity</span></span>](xref:security/authentication/scaffold-identity) |
| <span data-ttu-id="2d6ae-130">razorpage</span><span class="sxs-lookup"><span data-stu-id="2d6ae-130">razorpage</span></span>  | [<span data-ttu-id="2d6ae-131">Pagine di ponteggi Razor</span><span class="sxs-lookup"><span data-stu-id="2d6ae-131">Scaffolds Razor Pages</span></span>](xref:tutorials/razor-pages/model)            |
| <span data-ttu-id="2d6ae-132">vista</span><span class="sxs-lookup"><span data-stu-id="2d6ae-132">view</span></span>       | [<span data-ttu-id="2d6ae-133">Esegue lo scaffolding di una visualizzazione</span><span class="sxs-lookup"><span data-stu-id="2d6ae-133">Scaffolds a view</span></span>](xref:mvc/views/overview)                          |

## <a name="options"></a><span data-ttu-id="2d6ae-134">Opzioni</span><span class="sxs-lookup"><span data-stu-id="2d6ae-134">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="2d6ae-135">Specifica la directory del pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-135">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="2d6ae-136">Definisce la configurazione di compilazione.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-136">Defines the build configuration.</span></span> <span data-ttu-id="2d6ae-137">Il valore predefinito è `Debug`.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-137">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="2d6ae-138">[Framework](/dotnet/standard/frameworks) di destinazione da usare.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-138">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="2d6ae-139">Ad esempio: `net46`.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-139">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="2d6ae-140">Percorso di base di compilazione.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-140">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="2d6ae-141">Stampa una breve guida per il comando.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-141">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="2d6ae-142">Non compila il progetto prima dell'esecuzione.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-142">Doesn't build the project before running.</span></span> <span data-ttu-id="2d6ae-143">Imposta anche in modo implicito il flag `--no-restore`.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-143">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="2d6ae-144">Specifica il percorso del file di progetto da eseguire: nome della cartella o percorso completo.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-144">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="2d6ae-145">Se non specificato, per impostazione predefinita il percorso corrisponde alla directory corrente.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-145">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="2d6ae-146">Opzioni del generatore</span><span class="sxs-lookup"><span data-stu-id="2d6ae-146">Generator options</span></span>

<span data-ttu-id="2d6ae-147">Nelle sezioni seguenti vengono descritte in dettaglio le opzioni disponibili per i generatori supportati:</span><span class="sxs-lookup"><span data-stu-id="2d6ae-147">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="2d6ae-148">Area</span><span class="sxs-lookup"><span data-stu-id="2d6ae-148">Area</span></span>
* <span data-ttu-id="2d6ae-149">Controller</span><span class="sxs-lookup"><span data-stu-id="2d6ae-149">Controller</span></span>
* Identity  
* <span data-ttu-id="2d6ae-150">Razorpage</span><span class="sxs-lookup"><span data-stu-id="2d6ae-150">Razorpage</span></span>
* <span data-ttu-id="2d6ae-151">Visualizzazione</span><span class="sxs-lookup"><span data-stu-id="2d6ae-151">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="2d6ae-152">Opzioni per area</span><span class="sxs-lookup"><span data-stu-id="2d6ae-152">Area options</span></span>

<span data-ttu-id="2d6ae-153">Questo strumento è progettato per i progetti Web ASP.NET Core con controller e visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-153">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="2d6ae-154">Non è destinato alle Razor app di pagine.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-154">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="2d6ae-155">Sintassi: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="2d6ae-155">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="2d6ae-156">Il comando precedente genera le cartelle seguenti:</span><span class="sxs-lookup"><span data-stu-id="2d6ae-156">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="2d6ae-157">*Aree*</span><span class="sxs-lookup"><span data-stu-id="2d6ae-157">*Areas*</span></span>
  * <span data-ttu-id="2d6ae-158">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="2d6ae-158">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="2d6ae-159">*Controllers*</span><span class="sxs-lookup"><span data-stu-id="2d6ae-159">*Controllers*</span></span>
    * <span data-ttu-id="2d6ae-160">*Dati*</span><span class="sxs-lookup"><span data-stu-id="2d6ae-160">*Data*</span></span>
    * <span data-ttu-id="2d6ae-161">*Modelli*</span><span class="sxs-lookup"><span data-stu-id="2d6ae-161">*Models*</span></span>
    * <span data-ttu-id="2d6ae-162">*Visualizzazioni*</span><span class="sxs-lookup"><span data-stu-id="2d6ae-162">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="2d6ae-163">Opzioni per controller</span><span class="sxs-lookup"><span data-stu-id="2d6ae-163">Controller options</span></span>

<span data-ttu-id="2d6ae-164">Nella tabella seguente sono elencate le opzioni per  `aspnet-codegenerator` `controller` e `razorpage` :</span><span class="sxs-lookup"><span data-stu-id="2d6ae-164">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="2d6ae-165">La tabella seguente contiene un elenco di opzioni specifiche per `aspnet-codegenerator controller`:</span><span class="sxs-lookup"><span data-stu-id="2d6ae-165">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="2d6ae-166">Opzione</span><span class="sxs-lookup"><span data-stu-id="2d6ae-166">Option</span></span>                         | <span data-ttu-id="2d6ae-167">Description</span><span class="sxs-lookup"><span data-stu-id="2d6ae-167">Description</span></span>                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| <span data-ttu-id="2d6ae-168">--controllerName o -name</span><span class="sxs-lookup"><span data-stu-id="2d6ae-168">--controllerName or -name</span></span>      | <span data-ttu-id="2d6ae-169">Nome del controller.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-169">Name of the controller.</span></span>                                                                                   |
| <span data-ttu-id="2d6ae-170">--useAsyncActions o -async</span><span class="sxs-lookup"><span data-stu-id="2d6ae-170">--useAsyncActions or -async</span></span>    | <span data-ttu-id="2d6ae-171">Genera le azioni del controller asincrone.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-171">Generate async controller actions.</span></span>                                                                        |
| <span data-ttu-id="2d6ae-172">--noViews or -nv</span><span class="sxs-lookup"><span data-stu-id="2d6ae-172">--noViews or -nv</span></span>               | <span data-ttu-id="2d6ae-173">**Non** genera alcuna visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-173">Generate **no** views.</span></span>                                                                                    |
| <span data-ttu-id="2d6ae-174">--restWithNoViews o -api</span><span class="sxs-lookup"><span data-stu-id="2d6ae-174">--restWithNoViews or -api</span></span>      | <span data-ttu-id="2d6ae-175">Genera un controller con l'API in stile REST.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-175">Generate a Controller with REST style API.</span></span> <span data-ttu-id="2d6ae-176">Si presuppone `noViews` e qualsiasi opzione correlata alla visualizzazione viene ignorata.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-176">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="2d6ae-177">--readWriteActions o -actions</span><span class="sxs-lookup"><span data-stu-id="2d6ae-177">--readWriteActions or -actions</span></span> | <span data-ttu-id="2d6ae-178">Genera un controller con azioni di lettura/scrittura senza un modello.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-178">Generate controller with read/write actions without a model.</span></span>                                              |

<span data-ttu-id="2d6ae-179">Usare l'opzione `-h` per ottenere informazioni della Guida per il comando `aspnet-codegenerator controller`:</span><span class="sxs-lookup"><span data-stu-id="2d6ae-179">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="2d6ae-180">Vedere [Eseguire lo scaffolding del modello di filmato](xref:tutorials/first-mvc-app/adding-model) per un esempio di `dotnet aspnet-codegenerator controller`.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-180">See [Scaffold the movie model](xref:tutorials/first-mvc-app/adding-model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="no-locrazorpage"></a><span data-ttu-id="2d6ae-181">Razorpage</span><span class="sxs-lookup"><span data-stu-id="2d6ae-181">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="2d6ae-182">Razor Le pagine possono essere personalizzate individualmente specificando il nome della nuova pagina e il modello da usare.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-182">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="2d6ae-183">I modelli supportati sono:</span><span class="sxs-lookup"><span data-stu-id="2d6ae-183">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="2d6ae-184">Ad esempio, il comando seguente usa il modello Edit per generare *MyEdit.cshtml* e *MyEdit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="2d6ae-184">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="2d6ae-185">In genere, il modello e il nome di file generato non vengono specificati e vengono creati i modelli seguenti:</span><span class="sxs-lookup"><span data-stu-id="2d6ae-185">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="2d6ae-186">Nella tabella seguente sono elencate le opzioni per  `aspnet-codegenerator` `razorpage` e `controller` :</span><span class="sxs-lookup"><span data-stu-id="2d6ae-186">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="2d6ae-187">La tabella seguente contiene un elenco di opzioni specifiche per `aspnet-codegenerator razorpage`:</span><span class="sxs-lookup"><span data-stu-id="2d6ae-187">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="2d6ae-188">Opzione</span><span class="sxs-lookup"><span data-stu-id="2d6ae-188">Option</span></span>                        | <span data-ttu-id="2d6ae-189">Description</span><span class="sxs-lookup"><span data-stu-id="2d6ae-189">Description</span></span>                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| <span data-ttu-id="2d6ae-190">--namespaceName o -namespace</span><span class="sxs-lookup"><span data-stu-id="2d6ae-190">--namespaceName or -namespace</span></span> | <span data-ttu-id="2d6ae-191">Nome dello spazio dei nomi da usare per il PageModel generato</span><span class="sxs-lookup"><span data-stu-id="2d6ae-191">The name of the namespace to use for the generated PageModel</span></span>                          |
| <span data-ttu-id="2d6ae-192">--partialView o -partial</span><span class="sxs-lookup"><span data-stu-id="2d6ae-192">--partialView or -partial</span></span>     | <span data-ttu-id="2d6ae-193">Genera una visualizzazione parziale.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-193">Generate a partial view.</span></span> <span data-ttu-id="2d6ae-194">Le opzioni di layout -l e -udl vengono ignorate se viene specificata questa opzione.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-194">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="2d6ae-195">--noPageModel o -npm</span><span class="sxs-lookup"><span data-stu-id="2d6ae-195">--noPageModel or -npm</span></span>         | <span data-ttu-id="2d6ae-196">Opzione per non generare una classe PageModel per un modello vuoto</span><span class="sxs-lookup"><span data-stu-id="2d6ae-196">Switch to not generate a PageModel class for Empty template</span></span>                           |

<span data-ttu-id="2d6ae-197">Usare l'opzione `-h` per ottenere informazioni della Guida per il comando `aspnet-codegenerator razorpage`:</span><span class="sxs-lookup"><span data-stu-id="2d6ae-197">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="2d6ae-198">Vedere [Eseguire lo scaffolding del modello di filmato](xref:tutorials/razor-pages/model) per un esempio di `dotnet aspnet-codegenerator razorpage`.</span><span class="sxs-lookup"><span data-stu-id="2d6ae-198">See [Scaffold the movie model](xref:tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### Identity

<span data-ttu-id="2d6ae-199">Vedere [impalcatura Identity ](xref:security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="2d6ae-199">See [Scaffold Identity](xref:security/authentication/scaffold-identity)</span></span>
