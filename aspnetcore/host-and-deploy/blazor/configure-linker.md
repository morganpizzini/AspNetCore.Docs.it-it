---
title: Configurare il linker per ASP.NET CoreBlazor
author: guardrex
description: Scopri come controllare il linker IL (Intermediate Blazor Language) durante la compilazione di un'app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: 109da5ef400c3b9d64ccf3ceb33a5387ea6b5618
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218661"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="a5fcf-103">Configurare il linker per ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="a5fcf-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="a5fcf-104">Di [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a5fcf-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="a5fcf-105">Blazor WebAssembly esegue il collegamento [in linguaggio intermedio (IL)](/dotnet/standard/managed-code#intermediate-language--execution) durante una compilazione per tagliare il linguaggio intermedio dagli assembly di output dell'app.</span><span class="sxs-lookup"><span data-stu-id="a5fcf-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="a5fcf-106">Il linker è disabilitato durante la compilazione nella configurazione di debug.</span><span class="sxs-lookup"><span data-stu-id="a5fcf-106">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="a5fcf-107">Le app devono essere compilate nella configurazione di rilascio per abilitare il linker.</span><span class="sxs-lookup"><span data-stu-id="a5fcf-107">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="a5fcf-108">Si consiglia di compilare in Release quando si distribuiscono le applicazioni WebAssembly Blazor.</span><span class="sxs-lookup"><span data-stu-id="a5fcf-108">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="a5fcf-109">Il collegamento di un'app consente di ottimizzare le dimensioni, ma può avere effetti negativi.</span><span class="sxs-lookup"><span data-stu-id="a5fcf-109">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="a5fcf-110">Le app che usano la reflection o funzionalità dinamiche correlate potrebbero interrompersi quando vengono rifinite perché il linker non conosce questo comportamento dinamico e non è in grado di determinare in generale quali tipi sono necessari per la reflection in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="a5fcf-110">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="a5fcf-111">Per tagliare tali app, il linker deve essere informato su tutti i tipi richiesti dalla reflection nel codice e nei pacchetti o framework da cui dipende l'app.</span><span class="sxs-lookup"><span data-stu-id="a5fcf-111">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="a5fcf-112">Per assicurarti che l'app tagliata funzioni correttamente una volta distribuita, è importante testare frequentemente le build di rilascio dell'app durante lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="a5fcf-112">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="a5fcf-113">Il collegamento per le app Blazor può essere configurato usando queste funzionalità MSBuild:Linking for Blazor apps can be configured using these MSBuild features:</span><span class="sxs-lookup"><span data-stu-id="a5fcf-113">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="a5fcf-114">Configurare il collegamento a livello globale con una [proprietà MSBuild](#control-linking-with-an-msbuild-property).</span><span class="sxs-lookup"><span data-stu-id="a5fcf-114">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="a5fcf-115">Controllare il collegamento in base all'assembly con un file di [configurazione.](#control-linking-with-a-configuration-file)</span><span class="sxs-lookup"><span data-stu-id="a5fcf-115">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="a5fcf-116">Controllare il collegamento con una proprietà MSBuildControl linking with an MSBuild property</span><span class="sxs-lookup"><span data-stu-id="a5fcf-116">Control linking with an MSBuild property</span></span>

<span data-ttu-id="a5fcf-117">Il collegamento è abilitato quando `Release` viene compilata un'app nella configurazione.</span><span class="sxs-lookup"><span data-stu-id="a5fcf-117">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="a5fcf-118">Per modificare questa `BlazorWebAssemblyEnableLinking` impostazione, configurare la proprietà MSBuild nel file di progetto:To change this, configure the MSBuild property in the project file:</span><span class="sxs-lookup"><span data-stu-id="a5fcf-118">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="a5fcf-119">Controllare il collegamento con un file di configurazione</span><span class="sxs-lookup"><span data-stu-id="a5fcf-119">Control linking with a configuration file</span></span>

<span data-ttu-id="a5fcf-120">Controllare il collegamento per ogni singolo assembly usando un file di configurazione XML e specificando il file come un elemento MSBuild nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="a5fcf-120">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="a5fcf-121">*LinkerConfig.xml*:</span><span class="sxs-lookup"><span data-stu-id="a5fcf-121">*LinkerConfig.xml*:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

<span data-ttu-id="a5fcf-122">Per ulteriori informazioni, vedere Esempi di [file xml di collegamento (repository GitHub mono/linker)](https://github.com/mono/linker#link-xml-file-examples).</span><span class="sxs-lookup"><span data-stu-id="a5fcf-122">For more information, see [Link xml file examples (mono/linker GitHub repository)](https://github.com/mono/linker#link-xml-file-examples).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="a5fcf-123">Aggiungere un file di configurazione del linker XML a una libreriaAdd an XML linker configuration file to a library</span><span class="sxs-lookup"><span data-stu-id="a5fcf-123">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="a5fcf-124">Per configurare il linker per una libreria specifica, aggiungere un file di configurazione del linker XML nella libreria come risorsa incorporata.</span><span class="sxs-lookup"><span data-stu-id="a5fcf-124">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="a5fcf-125">La risorsa incorporata deve avere lo stesso nome dell'assembly.</span><span class="sxs-lookup"><span data-stu-id="a5fcf-125">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="a5fcf-126">Nell'esempio seguente, il file *LinkerConfig.xml* viene specificato come risorsa incorporata con lo stesso nome dell'assembly della libreria:</span><span class="sxs-lookup"><span data-stu-id="a5fcf-126">In the following example, the *LinkerConfig.xml* file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="a5fcf-127">Configurare il linker per l'internazionalizzazione</span><span class="sxs-lookup"><span data-stu-id="a5fcf-127">Configure the linker for internationalization</span></span>

<span data-ttu-id="a5fcf-128">Per impostazione predefinita, la configurazione del linker di Blazor per le app Blazor WebAssembly elimina le informazioni sull'internazionalizzazione, ad eccezione delle impostazioni locali richieste in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="a5fcf-128">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="a5fcf-129">La rimozione di questi assembly riduce al minimo le dimensioni dell'app.</span><span class="sxs-lookup"><span data-stu-id="a5fcf-129">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="a5fcf-130">Per controllare quali assembly I18N vengono `<MonoLinkerI18NAssemblies>` mantenuti, impostare la proprietà MSBuild nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="a5fcf-130">To control which I18N assemblies are retained, set the `<MonoLinkerI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="a5fcf-131">Valore regione</span><span class="sxs-lookup"><span data-stu-id="a5fcf-131">Region Value</span></span>     | <span data-ttu-id="a5fcf-132">Assemblaggio area mono-area</span><span class="sxs-lookup"><span data-stu-id="a5fcf-132">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="a5fcf-133">Tutti gli assiemi inclusi</span><span class="sxs-lookup"><span data-stu-id="a5fcf-133">All assemblies included</span></span> |
| `cjk`            | <span data-ttu-id="a5fcf-134">*I18N.CJK.dll*</span><span class="sxs-lookup"><span data-stu-id="a5fcf-134">*I18N.CJK.dll*</span></span>          |
| `mideast`        | <span data-ttu-id="a5fcf-135">*I18N.MidEast.dll*</span><span class="sxs-lookup"><span data-stu-id="a5fcf-135">*I18N.MidEast.dll*</span></span>      |
| <span data-ttu-id="a5fcf-136">`none` (impostazione predefinita)</span><span class="sxs-lookup"><span data-stu-id="a5fcf-136">`none` (default)</span></span> | <span data-ttu-id="a5fcf-137">nessuno</span><span class="sxs-lookup"><span data-stu-id="a5fcf-137">None</span></span>                    |
| `other`          | <span data-ttu-id="a5fcf-138">*I18N.Other.dll*</span><span class="sxs-lookup"><span data-stu-id="a5fcf-138">*I18N.Other.dll*</span></span>        |
| `rare`           | <span data-ttu-id="a5fcf-139">*I18N.Rare.dll*</span><span class="sxs-lookup"><span data-stu-id="a5fcf-139">*I18N.Rare.dll*</span></span>         |
| `west`           | <span data-ttu-id="a5fcf-140">*I18N.West.dll*</span><span class="sxs-lookup"><span data-stu-id="a5fcf-140">*I18N.West.dll*</span></span>         |

<span data-ttu-id="a5fcf-141">Utilizzare una virgola per separare `mideast,west`più valori (ad esempio, ).</span><span class="sxs-lookup"><span data-stu-id="a5fcf-141">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="a5fcf-142">Per ulteriori informazioni, vedere [I18N: Pnetlib Internationalization Framework Library (repository GitHub mono/mono)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="a5fcf-142">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>
