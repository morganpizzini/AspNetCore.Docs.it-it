---
title: Usare le API di ASP.NET base in una libreria di classiUse a Core APIs in a class library
author: scottaddie
description: Informazioni su come usare le API di ASP.NET Core in una libreria di classi.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: 5374d7eec4334223a4bba7ee26cb6e2f208ed20b
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977197"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a><span data-ttu-id="4bee7-103">Usare le API di ASP.NET base in una libreria di classiUse a Core APIs in a class library</span><span class="sxs-lookup"><span data-stu-id="4bee7-103">Use ASP.NET Core APIs in a class library</span></span>

<span data-ttu-id="4bee7-104">Di [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="4bee7-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="4bee7-105">Questo documento fornisce indicazioni per l'uso di API di ASP.NET base in una libreria di classi.</span><span class="sxs-lookup"><span data-stu-id="4bee7-105">This document provides guidance for using ASP.NET Core APIs in a class library.</span></span> <span data-ttu-id="4bee7-106">Per tutte le altre linee guida per la libreria, consultate Linee guida per la [libreria Open-source.](/dotnet/standard/library-guidance/)</span><span class="sxs-lookup"><span data-stu-id="4bee7-106">For all other library guidance, see [Open-source library guidance](/dotnet/standard/library-guidance/).</span></span>

## <a name="determine-which-aspnet-core-versions-to-support"></a><span data-ttu-id="4bee7-107">Determinare le versioni di ASP.NET Core da supportareDetermine which a Core versions to support</span><span class="sxs-lookup"><span data-stu-id="4bee7-107">Determine which ASP.NET Core versions to support</span></span>

<span data-ttu-id="4bee7-108">ASP.NET Core aderisce ai criteri di supporto di [.NET Core.](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)</span><span class="sxs-lookup"><span data-stu-id="4bee7-108">ASP.NET Core adheres to the [.NET Core support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span> <span data-ttu-id="4bee7-109">Consultare i criteri di supporto per determinare le versioni di ASP.NET Core da supportare in una libreria.</span><span class="sxs-lookup"><span data-stu-id="4bee7-109">Consult the support policy when determining which ASP.NET Core versions to support in a library.</span></span> <span data-ttu-id="4bee7-110">Una libreria deve:</span><span class="sxs-lookup"><span data-stu-id="4bee7-110">A library should:</span></span>

* <span data-ttu-id="4bee7-111">Fare uno sforzo per supportare tutte le versioni ASP.NET Core classificate come *supporto a lungo termine* (LTS).</span><span class="sxs-lookup"><span data-stu-id="4bee7-111">Make an effort to support all ASP.NET Core versions classified as *Long-Term Support* (LTS).</span></span>
* <span data-ttu-id="4bee7-112">Non sentirsi obbligato a supportare ASP.NET versioni di base classificate come *End of Life* (EOL).</span><span class="sxs-lookup"><span data-stu-id="4bee7-112">Not feel obligated to support ASP.NET Core versions classified as *End of Life* (EOL).</span></span>

<span data-ttu-id="4bee7-113">Man mano che vengono rese disponibili le versioni di anteprima di ASP.NET Core, le modifiche di rilievo vengono pubblicate nel repository [GitHub aspnet/Annunci.](https://github.com/aspnet/Announcements/issues)</span><span class="sxs-lookup"><span data-stu-id="4bee7-113">As preview releases of ASP.NET Core are made available, breaking changes are posted in the [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub repository.</span></span> <span data-ttu-id="4bee7-114">Il test di compatibilità delle librerie può essere condotto durante lo sviluppo delle funzionalità del framework.</span><span class="sxs-lookup"><span data-stu-id="4bee7-114">Compatibility testing of libraries can be conducted as framework features are being developed.</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="4bee7-115">Usare il framework condiviso ASP.NET CoreUse the ASP.NET Core shared framework</span><span class="sxs-lookup"><span data-stu-id="4bee7-115">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="4bee7-116">Con il rilascio di .NET Core 3.0, molti assembly ASP.NET Core non vengono più pubblicati in NuGet come pacchetti.</span><span class="sxs-lookup"><span data-stu-id="4bee7-116">With the release of .NET Core 3.0, many ASP.NET Core assemblies are no longer published to NuGet as packages.</span></span> <span data-ttu-id="4bee7-117">Al contrario, gli `Microsoft.AspNetCore.App` assembly sono inclusi nel framework condiviso, che viene installato con .NET Core SDK e programmi di installazione di runtime.</span><span class="sxs-lookup"><span data-stu-id="4bee7-117">Instead, the assemblies are included in the `Microsoft.AspNetCore.App` shared framework, which is installed with the .NET Core SDK and runtime installers.</span></span> <span data-ttu-id="4bee7-118">Per un elenco dei pacchetti non più pubblicati, consultate Rimuovere i riferimenti ai [pacchetti obsoleti.](xref:migration/22-to-30#remove-obsolete-package-references)</span><span class="sxs-lookup"><span data-stu-id="4bee7-118">For a list of packages no longer being published, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="4bee7-119">A partire da .NET Core 3.0, i progetti che utilizzano `Microsoft.NET.Sdk.Web` MSBuild SDK fanno riferimento in modo implicito al framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="4bee7-119">As of .NET Core 3.0, projects using the `Microsoft.NET.Sdk.Web` MSBuild SDK implicitly reference the shared framework.</span></span> <span data-ttu-id="4bee7-120">I progetti `Microsoft.NET.Sdk` `Microsoft.NET.Sdk.Razor` che usano l'SDK devono fare riferimento a ASP.NET Core per usare le API di ASP.NET Core nel framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="4bee7-120">Projects using the `Microsoft.NET.Sdk` or `Microsoft.NET.Sdk.Razor` SDK must reference ASP.NET Core to use ASP.NET Core APIs in the shared framework.</span></span>

<span data-ttu-id="4bee7-121">Per fare riferimento a `<FrameworkReference>` ASP.NET Core, aggiungere l'elemento seguente al file di progetto:To reference ASP.NET Core, add the following element to your project file:</span><span class="sxs-lookup"><span data-stu-id="4bee7-121">To reference ASP.NET Core, add the following `<FrameworkReference>` element to your project file:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

<span data-ttu-id="4bee7-122">Il riferimento a ASP.NET Core in questo modo è supportato solo per i progetti destinati a .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="4bee7-122">Referencing ASP.NET Core in this manner is only supported for projects targeting .NET Core 3.x.</span></span>

## <a name="include-blazor-extensibility"></a><span data-ttu-id="4bee7-123">Includi estendibilità Blazor</span><span class="sxs-lookup"><span data-stu-id="4bee7-123">Include Blazor extensibility</span></span>

<span data-ttu-id="4bee7-124">Blazor supporta i modelli di [hosting](xref:blazor/hosting-models)WebAssembly (WASM) e Server.</span><span class="sxs-lookup"><span data-stu-id="4bee7-124">Blazor supports WebAssembly (WASM) and Server [hosting models](xref:blazor/hosting-models).</span></span> <span data-ttu-id="4bee7-125">A meno che non vi sia un motivo specifico per non farlo, una libreria di [componenti Razor](xref:blazor/components) dovrebbe supportare entrambi i modelli di hosting.</span><span class="sxs-lookup"><span data-stu-id="4bee7-125">Unless there's a specific reason not to, a [Razor components](xref:blazor/components) library should support both hosting models.</span></span> <span data-ttu-id="4bee7-126">Una libreria di componenti Razor deve utilizzare [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="4bee7-126">A Razor components library must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

### <a name="support-both-hosting-models"></a><span data-ttu-id="4bee7-127">Supporta entrambi i modelli di hosting</span><span class="sxs-lookup"><span data-stu-id="4bee7-127">Support both hosting models</span></span>

<span data-ttu-id="4bee7-128">Per supportare il consumo di componenti Razor da entrambi i progetti [Blazor Server](xref:blazor/hosting-models#blazor-server) e [Blazor WASM,](xref:blazor/hosting-models#blazor-webassembly) utilizzare le istruzioni seguenti per l'editor.</span><span class="sxs-lookup"><span data-stu-id="4bee7-128">To support Razor component consumption from both [Blazor Server](xref:blazor/hosting-models#blazor-server) and [Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) projects, use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4bee7-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bee7-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4bee7-130">Utilizzare il modello di progetto **Libreria di classi Razor.Use** the Razor Class Library project template.</span><span class="sxs-lookup"><span data-stu-id="4bee7-130">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="4bee7-131">La casella di controllo **Pagine e viste** di supporto del modello deve essere deselezionata.</span><span class="sxs-lookup"><span data-stu-id="4bee7-131">The template's **Support pages and views** checkbox should be deselected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4bee7-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4bee7-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4bee7-133">Eseguire il seguente comando nel terminale integrato:</span><span class="sxs-lookup"><span data-stu-id="4bee7-133">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4bee7-134">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="4bee7-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4bee7-135">Utilizzare il modello di progetto **Libreria di classi Razor.Use** the Razor Class Library project template.</span><span class="sxs-lookup"><span data-stu-id="4bee7-135">Use the **Razor Class Library** project template.</span></span>

---

<span data-ttu-id="4bee7-136">Il progetto generato dal modello esegue le operazioni seguenti:The project generated from the template does the following things:</span><span class="sxs-lookup"><span data-stu-id="4bee7-136">The project generated from the template does the following things:</span></span>

* <span data-ttu-id="4bee7-137">Destinazioni .NET Standard 2.0.Targets .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="4bee7-137">Targets .NET Standard 2.0.</span></span>
* <span data-ttu-id="4bee7-138">Imposta la proprietà `RazorLangVersion` su `3.0`.</span><span class="sxs-lookup"><span data-stu-id="4bee7-138">Sets the `RazorLangVersion` property to `3.0`.</span></span> <span data-ttu-id="4bee7-139">`3.0`è il valore predefinito per .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="4bee7-139">`3.0` is the default value for .NET Core 3.x.</span></span>
* <span data-ttu-id="4bee7-140">Aggiunge i riferimenti al pacchetto seguenti:</span><span class="sxs-lookup"><span data-stu-id="4bee7-140">Adds the following package references:</span></span>
  * [<span data-ttu-id="4bee7-141">Microsoft.AspNetCore.Components</span><span class="sxs-lookup"><span data-stu-id="4bee7-141">Microsoft.AspNetCore.Components</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [<span data-ttu-id="4bee7-142">Microsoft.AspNetCore.Components.Web</span><span class="sxs-lookup"><span data-stu-id="4bee7-142">Microsoft.AspNetCore.Components.Web</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

<span data-ttu-id="4bee7-143">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="4bee7-143">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a><span data-ttu-id="4bee7-144">Supportare un modello di hosting specificoSupport a specific hosting model</span><span class="sxs-lookup"><span data-stu-id="4bee7-144">Support a specific hosting model</span></span>

<span data-ttu-id="4bee7-145">È molto meno comune supportare un singolo modello di hosting Blazor.</span><span class="sxs-lookup"><span data-stu-id="4bee7-145">It's far less common to support a single Blazor hosting model.</span></span> <span data-ttu-id="4bee7-146">Ad esempio, per supportare il consumo di componenti Razor solo dai progetti [Blazor Server:](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="4bee7-146">As an example, to support Razor component consumption from [Blazor Server](xref:blazor/hosting-models#blazor-server) projects only:</span></span>

* <span data-ttu-id="4bee7-147">Destinazione .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="4bee7-147">Target .NET Core 3.x.</span></span>
* <span data-ttu-id="4bee7-148">Aggiungere `<FrameworkReference>` un elemento per il framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="4bee7-148">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="4bee7-149">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="4bee7-149">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

<span data-ttu-id="4bee7-150">Per ulteriori informazioni sulle librerie contenenti componenti Razor , vedere ASP.NET librerie di [classi componenti Razor di base](xref:blazor/class-libraries).</span><span class="sxs-lookup"><span data-stu-id="4bee7-150">For more information on libraries containing Razor components, see [ASP.NET Core Razor components class libraries](xref:blazor/class-libraries).</span></span>

## <a name="include-mvc-extensibility"></a><span data-ttu-id="4bee7-151">Includere l'estensibilità MVC</span><span class="sxs-lookup"><span data-stu-id="4bee7-151">Include MVC extensibility</span></span>

<span data-ttu-id="4bee7-152">In questa sezione vengono descritti i suggerimenti per le raccolte che includono:This section outlines recommendations for libraries that include:</span><span class="sxs-lookup"><span data-stu-id="4bee7-152">This section outlines recommendations for libraries that include:</span></span>

* [<span data-ttu-id="4bee7-153">Visualizzazioni Razor o Pagine Razor</span><span class="sxs-lookup"><span data-stu-id="4bee7-153">Razor views or Razor Pages</span></span>](#razor-views-or-razor-pages)
* [<span data-ttu-id="4bee7-154">Helper tag</span><span class="sxs-lookup"><span data-stu-id="4bee7-154">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="4bee7-155">Componenti di visualizzazione</span><span class="sxs-lookup"><span data-stu-id="4bee7-155">View components</span></span>](#view-components)

<span data-ttu-id="4bee7-156">In questa sezione non viene illustrato il multitargeting per supportare più versioni di MVC.</span><span class="sxs-lookup"><span data-stu-id="4bee7-156">This section doesn't discuss multi-targeting to support multiple versions of MVC.</span></span> <span data-ttu-id="4bee7-157">Per istruzioni sul supporto di più versioni di ASP.NET Core, vedere [Support multiple ASP.NET Core.](#support-multiple-aspnet-core-versions)</span><span class="sxs-lookup"><span data-stu-id="4bee7-157">For guidance on supporting multiple ASP.NET Core versions, see [Support multiple ASP.NET Core versions](#support-multiple-aspnet-core-versions).</span></span>

### <a name="razor-views-or-razor-pages"></a><span data-ttu-id="4bee7-158">Visualizzazioni Razor o Pagine Razor</span><span class="sxs-lookup"><span data-stu-id="4bee7-158">Razor views or Razor Pages</span></span>

<span data-ttu-id="4bee7-159">Un progetto che include [le visualizzazioni Razor](xref:mvc/views/overview) o [le pagine Razor](xref:razor-pages/index) deve utilizzare [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="4bee7-159">A project that includes [Razor views](xref:mvc/views/overview) or [Razor Pages](xref:razor-pages/index) must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

<span data-ttu-id="4bee7-160">Se il progetto è destinato a .NET Core 3.x, richiede:If the project targets .NET Core 3.x, it requires:</span><span class="sxs-lookup"><span data-stu-id="4bee7-160">If the project targets .NET Core 3.x, it requires:</span></span>

* <span data-ttu-id="4bee7-161">Una `AddRazorSupportForMvc` proprietà MSBuild `true`impostata su .</span><span class="sxs-lookup"><span data-stu-id="4bee7-161">An `AddRazorSupportForMvc` MSBuild property set to `true`.</span></span>
* <span data-ttu-id="4bee7-162">Elemento `<FrameworkReference>` per il framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="4bee7-162">A `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="4bee7-163">Il modello di progetto Libreria di classi **Razor** soddisfa i requisiti precedenti per i progetti destinati a .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="4bee7-163">The **Razor Class Library** project template satisfies the preceding requirements for projects targeting .NET Core 3.x.</span></span> <span data-ttu-id="4bee7-164">Utilizzare le istruzioni seguenti per l'editor.</span><span class="sxs-lookup"><span data-stu-id="4bee7-164">Use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4bee7-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bee7-165">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4bee7-166">Utilizzare il modello di progetto **Libreria di classi Razor.Use** the Razor Class Library project template.</span><span class="sxs-lookup"><span data-stu-id="4bee7-166">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="4bee7-167">La casella di controllo **Pagine e viste** di supporto del modello deve essere selezionata.</span><span class="sxs-lookup"><span data-stu-id="4bee7-167">The template's **Support pages and views** checkbox should be selected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4bee7-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4bee7-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4bee7-169">Eseguire il seguente comando nel terminale integrato:</span><span class="sxs-lookup"><span data-stu-id="4bee7-169">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4bee7-170">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="4bee7-170">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4bee7-171">Nessun supporto del modello di progetto in questo momento.</span><span class="sxs-lookup"><span data-stu-id="4bee7-171">No project template support at this time.</span></span>

---

<span data-ttu-id="4bee7-172">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="4bee7-172">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

<span data-ttu-id="4bee7-173">Se invece il progetto è destinato a .NET Standard, è necessario un riferimento al pacchetto [Microsoft.AspNetCore.Mvc.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc)</span><span class="sxs-lookup"><span data-stu-id="4bee7-173">If the project targets .NET Standard instead, a [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) package reference is required.</span></span> <span data-ttu-id="4bee7-174">Il `Microsoft.AspNetCore.Mvc` pacchetto è stato spostato nel framework condiviso in ASP.NET Core 3.0 e pertanto non è più pubblicato.</span><span class="sxs-lookup"><span data-stu-id="4bee7-174">The `Microsoft.AspNetCore.Mvc` package moved into the shared framework in ASP.NET Core 3.0 and is therefore no longer published.</span></span> <span data-ttu-id="4bee7-175">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="4bee7-175">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a><span data-ttu-id="4bee7-176">Helper tag</span><span class="sxs-lookup"><span data-stu-id="4bee7-176">Tag Helpers</span></span>

<span data-ttu-id="4bee7-177">Un progetto che include gli `Microsoft.NET.Sdk` helper [tag](xref:mvc/views/tag-helpers/intro) deve usare l'SDK.</span><span class="sxs-lookup"><span data-stu-id="4bee7-177">A project that includes [Tag Helpers](xref:mvc/views/tag-helpers/intro) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="4bee7-178">Se la destinazione è .NET Core `<FrameworkReference>` 3.x, aggiungere un elemento per il framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="4bee7-178">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="4bee7-179">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="4bee7-179">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="4bee7-180">Se la destinazione è .NET Standard (per supportare versioni precedenti ASP.NET Core 3.x), aggiungere un riferimento al pacchetto a [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span><span class="sxs-lookup"><span data-stu-id="4bee7-180">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span></span> <span data-ttu-id="4bee7-181">Il `Microsoft.AspNetCore.Mvc.Razor` pacchetto è stato spostato nel framework condiviso e pertanto non è più pubblicato.</span><span class="sxs-lookup"><span data-stu-id="4bee7-181">The `Microsoft.AspNetCore.Mvc.Razor` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="4bee7-182">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="4bee7-182">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a><span data-ttu-id="4bee7-183">Componenti di visualizzazione</span><span class="sxs-lookup"><span data-stu-id="4bee7-183">View components</span></span>

<span data-ttu-id="4bee7-184">Un progetto che include i `Microsoft.NET.Sdk` componenti di [visualizzazione](xref:mvc/views/view-components) deve utilizzare l'SDK.</span><span class="sxs-lookup"><span data-stu-id="4bee7-184">A project that includes [View components](xref:mvc/views/view-components) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="4bee7-185">Se la destinazione è .NET Core `<FrameworkReference>` 3.x, aggiungere un elemento per il framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="4bee7-185">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="4bee7-186">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="4bee7-186">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="4bee7-187">Se la destinazione di .NET Standard (per supportare versioni precedenti a ASP.NET Core 3.x), aggiungere un riferimento al pacchetto a [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span><span class="sxs-lookup"><span data-stu-id="4bee7-187">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span></span> <span data-ttu-id="4bee7-188">Il `Microsoft.AspNetCore.Mvc.ViewFeatures` pacchetto è stato spostato nel framework condiviso e pertanto non è più pubblicato.</span><span class="sxs-lookup"><span data-stu-id="4bee7-188">The `Microsoft.AspNetCore.Mvc.ViewFeatures` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="4bee7-189">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="4bee7-189">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a><span data-ttu-id="4bee7-190">Supporta più versioni ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4bee7-190">Support multiple ASP.NET Core versions</span></span>

<span data-ttu-id="4bee7-191">Il multitargeting è necessario per creare una libreria che supporta più varianti di ASP.NET Core.Multi-targeting is required to author a library that supports multiple variants of ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4bee7-191">Multi-targeting is required to author a library that supports multiple variants of ASP.NET Core.</span></span> <span data-ttu-id="4bee7-192">Si consideri uno scenario in cui una libreria di helper tag deve supportare le seguenti varianti di ASP.NET Core:Consider a scenario in which a Tag Helpers library must support the following ASP.NET Core variants:</span><span class="sxs-lookup"><span data-stu-id="4bee7-192">Consider a scenario in which a Tag Helpers library must support the following ASP.NET Core variants:</span></span>

* <span data-ttu-id="4bee7-193">ASP.NET Core 2.1 destinato a .NET Framework 4.6.1</span><span class="sxs-lookup"><span data-stu-id="4bee7-193">ASP.NET Core 2.1 targeting .NET Framework 4.6.1</span></span>
* <span data-ttu-id="4bee7-194">ASP.NET Core 2.x destinato a .NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="4bee7-194">ASP.NET Core 2.x targeting .NET Core 2.x</span></span>
* <span data-ttu-id="4bee7-195">ASP.NET Core 3.x destinato a .NET Core 3.x</span><span class="sxs-lookup"><span data-stu-id="4bee7-195">ASP.NET Core 3.x targeting .NET Core 3.x</span></span>

<span data-ttu-id="4bee7-196">Il file di progetto seguente supporta `TargetFrameworks` queste varianti tramite la proprietà:</span><span class="sxs-lookup"><span data-stu-id="4bee7-196">The following project file supports these variants via the `TargetFrameworks` property:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

<span data-ttu-id="4bee7-197">Con il file di progetto precedente:</span><span class="sxs-lookup"><span data-stu-id="4bee7-197">With the preceding project file:</span></span>

* <span data-ttu-id="4bee7-198">Il `Markdig` pacchetto viene aggiunto per tutti i consumer.</span><span class="sxs-lookup"><span data-stu-id="4bee7-198">The `Markdig` package is added for all consumers.</span></span>
* <span data-ttu-id="4bee7-199">Viene aggiunto un riferimento a [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) per i consumer destinati a .NET Framework 4.6.1 o versione successiva o .NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="4bee7-199">A reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) is added for consumers targeting .NET Framework 4.6.1 or later or .NET Core 2.x.</span></span> <span data-ttu-id="4bee7-200">La versione 2.1.0 del pacchetto funziona con ASP.NET Core 2.2 a causa della compatibilità con le versioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="4bee7-200">Version 2.1.0 of the package works with ASP.NET Core 2.2 because of backwards compatibility.</span></span>
* <span data-ttu-id="4bee7-201">Viene fatto riferimento al framework condiviso per i consumer destinati a .NET Core 3.x.The shared framework is referenced for consumers targeting .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="4bee7-201">The shared framework is referenced for consumers targeting .NET Core 3.x.</span></span> <span data-ttu-id="4bee7-202">Il `Microsoft.AspNetCore.Mvc.Razor` pacchetto è incluso nel framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="4bee7-202">The `Microsoft.AspNetCore.Mvc.Razor` package is included in the shared framework.</span></span>

<span data-ttu-id="4bee7-203">In alternativa, .NET Standard 2.0 potrebbe essere destinato invece di scegliere come target sia .NET Core 2.1 che .NET Framework 4.6.1:</span><span class="sxs-lookup"><span data-stu-id="4bee7-203">Alternatively, .NET Standard 2.0 could be targeted instead of targeting both .NET Core 2.1 and .NET Framework 4.6.1:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

<span data-ttu-id="4bee7-204">Con il file di progetto precedente, esistono le seguenti avvertenze:</span><span class="sxs-lookup"><span data-stu-id="4bee7-204">With the preceding project file, the following caveats exist:</span></span>

* <span data-ttu-id="4bee7-205">Poiché la libreria contiene solo helper tag, è più semplice scegliere come destinazione le piattaforme specifiche su cui viene eseguito ASP.NET Core: .NET Core e .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="4bee7-205">Since the library only contains Tag Helpers, it's more straightforward to target the specific platforms on which ASP.NET Core runs: .NET Core and .NET Framework.</span></span> <span data-ttu-id="4bee7-206">Tag Helpers can't be used by other .NET Standard 2.0-compliant target frameworks such as Unity, UWP, and Xamarin.</span><span class="sxs-lookup"><span data-stu-id="4bee7-206">Tag Helpers can't be used by other .NET Standard 2.0-compliant target frameworks such as Unity, UWP, and Xamarin.</span></span>
* <span data-ttu-id="4bee7-207">L'uso di .NET Standard 2.0 da .NET Framework presenta alcuni problemi che sono stati risolti in .NET Framework 4.7.2.</span><span class="sxs-lookup"><span data-stu-id="4bee7-207">Using .NET Standard 2.0 from .NET Framework has some issues that were addressed in .NET Framework 4.7.2.</span></span> <span data-ttu-id="4bee7-208">È possibile migliorare l'esperienza per i consumer che usano .NET Framework 4.6.1 a 4.7.1 impostando come destinazione .NET Framework 4.6.1.You can improve the experience for consumers using .NET Framework 4.6.1 through 4.7.1 by targeting .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="4bee7-208">You can improve the experience for consumers using .NET Framework 4.6.1 through 4.7.1 by targeting .NET Framework 4.6.1.</span></span>

<span data-ttu-id="4bee7-209">Se la libreria deve chiamare API specifiche della piattaforma, scegliere come destinazione implementazioni .NET specifiche anziché .NET Standard.If your library needs to call platform-specific APIs, target specific .NET implementations instead of .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="4bee7-209">If your library needs to call platform-specific APIs, target specific .NET implementations instead of .NET Standard.</span></span> <span data-ttu-id="4bee7-210">Per ulteriori informazioni, consultate [Multitargeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span><span class="sxs-lookup"><span data-stu-id="4bee7-210">For more information, see [Multi-targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span></span>

## <a name="use-an-api-that-hasnt-changed"></a><span data-ttu-id="4bee7-211">Usare un'API che non è stata modificataUse an API that't changed</span><span class="sxs-lookup"><span data-stu-id="4bee7-211">Use an API that hasn't changed</span></span>

<span data-ttu-id="4bee7-212">Si immagini uno scenario in cui si sta aggiornando una libreria middleware da .NET Core 2.2 a 3.0.</span><span class="sxs-lookup"><span data-stu-id="4bee7-212">Imagine a scenario in which you're upgrading a middleware library from .NET Core 2.2 to 3.0.</span></span> <span data-ttu-id="4bee7-213">Le API middleware di ASP.NET Core in uso nella libreria non sono state modificate tra ASP.NET Core 2.2 e 3.0.</span><span class="sxs-lookup"><span data-stu-id="4bee7-213">The ASP.NET Core middleware APIs being used in the library haven't changed between ASP.NET Core 2.2 and 3.0.</span></span> <span data-ttu-id="4bee7-214">Per continuare a supportare la libreria middleware in .NET Core 3.0, attenersi alla seguente procedura:</span><span class="sxs-lookup"><span data-stu-id="4bee7-214">To continue supporting the middleware library in .NET Core 3.0, take the following steps:</span></span>

* <span data-ttu-id="4bee7-215">Seguire le [linee guida della libreria standard](/dotnet/standard/library-guidance/).</span><span class="sxs-lookup"><span data-stu-id="4bee7-215">Follow the [standard library guidance](/dotnet/standard/library-guidance/).</span></span>
* <span data-ttu-id="4bee7-216">Aggiungere un riferimento al pacchetto per ogni pacchetto NuGet dell'API se l'assembly corrispondente non esiste nel framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="4bee7-216">Add a package reference for each API's NuGet package if the corresponding assembly doesn't exist in the shared framework.</span></span>

## <a name="use-an-api-that-changed"></a><span data-ttu-id="4bee7-217">Usare un'API modificataUse an API that changed</span><span class="sxs-lookup"><span data-stu-id="4bee7-217">Use an API that changed</span></span>

<span data-ttu-id="4bee7-218">Si immagini uno scenario in cui si sta aggiornando una libreria da .NET Core 2.2 a .NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="4bee7-218">Imagine a scenario in which you're upgrading a library from .NET Core 2.2 to .NET Core 3.0.</span></span> <span data-ttu-id="4bee7-219">Un'API ASP.NET Core in uso nella libreria presenta una modifica sostanziale in ASP.NET Core 3.0.An ASP.NET Core API being used in the library has a [breaking change](/dotnet/core/compatibility/breaking-changes) in ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="4bee7-219">An ASP.NET Core API being used in the library has a [breaking change](/dotnet/core/compatibility/breaking-changes) in ASP.NET Core 3.0.</span></span> <span data-ttu-id="4bee7-220">Valutare se la libreria può essere riscritta per non usare l'API interrotta in tutte le versioni.</span><span class="sxs-lookup"><span data-stu-id="4bee7-220">Consider whether the library can be rewritten to not use the broken API in all versions.</span></span>

<span data-ttu-id="4bee7-221">Se è possibile riscrivere la libreria, eseguire questa operazione e continuare a utilizzare come destinazione un framework di destinazione precedente (ad esempio, .NET Standard 2.0 o .NET Framework 4.6.1) con riferimenti al pacchetto.</span><span class="sxs-lookup"><span data-stu-id="4bee7-221">If you can rewrite the library, do so and continue to target an earlier target framework (for example, .NET Standard 2.0 or .NET Framework 4.6.1) with package references.</span></span>

<span data-ttu-id="4bee7-222">Se non è possibile riscrivere la libreria, attenersi alla seguente procedura:</span><span class="sxs-lookup"><span data-stu-id="4bee7-222">If you can't rewrite the library, take the following steps:</span></span>

* <span data-ttu-id="4bee7-223">Aggiungere una destinazione per .NET Core 3.0.Add a target for .NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="4bee7-223">Add a target for .NET Core 3.0.</span></span>
* <span data-ttu-id="4bee7-224">Aggiungere `<FrameworkReference>` un elemento per il framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="4bee7-224">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="4bee7-225">Usare la direttiva per [il preprocessore #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) con il simbolo del framework di destinazione appropriato per compilare il codice in modo condizionale.</span><span class="sxs-lookup"><span data-stu-id="4bee7-225">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="4bee7-226">Ad esempio, le letture e le scritture sincrone nei flussi di richiesta e risposta HTTP sono disabilitate per impostazione predefinita a partire da ASP.NET Core 3.0.For example, synchronous reads and writes on HTTP request and response streams are disabled by a default as of ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="4bee7-226">For example, synchronous reads and writes on HTTP request and response streams are disabled by default as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="4bee7-227">ASP.NET Core 2.2 supporta il comportamento sincrono per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="4bee7-227">ASP.NET Core 2.2 supports the synchronous behavior by default.</span></span> <span data-ttu-id="4bee7-228">Si consideri una libreria middleware in cui le letture e le scritture sincrone devono essere abilitate in cui si verificano operazioni di I/O.</span><span class="sxs-lookup"><span data-stu-id="4bee7-228">Consider a middleware library in which synchronous reads and writes should be enabled where I/O is occurring.</span></span> <span data-ttu-id="4bee7-229">La libreria deve racchiudere il codice per abilitare le funzionalità sincrone nella direttiva del preprocessore appropriata.</span><span class="sxs-lookup"><span data-stu-id="4bee7-229">The library should enclose the code to enable synchronous features in the appropriate preprocessor directive.</span></span> <span data-ttu-id="4bee7-230">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="4bee7-230">For example:</span></span>

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a><span data-ttu-id="4bee7-231">Usare un'API introdotta nella 3.0Use an API introduced in 3.0</span><span class="sxs-lookup"><span data-stu-id="4bee7-231">Use an API introduced in 3.0</span></span>

<span data-ttu-id="4bee7-232">Si supponga di voler usare un'API ASP.NET Core introdotta in ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="4bee7-232">Imagine that you want to use an ASP.NET Core API that was introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="4bee7-233">Prendere in considerazione le domande seguenti:</span><span class="sxs-lookup"><span data-stu-id="4bee7-233">Consider the following questions:</span></span>

1. <span data-ttu-id="4bee7-234">La libreria richiede funzionalmente la nuova API?</span><span class="sxs-lookup"><span data-stu-id="4bee7-234">Does the library functionally require the new API?</span></span>
1. <span data-ttu-id="4bee7-235">La libreria può implementare questa funzionalità in modo diverso?</span><span class="sxs-lookup"><span data-stu-id="4bee7-235">Can the library implement this feature in a different way?</span></span>

<span data-ttu-id="4bee7-236">Se la libreria richiede funzionalmente l'API e non c'è modo di implementarla di livello inferiore:If the library functionally requires the API and there's no way to implement it down-level:</span><span class="sxs-lookup"><span data-stu-id="4bee7-236">If the library functionally requires the API and there's no way to implement it down-level:</span></span>

* <span data-ttu-id="4bee7-237">Solo .NET Core 3.x di destinazione.</span><span class="sxs-lookup"><span data-stu-id="4bee7-237">Target .NET Core 3.x only.</span></span>
* <span data-ttu-id="4bee7-238">Aggiungere `<FrameworkReference>` un elemento per il framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="4bee7-238">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="4bee7-239">Se la libreria può implementare la funzionalità in modo diverso:</span><span class="sxs-lookup"><span data-stu-id="4bee7-239">If the library can implement the feature in a different way:</span></span>

* <span data-ttu-id="4bee7-240">Aggiungere .NET Core 3.x come framework di destinazione.</span><span class="sxs-lookup"><span data-stu-id="4bee7-240">Add .NET Core 3.x as a target framework.</span></span>
* <span data-ttu-id="4bee7-241">Aggiungere `<FrameworkReference>` un elemento per il framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="4bee7-241">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="4bee7-242">Usare la direttiva per [il preprocessore #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) con il simbolo del framework di destinazione appropriato per compilare il codice in modo condizionale.</span><span class="sxs-lookup"><span data-stu-id="4bee7-242">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="4bee7-243">Ad esempio, l'helper <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> tag seguente usa l'interfaccia introdotta in ASP.NET Core 3.0.For example, the following Tag Helper uses the interface introduced in ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="4bee7-243">For example, the following Tag Helper uses the <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interface introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="4bee7-244">I consumer destinati a .NET Core 3.0 `NETCOREAPP3_0` eseguono il percorso del codice definito dal simbolo del framework di destinazione.</span><span class="sxs-lookup"><span data-stu-id="4bee7-244">Consumers targeting .NET Core 3.0 execute the code path defined by the `NETCOREAPP3_0` target framework symbol.</span></span> <span data-ttu-id="4bee7-245">Il tipo di parametro del <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> costruttore di Tag Helper viene modificato in per i consumer di .NET Core 2.1 e .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="4bee7-245">The Tag Helper's constructor parameter type changes to <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> for .NET Core 2.1 and .NET Framework 4.6.1 consumers.</span></span> <span data-ttu-id="4bee7-246">Questa modifica è stata necessaria perché `IHostingEnvironment` ASP.NET Core `IWebHostEnvironment` 3.0 contrassegnato come obsoleto e consigliato come sostituzione.</span><span class="sxs-lookup"><span data-stu-id="4bee7-246">This change was necessary because ASP.NET Core 3.0 marked `IHostingEnvironment` as obsolete and recommended `IWebHostEnvironment` as the replacement.</span></span>

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

<span data-ttu-id="4bee7-247">Il seguente file di progetto multi-targeting supporta questo scenario di tag Helper:</span><span class="sxs-lookup"><span data-stu-id="4bee7-247">The following multi-targeted project file supports this Tag Helper scenario:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a><span data-ttu-id="4bee7-248">Usare un'API rimossa dal framework condivisoUse an API removed from the shared framework</span><span class="sxs-lookup"><span data-stu-id="4bee7-248">Use an API removed from the shared framework</span></span>

<span data-ttu-id="4bee7-249">Per usare un ASP.NET assembly Core che è stato rimosso dal framework condiviso, aggiungere il riferimento al pacchetto appropriato.</span><span class="sxs-lookup"><span data-stu-id="4bee7-249">To use an ASP.NET Core assembly that was removed from the shared framework, add the appropriate package reference.</span></span> <span data-ttu-id="4bee7-250">Per un elenco dei pacchetti rimossi dal framework condiviso in ASP.NET Core 3.0, vedere [Remove obsoleto package references](xref:migration/22-to-30#remove-obsolete-package-references).</span><span class="sxs-lookup"><span data-stu-id="4bee7-250">For a list of packages removed from the shared framework in ASP.NET Core 3.0, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="4bee7-251">Ad esempio, per aggiungere il client API Web:</span><span class="sxs-lookup"><span data-stu-id="4bee7-251">For example, to add the web API client:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a><span data-ttu-id="4bee7-252">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="4bee7-252">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* <xref:blazor/class-libraries>
* [<span data-ttu-id="4bee7-253">Supporto per le implementazioni di .NET</span><span class="sxs-lookup"><span data-stu-id="4bee7-253">.NET implementation support</span></span>](/dotnet/standard/net-standard#net-implementation-support)
* [<span data-ttu-id="4bee7-254">Criteri di supporto .NET</span><span class="sxs-lookup"><span data-stu-id="4bee7-254">.NET support policies</span></span>](https://dotnet.microsoft.com/platform/support/policy)
