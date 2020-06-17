---
title: Configurare il linker per ASP.NET CoreBlazor
author: guardrex
description: Informazioni su come controllare il linker del linguaggio intermedio (IL) durante la compilazione di un' Blazor app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: fd8dc118b8ed9c9e7c0d43e67b14ef4affa09d48
ms.sourcegitcommit: b0062f29cba2e5c21b95cf89eaf435ba830d11a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/16/2020
ms.locfileid: "83608629"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="f0957-103">Configurare il linker per ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="f0957-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="f0957-104">Di [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f0957-104">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor<span data-ttu-id="f0957-105">Webassembly esegue il collegamento del [linguaggio intermedio (il)](/dotnet/standard/managed-code#intermediate-language--execution) durante una compilazione per eliminare il linguaggio intermedio non necessario dagli assembly di output dell'app.</span><span class="sxs-lookup"><span data-stu-id="f0957-105"> WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="f0957-106">Il linker è disabilitato durante la compilazione nella configurazione di debug.</span><span class="sxs-lookup"><span data-stu-id="f0957-106">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="f0957-107">Per abilitare il linker, le app devono essere compilate in configurazione versione.</span><span class="sxs-lookup"><span data-stu-id="f0957-107">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="f0957-108">Quando si distribuiscono le app webassembly, è consigliabile compilare in versione Blazor .</span><span class="sxs-lookup"><span data-stu-id="f0957-108">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="f0957-109">Il collegamento di un'app ottimizza le dimensioni, ma può avere effetti negativi.</span><span class="sxs-lookup"><span data-stu-id="f0957-109">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="f0957-110">Le app che usano la reflection o le funzionalità dinamiche correlate potrebbero interrompersi quando vengono tagliate perché il linker non è a conoscenza di questo comportamento dinamico e non può determinare in generale quali tipi sono necessari per la reflection in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="f0957-110">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="f0957-111">Per tagliare tali app, il linker deve essere informato sui tipi necessari per la reflection nel codice e nei pacchetti o Framework da cui dipende l'app.</span><span class="sxs-lookup"><span data-stu-id="f0957-111">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="f0957-112">Per assicurarsi che l'app tagliata funzioni correttamente una volta distribuita, è importante testare le build di rilascio dell'app spesso durante lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="f0957-112">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="f0957-113">Il collegamento per Blazor le app può essere configurato usando le funzionalità di MSBuild seguenti:</span><span class="sxs-lookup"><span data-stu-id="f0957-113">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="f0957-114">Configurare il collegamento a livello globale con una [proprietà di MSBuild](#control-linking-with-an-msbuild-property).</span><span class="sxs-lookup"><span data-stu-id="f0957-114">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="f0957-115">Controllo del collegamento per ogni assembly con un [file di configurazione](#control-linking-with-a-configuration-file).</span><span class="sxs-lookup"><span data-stu-id="f0957-115">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="f0957-116">Controllo del collegamento con una proprietà MSBuild</span><span class="sxs-lookup"><span data-stu-id="f0957-116">Control linking with an MSBuild property</span></span>

<span data-ttu-id="f0957-117">Il collegamento viene abilitato quando si compila un'app nella `Release` configurazione.</span><span class="sxs-lookup"><span data-stu-id="f0957-117">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="f0957-118">Per modificare questa configurazione, configurare la `BlazorWebAssemblyEnableLinking` Proprietà MSBuild nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="f0957-118">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="f0957-119">Controllare il collegamento con un file di configurazione</span><span class="sxs-lookup"><span data-stu-id="f0957-119">Control linking with a configuration file</span></span>

<span data-ttu-id="f0957-120">Controllare il collegamento per ogni singolo assembly usando un file di configurazione XML e specificando il file come un elemento MSBuild nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="f0957-120">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="f0957-121">*LinkerConfig.xml*:</span><span class="sxs-lookup"><span data-stu-id="f0957-121">*LinkerConfig.xml*:</span></span>

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

<span data-ttu-id="f0957-122">Per altre informazioni ed esempi, vedere [formati di dati (repository GitHub mono/linker)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span><span class="sxs-lookup"><span data-stu-id="f0957-122">For more information and examples, see [Data Formats (mono/linker GitHub repository)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="f0957-123">Aggiungere un file di configurazione del linker XML a una raccolta</span><span class="sxs-lookup"><span data-stu-id="f0957-123">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="f0957-124">Per configurare il linker per una libreria specifica, aggiungere un file di configurazione del linker XML nella libreria come risorsa incorporata.</span><span class="sxs-lookup"><span data-stu-id="f0957-124">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="f0957-125">La risorsa incorporata deve avere lo stesso nome dell'assembly.</span><span class="sxs-lookup"><span data-stu-id="f0957-125">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="f0957-126">Nell'esempio seguente il file di *LinkerConfig.xml* viene specificato come una risorsa incorporata con lo stesso nome dell'assembly della libreria:</span><span class="sxs-lookup"><span data-stu-id="f0957-126">In the following example, the *LinkerConfig.xml* file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="f0957-127">Configurare il linker per l'internazionalizzazione</span><span class="sxs-lookup"><span data-stu-id="f0957-127">Configure the linker for internationalization</span></span>

<span data-ttu-id="f0957-128">Per impostazione predefinita, Blazor la configurazione del linker per le Blazor app webassembly rimuove le informazioni di internazionalizzazione ad eccezione delle impostazioni locali richieste in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="f0957-128">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="f0957-129">La rimozione di questi assembly riduce al minimo le dimensioni dell'app.</span><span class="sxs-lookup"><span data-stu-id="f0957-129">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="f0957-130">Per controllare quali assembly I18N vengono conservati, impostare la `<BlazorWebAssemblyI18NAssemblies>` Proprietà MSBuild nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="f0957-130">To control which I18N assemblies are retained, set the `<BlazorWebAssemblyI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="f0957-131">Valore Region</span><span class="sxs-lookup"><span data-stu-id="f0957-131">Region Value</span></span>     | <span data-ttu-id="f0957-132">Assembly dell'area mono</span><span class="sxs-lookup"><span data-stu-id="f0957-132">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="f0957-133">Tutti gli assembly inclusi</span><span class="sxs-lookup"><span data-stu-id="f0957-133">All assemblies included</span></span> |
| `cjk`            | <span data-ttu-id="f0957-134">*I18N.CJK.dll*</span><span class="sxs-lookup"><span data-stu-id="f0957-134">*I18N.CJK.dll*</span></span>          |
| `mideast`        | <span data-ttu-id="f0957-135">*I18N.MidEast.dll*</span><span class="sxs-lookup"><span data-stu-id="f0957-135">*I18N.MidEast.dll*</span></span>      |
| <span data-ttu-id="f0957-136">`none` (impostazione predefinita)</span><span class="sxs-lookup"><span data-stu-id="f0957-136">`none` (default)</span></span> | <span data-ttu-id="f0957-137">nessuno</span><span class="sxs-lookup"><span data-stu-id="f0957-137">None</span></span>                    |
| `other`          | <span data-ttu-id="f0957-138">*I18N.Other.dll*</span><span class="sxs-lookup"><span data-stu-id="f0957-138">*I18N.Other.dll*</span></span>        |
| `rare`           | <span data-ttu-id="f0957-139">*I18N.Rare.dll*</span><span class="sxs-lookup"><span data-stu-id="f0957-139">*I18N.Rare.dll*</span></span>         |
| `west`           | <span data-ttu-id="f0957-140">*I18N.West.dll*</span><span class="sxs-lookup"><span data-stu-id="f0957-140">*I18N.West.dll*</span></span>         |

<span data-ttu-id="f0957-141">Usare una virgola per separare più valori (ad esempio, `mideast,west` ).</span><span class="sxs-lookup"><span data-stu-id="f0957-141">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="f0957-142">Per altre informazioni, vedere [i18n: Pnetlib internationalation Framework Library (repository GitHub mono/mono)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="f0957-142">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f0957-143">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="f0957-143">Additional resources</span></span>

* <xref:performance/blazor/webassembly-best-practices#intermediate-language-il-linking>
