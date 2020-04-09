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
# <a name="configure-the-linker-for-aspnet-core-blazor"></a>Configurare il linker per ASP.NET Core Blazor

Di [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor WebAssembly esegue il collegamento [in linguaggio intermedio (IL)](/dotnet/standard/managed-code#intermediate-language--execution) durante una compilazione per tagliare il linguaggio intermedio dagli assembly di output dell'app. Il linker è disabilitato durante la compilazione nella configurazione di debug. Le app devono essere compilate nella configurazione di rilascio per abilitare il linker. Si consiglia di compilare in Release quando si distribuiscono le applicazioni WebAssembly Blazor. 

Il collegamento di un'app consente di ottimizzare le dimensioni, ma può avere effetti negativi. Le app che usano la reflection o funzionalità dinamiche correlate potrebbero interrompersi quando vengono rifinite perché il linker non conosce questo comportamento dinamico e non è in grado di determinare in generale quali tipi sono necessari per la reflection in fase di esecuzione. Per tagliare tali app, il linker deve essere informato su tutti i tipi richiesti dalla reflection nel codice e nei pacchetti o framework da cui dipende l'app. 

Per assicurarti che l'app tagliata funzioni correttamente una volta distribuita, è importante testare frequentemente le build di rilascio dell'app durante lo sviluppo.

Il collegamento per le app Blazor può essere configurato usando queste funzionalità MSBuild:Linking for Blazor apps can be configured using these MSBuild features:

* Configurare il collegamento a livello globale con una [proprietà MSBuild](#control-linking-with-an-msbuild-property).
* Controllare il collegamento in base all'assembly con un file di [configurazione.](#control-linking-with-a-configuration-file)

## <a name="control-linking-with-an-msbuild-property"></a>Controllare il collegamento con una proprietà MSBuildControl linking with an MSBuild property

Il collegamento è abilitato quando `Release` viene compilata un'app nella configurazione. Per modificare questa `BlazorWebAssemblyEnableLinking` impostazione, configurare la proprietà MSBuild nel file di progetto:To change this, configure the MSBuild property in the project file:

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>Controllare il collegamento con un file di configurazione

Controllare il collegamento per ogni singolo assembly usando un file di configurazione XML e specificando il file come un elemento MSBuild nel file di progetto:

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

*LinkerConfig.xml*:

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

Per ulteriori informazioni, vedere Esempi di [file xml di collegamento (repository GitHub mono/linker)](https://github.com/mono/linker#link-xml-file-examples).

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a>Aggiungere un file di configurazione del linker XML a una libreriaAdd an XML linker configuration file to a library

Per configurare il linker per una libreria specifica, aggiungere un file di configurazione del linker XML nella libreria come risorsa incorporata. La risorsa incorporata deve avere lo stesso nome dell'assembly.

Nell'esempio seguente, il file *LinkerConfig.xml* viene specificato come risorsa incorporata con lo stesso nome dell'assembly della libreria:

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a>Configurare il linker per l'internazionalizzazione

Per impostazione predefinita, la configurazione del linker di Blazor per le app Blazor WebAssembly elimina le informazioni sull'internazionalizzazione, ad eccezione delle impostazioni locali richieste in modo esplicito. La rimozione di questi assembly riduce al minimo le dimensioni dell'app.

Per controllare quali assembly I18N vengono `<MonoLinkerI18NAssemblies>` mantenuti, impostare la proprietà MSBuild nel file di progetto:

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| Valore regione     | Assemblaggio area mono-area    |
| ---------------- | ----------------------- |
| `all`            | Tutti gli assiemi inclusi |
| `cjk`            | *I18N.CJK.dll*          |
| `mideast`        | *I18N.MidEast.dll*      |
| `none` (impostazione predefinita) | nessuno                    |
| `other`          | *I18N.Other.dll*        |
| `rare`           | *I18N.Rare.dll*         |
| `west`           | *I18N.West.dll*         |

Utilizzare una virgola per separare `mideast,west`più valori (ad esempio, ).

Per ulteriori informazioni, vedere [I18N: Pnetlib Internationalization Framework Library (repository GitHub mono/mono)](https://github.com/mono/mono/tree/master/mcs/class/I18N).
