---
title: Usare API di ASP.NET Core in una libreria di classi
author: scottaddie
description: Informazioni su come usare le API ASP.NET Core in una libreria di classi.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: 85c0d850922b7118b101126c09b208b0db420f7e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776487"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a><span data-ttu-id="a5dd9-103">Usare API di ASP.NET Core in una libreria di classi</span><span class="sxs-lookup"><span data-stu-id="a5dd9-103">Use ASP.NET Core APIs in a class library</span></span>

<span data-ttu-id="a5dd9-104">Di [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="a5dd9-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="a5dd9-105">Questo documento fornisce indicazioni per l'uso di API ASP.NET Core in una libreria di classi.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-105">This document provides guidance for using ASP.NET Core APIs in a class library.</span></span> <span data-ttu-id="a5dd9-106">Per tutte le altre linee guida della libreria, vedere [linee guida per la libreria open source](/dotnet/standard/library-guidance/).</span><span class="sxs-lookup"><span data-stu-id="a5dd9-106">For all other library guidance, see [Open-source library guidance](/dotnet/standard/library-guidance/).</span></span>

## <a name="determine-which-aspnet-core-versions-to-support"></a><span data-ttu-id="a5dd9-107">Determinare quali versioni di ASP.NET Core supportare</span><span class="sxs-lookup"><span data-stu-id="a5dd9-107">Determine which ASP.NET Core versions to support</span></span>

<span data-ttu-id="a5dd9-108">ASP.NET Core rispetta i criteri di [supporto di .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="a5dd9-108">ASP.NET Core adheres to the [.NET Core support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span> <span data-ttu-id="a5dd9-109">Per determinare quali versioni di ASP.NET Core supportare in una raccolta, consultare i criteri di supporto.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-109">Consult the support policy when determining which ASP.NET Core versions to support in a library.</span></span> <span data-ttu-id="a5dd9-110">Una libreria deve:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-110">A library should:</span></span>

* <span data-ttu-id="a5dd9-111">Eseguire un tentativo di supporto per tutte le versioni di ASP.NET Core classificate come *supporto a lungo termine* (LTS).</span><span class="sxs-lookup"><span data-stu-id="a5dd9-111">Make an effort to support all ASP.NET Core versions classified as *Long-Term Support* (LTS).</span></span>
* <span data-ttu-id="a5dd9-112">Non è obbligatorio supportare le versioni di ASP.NET Core classificate come *End of Life* (EOL).</span><span class="sxs-lookup"><span data-stu-id="a5dd9-112">Not feel obligated to support ASP.NET Core versions classified as *End of Life* (EOL).</span></span>

<span data-ttu-id="a5dd9-113">Quando vengono rese disponibili le versioni di anteprima di ASP.NET Core, le modifiche di rilievo vengono pubblicate nel repository di GitHub [ASPNET/annunci](https://github.com/aspnet/Announcements/issues) .</span><span class="sxs-lookup"><span data-stu-id="a5dd9-113">As preview releases of ASP.NET Core are made available, breaking changes are posted in the [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub repository.</span></span> <span data-ttu-id="a5dd9-114">Il test di compatibilità delle librerie può essere eseguito quando si sviluppano funzionalità del Framework.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-114">Compatibility testing of libraries can be conducted as framework features are being developed.</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="a5dd9-115">Usare il Framework condiviso ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a5dd9-115">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="a5dd9-116">Con il rilascio di .NET Core 3,0, molti assembly ASP.NET Core non vengono più pubblicati in NuGet come pacchetti.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-116">With the release of .NET Core 3.0, many ASP.NET Core assemblies are no longer published to NuGet as packages.</span></span> <span data-ttu-id="a5dd9-117">Gli assembly vengono invece inclusi nel Framework `Microsoft.AspNetCore.App` condiviso, che viene installato con i programmi di installazione di .NET Core SDK e Runtime.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-117">Instead, the assemblies are included in the `Microsoft.AspNetCore.App` shared framework, which is installed with the .NET Core SDK and runtime installers.</span></span> <span data-ttu-id="a5dd9-118">Per un elenco di pacchetti che non vengono più pubblicati, vedere [rimuovere i riferimenti ai pacchetti obsoleti](xref:migration/22-to-30#remove-obsolete-package-references).</span><span class="sxs-lookup"><span data-stu-id="a5dd9-118">For a list of packages no longer being published, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="a5dd9-119">A partire da .NET Core 3,0, i progetti `Microsoft.NET.Sdk.Web` che usano MSBuild SDK fanno riferimento in modo implicito al Framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-119">As of .NET Core 3.0, projects using the `Microsoft.NET.Sdk.Web` MSBuild SDK implicitly reference the shared framework.</span></span> <span data-ttu-id="a5dd9-120">I progetti che `Microsoft.NET.Sdk` usano `Microsoft.NET.Sdk.Razor` l'SDK o devono fare riferimento a ASP.NET Core per usare ASP.NET Core API nel Framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-120">Projects using the `Microsoft.NET.Sdk` or `Microsoft.NET.Sdk.Razor` SDK must reference ASP.NET Core to use ASP.NET Core APIs in the shared framework.</span></span>

<span data-ttu-id="a5dd9-121">Per fare riferimento ASP.NET Core, aggiungere il `<FrameworkReference>` seguente elemento al file di progetto:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-121">To reference ASP.NET Core, add the following `<FrameworkReference>` element to your project file:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

<span data-ttu-id="a5dd9-122">Il riferimento ASP.NET Core in questo modo è supportato solo per i progetti destinati a .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-122">Referencing ASP.NET Core in this manner is only supported for projects targeting .NET Core 3.x.</span></span>

## <a name="include-blazor-extensibility"></a><span data-ttu-id="a5dd9-123">Includi estendibilità Blazer</span><span class="sxs-lookup"><span data-stu-id="a5dd9-123">Include Blazor extensibility</span></span>

<span data-ttu-id="a5dd9-124">Blazer supporta i [modelli di hosting](xref:blazor/hosting-models)webassembly (WASM) e server.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-124">Blazor supports WebAssembly (WASM) and Server [hosting models](xref:blazor/hosting-models).</span></span> <span data-ttu-id="a5dd9-125">A meno che non esista un motivo specifico per non farlo, una libreria di [componenti Razor](xref:blazor/components) deve supportare entrambi i modelli di hosting.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-125">Unless there's a specific reason not to, a [Razor components](xref:blazor/components) library should support both hosting models.</span></span> <span data-ttu-id="a5dd9-126">Una libreria di componenti Razor deve usare [Microsoft. NET. Sdk. Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="a5dd9-126">A Razor components library must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

### <a name="support-both-hosting-models"></a><span data-ttu-id="a5dd9-127">Supportare entrambi i modelli di hosting</span><span class="sxs-lookup"><span data-stu-id="a5dd9-127">Support both hosting models</span></span>

<span data-ttu-id="a5dd9-128">Per supportare l'utilizzo di componenti Razor da entrambi i progetti [Blazer server](xref:blazor/hosting-models#blazor-server) e [Blazer WASM](xref:blazor/hosting-models#blazor-webassembly) , usare le istruzioni seguenti per l'editor.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-128">To support Razor component consumption from both [Blazor Server](xref:blazor/hosting-models#blazor-server) and [Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) projects, use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a5dd9-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5dd9-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a5dd9-130">Usare il modello di progetto **libreria di classi Razor** .</span><span class="sxs-lookup"><span data-stu-id="a5dd9-130">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="a5dd9-131">La casella di controllo **pagine e visualizzazioni del supporto** del modello deve essere deselezionata.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-131">The template's **Support pages and views** checkbox should be deselected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a5dd9-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a5dd9-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a5dd9-133">Eseguire il comando seguente nel terminale integrato:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-133">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a5dd9-134">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="a5dd9-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a5dd9-135">Usare il modello di progetto **libreria di classi Razor** .</span><span class="sxs-lookup"><span data-stu-id="a5dd9-135">Use the **Razor Class Library** project template.</span></span>

---

<span data-ttu-id="a5dd9-136">Il progetto generato dal modello esegue le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-136">The project generated from the template does the following things:</span></span>

* <span data-ttu-id="a5dd9-137">Destinazioni .NET Standard 2,0.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-137">Targets .NET Standard 2.0.</span></span>
* <span data-ttu-id="a5dd9-138">Imposta la proprietà `RazorLangVersion` su `3.0`.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-138">Sets the `RazorLangVersion` property to `3.0`.</span></span> <span data-ttu-id="a5dd9-139">`3.0`è il valore predefinito per .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-139">`3.0` is the default value for .NET Core 3.x.</span></span>
* <span data-ttu-id="a5dd9-140">Aggiunge i riferimenti ai pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-140">Adds the following package references:</span></span>
  * [<span data-ttu-id="a5dd9-141">Microsoft. AspNetCore. Components</span><span class="sxs-lookup"><span data-stu-id="a5dd9-141">Microsoft.AspNetCore.Components</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [<span data-ttu-id="a5dd9-142">Microsoft. AspNetCore. Components. Web</span><span class="sxs-lookup"><span data-stu-id="a5dd9-142">Microsoft.AspNetCore.Components.Web</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

<span data-ttu-id="a5dd9-143">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-143">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a><span data-ttu-id="a5dd9-144">Supporto di un modello di hosting specifico</span><span class="sxs-lookup"><span data-stu-id="a5dd9-144">Support a specific hosting model</span></span>

<span data-ttu-id="a5dd9-145">È molto meno comune supportare un singolo modello di hosting blazer.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-145">It's far less common to support a single Blazor hosting model.</span></span> <span data-ttu-id="a5dd9-146">Ad esempio, per supportare il consumo di componenti Razor solo da progetti [Server Blazer](xref:blazor/hosting-models#blazor-server) :</span><span class="sxs-lookup"><span data-stu-id="a5dd9-146">As an example, to support Razor component consumption from [Blazor Server](xref:blazor/hosting-models#blazor-server) projects only:</span></span>

* <span data-ttu-id="a5dd9-147">Destinazione .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-147">Target .NET Core 3.x.</span></span>
* <span data-ttu-id="a5dd9-148">Aggiungere un `<FrameworkReference>` elemento per il Framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-148">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="a5dd9-149">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-149">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

<span data-ttu-id="a5dd9-150">Per altre informazioni sulle librerie contenenti componenti Razor, vedere [ASP.NET Core librerie di classi di componenti Razor](xref:blazor/class-libraries).</span><span class="sxs-lookup"><span data-stu-id="a5dd9-150">For more information on libraries containing Razor components, see [ASP.NET Core Razor components class libraries](xref:blazor/class-libraries).</span></span>

## <a name="include-mvc-extensibility"></a><span data-ttu-id="a5dd9-151">Includi estendibilità MVC</span><span class="sxs-lookup"><span data-stu-id="a5dd9-151">Include MVC extensibility</span></span>

<span data-ttu-id="a5dd9-152">Questa sezione descrive le raccomandazioni per le librerie che includono:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-152">This section outlines recommendations for libraries that include:</span></span>

* [<span data-ttu-id="a5dd9-153">Visualizzazioni Razor o Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a5dd9-153">Razor views or Razor Pages</span></span>](#razor-views-or-razor-pages)
* [<span data-ttu-id="a5dd9-154">Helper tag</span><span class="sxs-lookup"><span data-stu-id="a5dd9-154">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="a5dd9-155">Componenti di visualizzazione</span><span class="sxs-lookup"><span data-stu-id="a5dd9-155">View components</span></span>](#view-components)

<span data-ttu-id="a5dd9-156">Questa sezione non illustra la funzionalità multitargeting per supportare più versioni di MVC.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-156">This section doesn't discuss multi-targeting to support multiple versions of MVC.</span></span> <span data-ttu-id="a5dd9-157">Per informazioni sul supporto di più versioni di ASP.NET Core, vedere [supportare più versioni di ASP.NET Core](#support-multiple-aspnet-core-versions).</span><span class="sxs-lookup"><span data-stu-id="a5dd9-157">For guidance on supporting multiple ASP.NET Core versions, see [Support multiple ASP.NET Core versions](#support-multiple-aspnet-core-versions).</span></span>

### <a name="razor-views-or-razor-pages"></a><span data-ttu-id="a5dd9-158">Visualizzazioni Razor o Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a5dd9-158">Razor views or Razor Pages</span></span>

<span data-ttu-id="a5dd9-159">Un progetto che include [visualizzazioni Razor](xref:mvc/views/overview) o [Razor Pages](xref:razor-pages/index) deve usare [Microsoft. NET. Sdk. Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="a5dd9-159">A project that includes [Razor views](xref:mvc/views/overview) or [Razor Pages](xref:razor-pages/index) must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

<span data-ttu-id="a5dd9-160">Se il progetto è destinato a .NET Core 3. x, è necessario:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-160">If the project targets .NET Core 3.x, it requires:</span></span>

* <span data-ttu-id="a5dd9-161">Proprietà `AddRazorSupportForMvc` di MSBuild impostata su `true`.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-161">An `AddRazorSupportForMvc` MSBuild property set to `true`.</span></span>
* <span data-ttu-id="a5dd9-162">`<FrameworkReference>` Elemento per il Framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-162">A `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="a5dd9-163">Il modello di progetto **libreria di classi Razor** soddisfa i requisiti precedenti per i progetti destinati a .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-163">The **Razor Class Library** project template satisfies the preceding requirements for projects targeting .NET Core 3.x.</span></span> <span data-ttu-id="a5dd9-164">Usare le istruzioni seguenti per l'editor.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-164">Use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a5dd9-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5dd9-165">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a5dd9-166">Usare il modello di progetto **libreria di classi Razor** .</span><span class="sxs-lookup"><span data-stu-id="a5dd9-166">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="a5dd9-167">È necessario selezionare la casella di controllo **pagine e visualizzazioni del supporto** del modello.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-167">The template's **Support pages and views** checkbox should be selected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a5dd9-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a5dd9-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a5dd9-169">Eseguire il comando seguente nel terminale integrato:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-169">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a5dd9-170">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="a5dd9-170">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a5dd9-171">Al momento non è disponibile alcun supporto per i modelli di progetto.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-171">No project template support at this time.</span></span>

---

<span data-ttu-id="a5dd9-172">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-172">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

<span data-ttu-id="a5dd9-173">Se il progetto è destinato .NET Standard al contrario, è necessario un riferimento al pacchetto [Microsoft. AspNetCore. Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) .</span><span class="sxs-lookup"><span data-stu-id="a5dd9-173">If the project targets .NET Standard instead, a [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) package reference is required.</span></span> <span data-ttu-id="a5dd9-174">Il `Microsoft.AspNetCore.Mvc` pacchetto è stato spostato nel Framework condiviso in ASP.NET Core 3,0 e pertanto non è più pubblicato.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-174">The `Microsoft.AspNetCore.Mvc` package moved into the shared framework in ASP.NET Core 3.0 and is therefore no longer published.</span></span> <span data-ttu-id="a5dd9-175">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-175">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a><span data-ttu-id="a5dd9-176">Helper tag</span><span class="sxs-lookup"><span data-stu-id="a5dd9-176">Tag Helpers</span></span>

<span data-ttu-id="a5dd9-177">Un progetto che include gli [Helper Tag](xref:mvc/views/tag-helpers/intro) deve usare l' `Microsoft.NET.Sdk` SDK.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-177">A project that includes [Tag Helpers](xref:mvc/views/tag-helpers/intro) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="a5dd9-178">Se la destinazione è .NET Core 3. x, aggiungere `<FrameworkReference>` un elemento per il Framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-178">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="a5dd9-179">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-179">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="a5dd9-180">Se la destinazione .NET Standard (per supportare versioni precedenti a ASP.NET Core 3. x), aggiungere un riferimento al pacchetto a [Microsoft. AspNetCore. MvcRazor.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span><span class="sxs-lookup"><span data-stu-id="a5dd9-180">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span></span> <span data-ttu-id="a5dd9-181">Il `Microsoft.AspNetCore.Mvc.Razor` pacchetto è stato spostato nel Framework condiviso e pertanto non è più pubblicato.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-181">The `Microsoft.AspNetCore.Mvc.Razor` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="a5dd9-182">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-182">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a><span data-ttu-id="a5dd9-183">Componenti di visualizzazione</span><span class="sxs-lookup"><span data-stu-id="a5dd9-183">View components</span></span>

<span data-ttu-id="a5dd9-184">Un progetto che include [componenti di visualizzazione](xref:mvc/views/view-components) deve usare `Microsoft.NET.Sdk` l'SDK.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-184">A project that includes [View components](xref:mvc/views/view-components) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="a5dd9-185">Se la destinazione è .NET Core 3. x, aggiungere `<FrameworkReference>` un elemento per il Framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-185">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="a5dd9-186">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-186">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="a5dd9-187">Se la destinazione .NET Standard (per supportare versioni precedenti a ASP.NET Core 3. x), aggiungere un riferimento al pacchetto a [Microsoft. AspNetCore. Mvc. ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span><span class="sxs-lookup"><span data-stu-id="a5dd9-187">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span></span> <span data-ttu-id="a5dd9-188">Il `Microsoft.AspNetCore.Mvc.ViewFeatures` pacchetto è stato spostato nel Framework condiviso e pertanto non è più pubblicato.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-188">The `Microsoft.AspNetCore.Mvc.ViewFeatures` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="a5dd9-189">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-189">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a><span data-ttu-id="a5dd9-190">Supporto di più versioni di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a5dd9-190">Support multiple ASP.NET Core versions</span></span>

<span data-ttu-id="a5dd9-191">Il multitargeting è necessario per creare una libreria che supporti più varianti di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-191">Multi-targeting is required to author a library that supports multiple variants of ASP.NET Core.</span></span> <span data-ttu-id="a5dd9-192">Si consideri uno scenario in cui una libreria helper tag deve supportare le varianti di ASP.NET Core seguenti:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-192">Consider a scenario in which a Tag Helpers library must support the following ASP.NET Core variants:</span></span>

* <span data-ttu-id="a5dd9-193">ASP.NET Core 2,1 targeting .NET Framework 4.6.1</span><span class="sxs-lookup"><span data-stu-id="a5dd9-193">ASP.NET Core 2.1 targeting .NET Framework 4.6.1</span></span>
* <span data-ttu-id="a5dd9-194">ASP.NET Core 2. x destinato a .NET Core 2. x</span><span class="sxs-lookup"><span data-stu-id="a5dd9-194">ASP.NET Core 2.x targeting .NET Core 2.x</span></span>
* <span data-ttu-id="a5dd9-195">ASP.NET Core 3. x destinato a .NET Core 3. x</span><span class="sxs-lookup"><span data-stu-id="a5dd9-195">ASP.NET Core 3.x targeting .NET Core 3.x</span></span>

<span data-ttu-id="a5dd9-196">Il file di progetto seguente supporta queste varianti tramite `TargetFrameworks` la proprietà:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-196">The following project file supports these variants via the `TargetFrameworks` property:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

<span data-ttu-id="a5dd9-197">Con il file di progetto precedente:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-197">With the preceding project file:</span></span>

* <span data-ttu-id="a5dd9-198">Il `Markdig` pacchetto viene aggiunto a tutti i consumer.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-198">The `Markdig` package is added for all consumers.</span></span>
* <span data-ttu-id="a5dd9-199">Riferimento a [Microsoft. AspNetCore. Mvc.Razor ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor)</span><span class="sxs-lookup"><span data-stu-id="a5dd9-199">A reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor)</span></span> <span data-ttu-id="a5dd9-200">viene aggiunto per i consumer destinati .NET Framework 4.6.1 o versione successiva o .NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-200">is added for consumers targeting .NET Framework 4.6.1 or later or .NET Core 2.x.</span></span> <span data-ttu-id="a5dd9-201">La versione 2.1.0 del pacchetto funziona con ASP.NET Core 2,2, a causa della compatibilità con le versioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-201">Version 2.1.0 of the package works with ASP.NET Core 2.2 because of backwards compatibility.</span></span>
* <span data-ttu-id="a5dd9-202">Al Framework condiviso viene fatto riferimento per i consumer destinati a .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-202">The shared framework is referenced for consumers targeting .NET Core 3.x.</span></span> <span data-ttu-id="a5dd9-203">Il `Microsoft.AspNetCore.Mvc.Razor` pacchetto è incluso nel Framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-203">The `Microsoft.AspNetCore.Mvc.Razor` package is included in the shared framework.</span></span>

<span data-ttu-id="a5dd9-204">In alternativa, è possibile indirizzare .NET Standard 2,0 anziché indirizzare sia a .NET Core 2,1 sia a .NET Framework 4.6.1:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-204">Alternatively, .NET Standard 2.0 could be targeted instead of targeting both .NET Core 2.1 and .NET Framework 4.6.1:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

<span data-ttu-id="a5dd9-205">Con il file di progetto precedente, sono presenti le avvertenze seguenti:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-205">With the preceding project file, the following caveats exist:</span></span>

* <span data-ttu-id="a5dd9-206">Poiché la libreria contiene solo Helper tag, è più semplice usare come destinazione le piattaforme specifiche in cui viene eseguito ASP.NET Core: .NET Core e .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-206">Since the library only contains Tag Helpers, it's more straightforward to target the specific platforms on which ASP.NET Core runs: .NET Core and .NET Framework.</span></span> <span data-ttu-id="a5dd9-207">Gli helper tag non possono essere usati da altri Framework di destinazione compatibili con .NET Standard 2,0, ad esempio Unity, UWP e Novell.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-207">Tag Helpers can't be used by other .NET Standard 2.0-compliant target frameworks such as Unity, UWP, and Xamarin.</span></span>
* <span data-ttu-id="a5dd9-208">L'uso di .NET Standard 2.0 da .NET Framework presenta alcuni problemi che sono stati risolti in .NET Framework 4.7.2.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-208">Using .NET Standard 2.0 from .NET Framework has some issues that were addressed in .NET Framework 4.7.2.</span></span> <span data-ttu-id="a5dd9-209">È possibile migliorare l'esperienza per gli utenti che usano .NET Framework 4.6.1 tramite 4.7.1 impostando come destinazione .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-209">You can improve the experience for consumers using .NET Framework 4.6.1 through 4.7.1 by targeting .NET Framework 4.6.1.</span></span>

<span data-ttu-id="a5dd9-210">Se la libreria deve chiamare le API specifiche della piattaforma, destinare implementazioni .NET specifiche anziché .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-210">If your library needs to call platform-specific APIs, target specific .NET implementations instead of .NET Standard.</span></span> <span data-ttu-id="a5dd9-211">Per ulteriori informazioni, vedere [multitargeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span><span class="sxs-lookup"><span data-stu-id="a5dd9-211">For more information, see [Multi-targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span></span>

## <a name="use-an-api-that-hasnt-changed"></a><span data-ttu-id="a5dd9-212">Usare un'API che non è stata modificata</span><span class="sxs-lookup"><span data-stu-id="a5dd9-212">Use an API that hasn't changed</span></span>

<span data-ttu-id="a5dd9-213">Si immagini uno scenario in cui si sta aggiornando una libreria middleware da .NET Core 2,2 a 3,0.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-213">Imagine a scenario in which you're upgrading a middleware library from .NET Core 2.2 to 3.0.</span></span> <span data-ttu-id="a5dd9-214">Le API middleware ASP.NET Core utilizzate nella libreria non sono state modificate tra ASP.NET Core 2,2 e 3,0.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-214">The ASP.NET Core middleware APIs being used in the library haven't changed between ASP.NET Core 2.2 and 3.0.</span></span> <span data-ttu-id="a5dd9-215">Per continuare a supportare la libreria middleware in .NET Core 3,0, seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-215">To continue supporting the middleware library in .NET Core 3.0, take the following steps:</span></span>

* <span data-ttu-id="a5dd9-216">Seguire le [indicazioni della libreria standard](/dotnet/standard/library-guidance/).</span><span class="sxs-lookup"><span data-stu-id="a5dd9-216">Follow the [standard library guidance](/dotnet/standard/library-guidance/).</span></span>
* <span data-ttu-id="a5dd9-217">Aggiungere un riferimento al pacchetto per ogni pacchetto NuGet dell'API se l'assembly corrispondente non esiste nel Framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-217">Add a package reference for each API's NuGet package if the corresponding assembly doesn't exist in the shared framework.</span></span>

## <a name="use-an-api-that-changed"></a><span data-ttu-id="a5dd9-218">Usare un'API modificata</span><span class="sxs-lookup"><span data-stu-id="a5dd9-218">Use an API that changed</span></span>

<span data-ttu-id="a5dd9-219">Si immagini uno scenario in cui si sta aggiornando una libreria da .NET Core 2,2 a .NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-219">Imagine a scenario in which you're upgrading a library from .NET Core 2.2 to .NET Core 3.0.</span></span> <span data-ttu-id="a5dd9-220">Un'API ASP.NET Core utilizzata nella libreria presenta una [modifica sostanziale](/dotnet/core/compatibility/breaking-changes) in ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-220">An ASP.NET Core API being used in the library has a [breaking change](/dotnet/core/compatibility/breaking-changes) in ASP.NET Core 3.0.</span></span> <span data-ttu-id="a5dd9-221">Valutare se la libreria può essere riscritta in modo da non usare l'API interruppe in tutte le versioni.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-221">Consider whether the library can be rewritten to not use the broken API in all versions.</span></span>

<span data-ttu-id="a5dd9-222">Se è possibile riscrivere la libreria, effettuare questa operazione e continuare a usare un Framework di destinazione precedente (ad esempio, .NET Standard 2,0 o .NET Framework 4.6.1) con i riferimenti al pacchetto.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-222">If you can rewrite the library, do so and continue to target an earlier target framework (for example, .NET Standard 2.0 or .NET Framework 4.6.1) with package references.</span></span>

<span data-ttu-id="a5dd9-223">Se non è possibile riscrivere la libreria, seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-223">If you can't rewrite the library, take the following steps:</span></span>

* <span data-ttu-id="a5dd9-224">Aggiungere una destinazione per .NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-224">Add a target for .NET Core 3.0.</span></span>
* <span data-ttu-id="a5dd9-225">Aggiungere un `<FrameworkReference>` elemento per il Framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-225">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="a5dd9-226">Usare la [direttiva per il preprocessore #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) con il simbolo del Framework di destinazione appropriato per compilare il codice in modo condizionale.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-226">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="a5dd9-227">Le letture e le scritture sincrone sui flussi di richiesta e risposta HTTP, ad esempio, sono disabilitate per impostazione predefinita a partire da ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-227">For example, synchronous reads and writes on HTTP request and response streams are disabled by default as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="a5dd9-228">ASP.NET Core 2,2 supporta il comportamento sincrono per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-228">ASP.NET Core 2.2 supports the synchronous behavior by default.</span></span> <span data-ttu-id="a5dd9-229">Si consideri una libreria middleware in cui le operazioni di lettura e scrittura sincrone devono essere abilitate quando si verifica l'I/O.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-229">Consider a middleware library in which synchronous reads and writes should be enabled where I/O is occurring.</span></span> <span data-ttu-id="a5dd9-230">La libreria deve racchiudere il codice per abilitare le funzionalità sincrone nella direttiva per il preprocessore appropriata.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-230">The library should enclose the code to enable synchronous features in the appropriate preprocessor directive.</span></span> <span data-ttu-id="a5dd9-231">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-231">For example:</span></span>

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a><span data-ttu-id="a5dd9-232">Usare un'API introdotta in 3,0</span><span class="sxs-lookup"><span data-stu-id="a5dd9-232">Use an API introduced in 3.0</span></span>

<span data-ttu-id="a5dd9-233">Si supponga di voler usare un'API ASP.NET Core introdotta in ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-233">Imagine that you want to use an ASP.NET Core API that was introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="a5dd9-234">Prendere in considerazione le domande seguenti:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-234">Consider the following questions:</span></span>

1. <span data-ttu-id="a5dd9-235">La libreria richiede funzionalmente la nuova API?</span><span class="sxs-lookup"><span data-stu-id="a5dd9-235">Does the library functionally require the new API?</span></span>
1. <span data-ttu-id="a5dd9-236">La libreria può implementare questa funzionalità in modo diverso?</span><span class="sxs-lookup"><span data-stu-id="a5dd9-236">Can the library implement this feature in a different way?</span></span>

<span data-ttu-id="a5dd9-237">Se la libreria richiede a livello funzionale l'API e non è possibile implementarla in modo inattivo:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-237">If the library functionally requires the API and there's no way to implement it down-level:</span></span>

* <span data-ttu-id="a5dd9-238">Solo .NET Core 3. x di destinazione.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-238">Target .NET Core 3.x only.</span></span>
* <span data-ttu-id="a5dd9-239">Aggiungere un `<FrameworkReference>` elemento per il Framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-239">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="a5dd9-240">Se la libreria può implementare la funzionalità in modo diverso:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-240">If the library can implement the feature in a different way:</span></span>

* <span data-ttu-id="a5dd9-241">Aggiungere .NET Core 3. x come Framework di destinazione.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-241">Add .NET Core 3.x as a target framework.</span></span>
* <span data-ttu-id="a5dd9-242">Aggiungere un `<FrameworkReference>` elemento per il Framework condiviso.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-242">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="a5dd9-243">Usare la [direttiva per il preprocessore #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) con il simbolo del Framework di destinazione appropriato per compilare il codice in modo condizionale.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-243">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="a5dd9-244">Ad esempio, l'helper tag seguente usa l' <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interfaccia introdotta in ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-244">For example, the following Tag Helper uses the <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interface introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="a5dd9-245">I consumer destinati a .NET Core 3,0 eseguono il percorso del codice `NETCOREAPP3_0` definito dal simbolo del Framework di destinazione.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-245">Consumers targeting .NET Core 3.0 execute the code path defined by the `NETCOREAPP3_0` target framework symbol.</span></span> <span data-ttu-id="a5dd9-246">Il tipo di parametro del costruttore dell'helper Tag passa <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> a per i consumer di .net core 2,1 e .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-246">The Tag Helper's constructor parameter type changes to <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> for .NET Core 2.1 and .NET Framework 4.6.1 consumers.</span></span> <span data-ttu-id="a5dd9-247">Questa modifica era necessaria perché ASP.NET Core 3,0 è `IHostingEnvironment` stata contrassegnata come `IWebHostEnvironment` obsoleta e consigliata come sostituzione.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-247">This change was necessary because ASP.NET Core 3.0 marked `IHostingEnvironment` as obsolete and recommended `IWebHostEnvironment` as the replacement.</span></span>

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

<span data-ttu-id="a5dd9-248">Il file di progetto multitargeting seguente supporta questo scenario di helper Tag:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-248">The following multi-targeted project file supports this Tag Helper scenario:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a><span data-ttu-id="a5dd9-249">Usare un'API rimossa dal Framework condiviso</span><span class="sxs-lookup"><span data-stu-id="a5dd9-249">Use an API removed from the shared framework</span></span>

<span data-ttu-id="a5dd9-250">Per usare un assembly ASP.NET Core rimosso dal Framework condiviso, aggiungere il riferimento al pacchetto appropriato.</span><span class="sxs-lookup"><span data-stu-id="a5dd9-250">To use an ASP.NET Core assembly that was removed from the shared framework, add the appropriate package reference.</span></span> <span data-ttu-id="a5dd9-251">Per un elenco di pacchetti rimossi dal Framework condiviso in ASP.NET Core 3,0, vedere [rimuovere i riferimenti ai pacchetti obsoleti](xref:migration/22-to-30#remove-obsolete-package-references).</span><span class="sxs-lookup"><span data-stu-id="a5dd9-251">For a list of packages removed from the shared framework in ASP.NET Core 3.0, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="a5dd9-252">Ad esempio, per aggiungere il client dell'API Web:</span><span class="sxs-lookup"><span data-stu-id="a5dd9-252">For example, to add the web API client:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="a5dd9-253">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a5dd9-253">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* <xref:blazor/class-libraries>
* [<span data-ttu-id="a5dd9-254">Supporto per le implementazioni di .NET</span><span class="sxs-lookup"><span data-stu-id="a5dd9-254">.NET implementation support</span></span>](/dotnet/standard/net-standard#net-implementation-support)
* [<span data-ttu-id="a5dd9-255">Criteri di supporto .NET</span><span class="sxs-lookup"><span data-stu-id="a5dd9-255">.NET support policies</span></span>](https://dotnet.microsoft.com/platform/support/policy)
