---
title: Comando dotnet aspnet-codegenerator
author: rick-anderson
description: Il comando dotnet aspnet-codegenerator esegue lo scaffolding dei progetti ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: af151b65d3fefb5863529de1cd97c6315f11ca82
ms.sourcegitcommit: ca6a1f100c1a3f59999189aa962523442dd4ead1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87444130"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="1271c-103">dotnet aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="1271c-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="1271c-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1271c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1271c-105">`dotnet aspnet-codegenerator` - Esegue il motore di scaffolding di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1271c-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="1271c-106">`dotnet aspnet-codegenerator` è necessario solo per eseguire lo scaffolding dalla riga di comando. Non è necessario per usare lo scaffolding con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1271c-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

<span data-ttu-id="1271c-107">Questo articolo si applica a [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="1271c-107">This article applies to [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) and later.</span></span>

## <a name="installing-aspnet-codegenerator"></a><span data-ttu-id="1271c-108">Installazione di aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="1271c-108">Installing aspnet-codegenerator</span></span>

<span data-ttu-id="1271c-109">`dotnet-aspnet-codegenerator` è uno [strumento global](/dotnet/core/tools/global-tools) che deve essere installato.</span><span class="sxs-lookup"><span data-stu-id="1271c-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="1271c-110">Il comando seguente installa l'ultima versione stabile dello strumento `dotnet-aspnet-codegenerator`:</span><span class="sxs-lookup"><span data-stu-id="1271c-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="1271c-111">Il comando seguente aggiorna `dotnet-aspnet-codegenerator` alla versione stabile più recente disponibile dai .NET Core SDK installati:</span><span class="sxs-lookup"><span data-stu-id="1271c-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="1271c-112">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="1271c-112">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="1271c-113">Descrizione</span><span class="sxs-lookup"><span data-stu-id="1271c-113">Description</span></span>

<span data-ttu-id="1271c-114">Il comando globale `dotnet aspnet-codegenerator` esegue il generatore di codice e il motore di scaffolding di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1271c-114">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="1271c-115">Argomenti</span><span class="sxs-lookup"><span data-stu-id="1271c-115">Arguments</span></span>

`generator`

<span data-ttu-id="1271c-116">Il generatore di codice da eseguire.</span><span class="sxs-lookup"><span data-stu-id="1271c-116">The code generator to run.</span></span> <span data-ttu-id="1271c-117">Sono disponibili i generatori seguenti:</span><span class="sxs-lookup"><span data-stu-id="1271c-117">The following generators are available:</span></span>

| <span data-ttu-id="1271c-118">Generatore</span><span class="sxs-lookup"><span data-stu-id="1271c-118">Generator</span></span>  | <span data-ttu-id="1271c-119">Operazione</span><span class="sxs-lookup"><span data-stu-id="1271c-119">Operation</span></span>                                                            |
| ---------- | -------------------------------------------------------------------- |
| <span data-ttu-id="1271c-120">area</span><span class="sxs-lookup"><span data-stu-id="1271c-120">area</span></span>       | [<span data-ttu-id="1271c-121">Esegue lo scaffolding di un'area</span><span class="sxs-lookup"><span data-stu-id="1271c-121">Scaffolds an Area</span></span>](xref:mvc/controllers/areas)                      |
| <span data-ttu-id="1271c-122">controller</span><span class="sxs-lookup"><span data-stu-id="1271c-122">controller</span></span> | [<span data-ttu-id="1271c-123">Esegue lo scaffolding di un controller</span><span class="sxs-lookup"><span data-stu-id="1271c-123">Scaffolds a controller</span></span>](xref:tutorials/first-mvc-app/adding-model)  |
| <span data-ttu-id="1271c-124">identity</span><span class="sxs-lookup"><span data-stu-id="1271c-124">identity</span></span>   | [<span data-ttu-id="1271c-125">Esegue lo scaffoldingIdentity</span><span class="sxs-lookup"><span data-stu-id="1271c-125">Scaffolds Identity</span></span>](xref:security/authentication/scaffold-identity) |
| <span data-ttu-id="1271c-126">razorpage</span><span class="sxs-lookup"><span data-stu-id="1271c-126">razorpage</span></span>  | [<span data-ttu-id="1271c-127">Pagine di ponteggi Razor</span><span class="sxs-lookup"><span data-stu-id="1271c-127">Scaffolds Razor Pages</span></span>](xref:tutorials/razor-pages/model)            |
| <span data-ttu-id="1271c-128">vista</span><span class="sxs-lookup"><span data-stu-id="1271c-128">view</span></span>       | [<span data-ttu-id="1271c-129">Esegue lo scaffolding di una visualizzazione</span><span class="sxs-lookup"><span data-stu-id="1271c-129">Scaffolds a view</span></span>](xref:mvc/views/overview)                          |

## <a name="options"></a><span data-ttu-id="1271c-130">Opzioni</span><span class="sxs-lookup"><span data-stu-id="1271c-130">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="1271c-131">Specifica la directory del pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="1271c-131">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="1271c-132">Definisce la configurazione di compilazione.</span><span class="sxs-lookup"><span data-stu-id="1271c-132">Defines the build configuration.</span></span> <span data-ttu-id="1271c-133">Il valore predefinito è `Debug`.</span><span class="sxs-lookup"><span data-stu-id="1271c-133">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="1271c-134">[Framework](/dotnet/standard/frameworks) di destinazione da usare.</span><span class="sxs-lookup"><span data-stu-id="1271c-134">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="1271c-135">Ad esempio: `net46`.</span><span class="sxs-lookup"><span data-stu-id="1271c-135">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="1271c-136">Percorso di base di compilazione.</span><span class="sxs-lookup"><span data-stu-id="1271c-136">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="1271c-137">Stampa una breve guida per il comando.</span><span class="sxs-lookup"><span data-stu-id="1271c-137">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="1271c-138">Non compila il progetto prima dell'esecuzione.</span><span class="sxs-lookup"><span data-stu-id="1271c-138">Doesn't build the project before running.</span></span> <span data-ttu-id="1271c-139">Imposta anche in modo implicito il flag `--no-restore`.</span><span class="sxs-lookup"><span data-stu-id="1271c-139">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="1271c-140">Specifica il percorso del file di progetto da eseguire: nome della cartella o percorso completo.</span><span class="sxs-lookup"><span data-stu-id="1271c-140">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="1271c-141">Se non specificato, per impostazione predefinita il percorso corrisponde alla directory corrente.</span><span class="sxs-lookup"><span data-stu-id="1271c-141">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="1271c-142">Opzioni del generatore</span><span class="sxs-lookup"><span data-stu-id="1271c-142">Generator options</span></span>

<span data-ttu-id="1271c-143">Nelle sezioni seguenti vengono descritte in dettaglio le opzioni disponibili per i generatori supportati:</span><span class="sxs-lookup"><span data-stu-id="1271c-143">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="1271c-144">Area</span><span class="sxs-lookup"><span data-stu-id="1271c-144">Area</span></span>
* <span data-ttu-id="1271c-145">Controller</span><span class="sxs-lookup"><span data-stu-id="1271c-145">Controller</span></span>
* Identity  
* <span data-ttu-id="1271c-146">Razorpage</span><span class="sxs-lookup"><span data-stu-id="1271c-146">Razorpage</span></span>
* <span data-ttu-id="1271c-147">Visualizza</span><span class="sxs-lookup"><span data-stu-id="1271c-147">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="1271c-148">Opzioni per area</span><span class="sxs-lookup"><span data-stu-id="1271c-148">Area options</span></span>

<span data-ttu-id="1271c-149">Questo strumento è progettato per i progetti Web ASP.NET Core con controller e visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="1271c-149">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="1271c-150">Non è destinato alle Razor app di pagine.</span><span class="sxs-lookup"><span data-stu-id="1271c-150">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="1271c-151">Sintassi: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="1271c-151">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="1271c-152">Il comando precedente genera le cartelle seguenti:</span><span class="sxs-lookup"><span data-stu-id="1271c-152">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="1271c-153">*Aree*</span><span class="sxs-lookup"><span data-stu-id="1271c-153">*Areas*</span></span>
  * <span data-ttu-id="1271c-154">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="1271c-154">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="1271c-155">*Controllers*</span><span class="sxs-lookup"><span data-stu-id="1271c-155">*Controllers*</span></span>
    * <span data-ttu-id="1271c-156">*Dati*</span><span class="sxs-lookup"><span data-stu-id="1271c-156">*Data*</span></span>
    * <span data-ttu-id="1271c-157">*Modelli*</span><span class="sxs-lookup"><span data-stu-id="1271c-157">*Models*</span></span>
    * <span data-ttu-id="1271c-158">*Visualizzazioni*</span><span class="sxs-lookup"><span data-stu-id="1271c-158">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="1271c-159">Opzioni per controller</span><span class="sxs-lookup"><span data-stu-id="1271c-159">Controller options</span></span>

<span data-ttu-id="1271c-160">Nella tabella seguente sono elencate le opzioni per `aspnet-codegenerator` `controller` e `razorpage` :</span><span class="sxs-lookup"><span data-stu-id="1271c-160">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="1271c-161">La tabella seguente contiene un elenco di opzioni specifiche per `aspnet-codegenerator controller`:</span><span class="sxs-lookup"><span data-stu-id="1271c-161">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="1271c-162">Opzione</span><span class="sxs-lookup"><span data-stu-id="1271c-162">Option</span></span>                         | <span data-ttu-id="1271c-163">Descrizione</span><span class="sxs-lookup"><span data-stu-id="1271c-163">Description</span></span>                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| <span data-ttu-id="1271c-164">--controllerName o -name</span><span class="sxs-lookup"><span data-stu-id="1271c-164">--controllerName or -name</span></span>      | <span data-ttu-id="1271c-165">Nome del controller.</span><span class="sxs-lookup"><span data-stu-id="1271c-165">Name of the controller.</span></span>                                                                                   |
| <span data-ttu-id="1271c-166">--useAsyncActions o -async</span><span class="sxs-lookup"><span data-stu-id="1271c-166">--useAsyncActions or -async</span></span>    | <span data-ttu-id="1271c-167">Genera le azioni del controller asincrone.</span><span class="sxs-lookup"><span data-stu-id="1271c-167">Generate async controller actions.</span></span>                                                                        |
| <span data-ttu-id="1271c-168">--noViews or -nv</span><span class="sxs-lookup"><span data-stu-id="1271c-168">--noViews or -nv</span></span>               | <span data-ttu-id="1271c-169">**Non** genera alcuna visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="1271c-169">Generate **no** views.</span></span>                                                                                    |
| <span data-ttu-id="1271c-170">--restWithNoViews o -api</span><span class="sxs-lookup"><span data-stu-id="1271c-170">--restWithNoViews or -api</span></span>      | <span data-ttu-id="1271c-171">Genera un controller con l'API in stile REST.</span><span class="sxs-lookup"><span data-stu-id="1271c-171">Generate a Controller with REST style API.</span></span> <span data-ttu-id="1271c-172">Si presuppone `noViews` e qualsiasi opzione correlata alla visualizzazione viene ignorata.</span><span class="sxs-lookup"><span data-stu-id="1271c-172">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="1271c-173">--readWriteActions o -actions</span><span class="sxs-lookup"><span data-stu-id="1271c-173">--readWriteActions or -actions</span></span> | <span data-ttu-id="1271c-174">Genera un controller con azioni di lettura/scrittura senza un modello.</span><span class="sxs-lookup"><span data-stu-id="1271c-174">Generate controller with read/write actions without a model.</span></span>                                              |

<span data-ttu-id="1271c-175">Usare l'opzione `-h` per ottenere informazioni della Guida per il comando `aspnet-codegenerator controller`:</span><span class="sxs-lookup"><span data-stu-id="1271c-175">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="1271c-176">Vedere [Eseguire lo scaffolding del modello di filmato](xref:tutorials/first-mvc-app/adding-model) per un esempio di `dotnet aspnet-codegenerator controller`.</span><span class="sxs-lookup"><span data-stu-id="1271c-176">See [Scaffold the movie model](xref:tutorials/first-mvc-app/adding-model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="no-locrazorpage"></a><span data-ttu-id="1271c-177">Razorpage</span><span class="sxs-lookup"><span data-stu-id="1271c-177">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="1271c-178">RazorLe pagine possono essere personalizzate individualmente specificando il nome della nuova pagina e il modello da usare.</span><span class="sxs-lookup"><span data-stu-id="1271c-178">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="1271c-179">I modelli supportati sono:</span><span class="sxs-lookup"><span data-stu-id="1271c-179">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="1271c-180">Ad esempio, il comando seguente usa il modello Edit per generare *MyEdit.cshtml* e *MyEdit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="1271c-180">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="1271c-181">In genere, il modello e il nome di file generato non vengono specificati e vengono creati i modelli seguenti:</span><span class="sxs-lookup"><span data-stu-id="1271c-181">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="1271c-182">Nella tabella seguente sono elencate le opzioni per `aspnet-codegenerator` `razorpage` e `controller` :</span><span class="sxs-lookup"><span data-stu-id="1271c-182">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="1271c-183">La tabella seguente contiene un elenco di opzioni specifiche per `aspnet-codegenerator razorpage`:</span><span class="sxs-lookup"><span data-stu-id="1271c-183">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="1271c-184">Opzione</span><span class="sxs-lookup"><span data-stu-id="1271c-184">Option</span></span>                        | <span data-ttu-id="1271c-185">Descrizione</span><span class="sxs-lookup"><span data-stu-id="1271c-185">Description</span></span>                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| <span data-ttu-id="1271c-186">--namespaceName o -namespace</span><span class="sxs-lookup"><span data-stu-id="1271c-186">--namespaceName or -namespace</span></span> | <span data-ttu-id="1271c-187">Nome dello spazio dei nomi da usare per il PageModel generato</span><span class="sxs-lookup"><span data-stu-id="1271c-187">The name of the namespace to use for the generated PageModel</span></span>                          |
| <span data-ttu-id="1271c-188">--partialView o -partial</span><span class="sxs-lookup"><span data-stu-id="1271c-188">--partialView or -partial</span></span>     | <span data-ttu-id="1271c-189">Genera una visualizzazione parziale.</span><span class="sxs-lookup"><span data-stu-id="1271c-189">Generate a partial view.</span></span> <span data-ttu-id="1271c-190">Le opzioni di layout -l e -udl vengono ignorate se viene specificata questa opzione.</span><span class="sxs-lookup"><span data-stu-id="1271c-190">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="1271c-191">--noPageModel o -npm</span><span class="sxs-lookup"><span data-stu-id="1271c-191">--noPageModel or -npm</span></span>         | <span data-ttu-id="1271c-192">Opzione per non generare una classe PageModel per un modello vuoto</span><span class="sxs-lookup"><span data-stu-id="1271c-192">Switch to not generate a PageModel class for Empty template</span></span>                           |

<span data-ttu-id="1271c-193">Usare l'opzione `-h` per ottenere informazioni della Guida per il comando `aspnet-codegenerator razorpage`:</span><span class="sxs-lookup"><span data-stu-id="1271c-193">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="1271c-194">Vedere [Eseguire lo scaffolding del modello di filmato](xref:tutorials/razor-pages/model) per un esempio di `dotnet aspnet-codegenerator razorpage`.</span><span class="sxs-lookup"><span data-stu-id="1271c-194">See [Scaffold the movie model](xref:tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### Identity

<span data-ttu-id="1271c-195">Vedere [impalcatura Identity ](xref:security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="1271c-195">See [Scaffold Identity](xref:security/authentication/scaffold-identity)</span></span>
