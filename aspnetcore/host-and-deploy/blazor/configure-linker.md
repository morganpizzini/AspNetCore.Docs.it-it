---
<span data-ttu-id="8f38a-101">title: "configurare il linker per ASP.NET Core Blazor " autore: Descrizione: "informazioni su come controllare il linker linguaggio intermedio (il) durante la compilazione di un' Blazor app".</span><span class="sxs-lookup"><span data-stu-id="8f38a-101">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8f38a-102">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8f38a-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8f38a-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-103">'Blazor'</span></span>
- <span data-ttu-id="8f38a-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8f38a-104">'Identity'</span></span>
- <span data-ttu-id="8f38a-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8f38a-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="8f38a-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-106">'Razor'</span></span>
- <span data-ttu-id="8f38a-107">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="8f38a-107">'SignalR' uid:</span></span> 

---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="8f38a-108">Configurare il linker per ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="8f38a-108">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="8f38a-109">Di [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8f38a-109">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor<span data-ttu-id="8f38a-110">Webassembly esegue il collegamento del [linguaggio intermedio (il)](/dotnet/standard/managed-code#intermediate-language--execution) durante una compilazione per eliminare il linguaggio intermedio non necessario dagli assembly di output dell'app.</span><span class="sxs-lookup"><span data-stu-id="8f38a-110"> WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="8f38a-111">Il linker è disabilitato durante la compilazione nella configurazione di debug.</span><span class="sxs-lookup"><span data-stu-id="8f38a-111">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="8f38a-112">Per abilitare il linker, le app devono essere compilate in configurazione versione.</span><span class="sxs-lookup"><span data-stu-id="8f38a-112">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="8f38a-113">Quando si distribuiscono le app webassembly, è consigliabile compilare in versione Blazor .</span><span class="sxs-lookup"><span data-stu-id="8f38a-113">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="8f38a-114">Il collegamento di un'app ottimizza le dimensioni, ma può avere effetti negativi.</span><span class="sxs-lookup"><span data-stu-id="8f38a-114">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="8f38a-115">Le app che usano la reflection o le funzionalità dinamiche correlate potrebbero interrompersi quando vengono tagliate perché il linker non è a conoscenza di questo comportamento dinamico e non può determinare in generale quali tipi sono necessari per la reflection in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="8f38a-115">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="8f38a-116">Per tagliare tali app, il linker deve essere informato sui tipi necessari per la reflection nel codice e nei pacchetti o Framework da cui dipende l'app.</span><span class="sxs-lookup"><span data-stu-id="8f38a-116">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="8f38a-117">Per assicurarsi che l'app tagliata funzioni correttamente una volta distribuita, è importante testare le build di rilascio dell'app spesso durante lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="8f38a-117">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="8f38a-118">Il collegamento per Blazor le app può essere configurato usando le funzionalità di MSBuild seguenti:</span><span class="sxs-lookup"><span data-stu-id="8f38a-118">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="8f38a-119">Configurare il collegamento a livello globale con una [proprietà di MSBuild](#control-linking-with-an-msbuild-property).</span><span class="sxs-lookup"><span data-stu-id="8f38a-119">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="8f38a-120">Controllo del collegamento per ogni assembly con un [file di configurazione](#control-linking-with-a-configuration-file).</span><span class="sxs-lookup"><span data-stu-id="8f38a-120">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="8f38a-121">Controllo del collegamento con una proprietà MSBuild</span><span class="sxs-lookup"><span data-stu-id="8f38a-121">Control linking with an MSBuild property</span></span>

<span data-ttu-id="8f38a-122">Il collegamento viene abilitato quando si compila un'app nella `Release` configurazione.</span><span class="sxs-lookup"><span data-stu-id="8f38a-122">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="8f38a-123">Per modificare questa configurazione, configurare la `BlazorWebAssemblyEnableLinking` Proprietà MSBuild nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="8f38a-123">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="8f38a-124">Controllare il collegamento con un file di configurazione</span><span class="sxs-lookup"><span data-stu-id="8f38a-124">Control linking with a configuration file</span></span>

<span data-ttu-id="8f38a-125">Controllare il collegamento per ogni singolo assembly usando un file di configurazione XML e specificando il file come un elemento MSBuild nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="8f38a-125">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="8f38a-126">*LinkerConfig. XML*:</span><span class="sxs-lookup"><span data-stu-id="8f38a-126">*LinkerConfig.xml*:</span></span>

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

<span data-ttu-id="8f38a-127">Per altre informazioni ed esempi, vedere [formati di dati (repository GitHub mono/linker)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span><span class="sxs-lookup"><span data-stu-id="8f38a-127">For more information and examples, see [Data Formats (mono/linker GitHub repository)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="8f38a-128">Aggiungere un file di configurazione del linker XML a una raccolta</span><span class="sxs-lookup"><span data-stu-id="8f38a-128">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="8f38a-129">Per configurare il linker per una libreria specifica, aggiungere un file di configurazione del linker XML nella libreria come risorsa incorporata.</span><span class="sxs-lookup"><span data-stu-id="8f38a-129">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="8f38a-130">La risorsa incorporata deve avere lo stesso nome dell'assembly.</span><span class="sxs-lookup"><span data-stu-id="8f38a-130">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="8f38a-131">Nell'esempio seguente il file *LinkerConfig. XML* viene specificato come una risorsa incorporata con lo stesso nome dell'assembly della libreria:</span><span class="sxs-lookup"><span data-stu-id="8f38a-131">In the following example, the *LinkerConfig.xml* file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="8f38a-132">Configurare il linker per l'internazionalizzazione</span><span class="sxs-lookup"><span data-stu-id="8f38a-132">Configure the linker for internationalization</span></span>

<span data-ttu-id="8f38a-133">Per impostazione predefinita, Blazor la configurazione del linker per le Blazor app webassembly rimuove le informazioni di internazionalizzazione ad eccezione delle impostazioni locali richieste in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="8f38a-133">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="8f38a-134">La rimozione di questi assembly riduce al minimo le dimensioni dell'app.</span><span class="sxs-lookup"><span data-stu-id="8f38a-134">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="8f38a-135">Per controllare quali assembly I18N vengono conservati, impostare la `<BlazorWebAssemblyI18NAssemblies>` Proprietà MSBuild nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="8f38a-135">To control which I18N assemblies are retained, set the `<BlazorWebAssemblyI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="8f38a-136">Valore Region</span><span class="sxs-lookup"><span data-stu-id="8f38a-136">Region Value</span></span>     | <span data-ttu-id="8f38a-137">Assembly dell'area mono</span><span class="sxs-lookup"><span data-stu-id="8f38a-137">Mono region assembly</span></span>    |
| ---
<span data-ttu-id="8f38a-138">title: "configurare il linker per ASP.NET Core Blazor " autore: Descrizione: "informazioni su come controllare il linker linguaggio intermedio (il) durante la compilazione di un' Blazor app".</span><span class="sxs-lookup"><span data-stu-id="8f38a-138">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8f38a-139">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8f38a-139">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8f38a-140">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-140">'Blazor'</span></span>
- <span data-ttu-id="8f38a-141">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8f38a-141">'Identity'</span></span>
- <span data-ttu-id="8f38a-142">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8f38a-142">'Let's Encrypt'</span></span>
- <span data-ttu-id="8f38a-143">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-143">'Razor'</span></span>
- <span data-ttu-id="8f38a-144">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="8f38a-144">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8f38a-145">title: "configurare il linker per ASP.NET Core Blazor " autore: Descrizione: "informazioni su come controllare il linker linguaggio intermedio (il) durante la compilazione di un' Blazor app".</span><span class="sxs-lookup"><span data-stu-id="8f38a-145">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8f38a-146">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8f38a-146">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8f38a-147">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-147">'Blazor'</span></span>
- <span data-ttu-id="8f38a-148">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8f38a-148">'Identity'</span></span>
- <span data-ttu-id="8f38a-149">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8f38a-149">'Let's Encrypt'</span></span>
- <span data-ttu-id="8f38a-150">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-150">'Razor'</span></span>
- <span data-ttu-id="8f38a-151">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="8f38a-151">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8f38a-152">title: "configurare il linker per ASP.NET Core Blazor " autore: Descrizione: "informazioni su come controllare il linker linguaggio intermedio (il) durante la compilazione di un' Blazor app".</span><span class="sxs-lookup"><span data-stu-id="8f38a-152">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8f38a-153">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8f38a-153">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8f38a-154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-154">'Blazor'</span></span>
- <span data-ttu-id="8f38a-155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8f38a-155">'Identity'</span></span>
- <span data-ttu-id="8f38a-156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8f38a-156">'Let's Encrypt'</span></span>
- <span data-ttu-id="8f38a-157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-157">'Razor'</span></span>
- <span data-ttu-id="8f38a-158">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="8f38a-158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8f38a-159">title: "configurare il linker per ASP.NET Core Blazor " autore: Descrizione: "informazioni su come controllare il linker linguaggio intermedio (il) durante la compilazione di un' Blazor app".</span><span class="sxs-lookup"><span data-stu-id="8f38a-159">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8f38a-160">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8f38a-160">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8f38a-161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-161">'Blazor'</span></span>
- <span data-ttu-id="8f38a-162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8f38a-162">'Identity'</span></span>
- <span data-ttu-id="8f38a-163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8f38a-163">'Let's Encrypt'</span></span>
- <span data-ttu-id="8f38a-164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-164">'Razor'</span></span>
- <span data-ttu-id="8f38a-165">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="8f38a-165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8f38a-166">title: "configurare il linker per ASP.NET Core Blazor " autore: Descrizione: "informazioni su come controllare il linker linguaggio intermedio (il) durante la compilazione di un' Blazor app".</span><span class="sxs-lookup"><span data-stu-id="8f38a-166">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8f38a-167">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8f38a-167">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8f38a-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-168">'Blazor'</span></span>
- <span data-ttu-id="8f38a-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8f38a-169">'Identity'</span></span>
- <span data-ttu-id="8f38a-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8f38a-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="8f38a-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-171">'Razor'</span></span>
- <span data-ttu-id="8f38a-172">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="8f38a-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8f38a-173">title: "configurare il linker per ASP.NET Core Blazor " autore: Descrizione: "informazioni su come controllare il linker linguaggio intermedio (il) durante la compilazione di un' Blazor app".</span><span class="sxs-lookup"><span data-stu-id="8f38a-173">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8f38a-174">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8f38a-174">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8f38a-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-175">'Blazor'</span></span>
- <span data-ttu-id="8f38a-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8f38a-176">'Identity'</span></span>
- <span data-ttu-id="8f38a-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8f38a-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="8f38a-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-178">'Razor'</span></span>
- <span data-ttu-id="8f38a-179">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="8f38a-179">'SignalR' uid:</span></span> 

<span data-ttu-id="8f38a-180">-------- | Titolo---: "configurare il linker per ASP.NET Core Blazor " autore: Descrizione: "informazioni su come controllare il linker linguaggio intermedio (il) durante la compilazione di un' Blazor app."</span><span class="sxs-lookup"><span data-stu-id="8f38a-180">-------- | --- title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8f38a-181">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8f38a-181">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8f38a-182">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-182">'Blazor'</span></span>
- <span data-ttu-id="8f38a-183">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8f38a-183">'Identity'</span></span>
- <span data-ttu-id="8f38a-184">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8f38a-184">'Let's Encrypt'</span></span>
- <span data-ttu-id="8f38a-185">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-185">'Razor'</span></span>
- <span data-ttu-id="8f38a-186">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="8f38a-186">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8f38a-187">title: "configurare il linker per ASP.NET Core Blazor " autore: Descrizione: "informazioni su come controllare il linker linguaggio intermedio (il) durante la compilazione di un' Blazor app".</span><span class="sxs-lookup"><span data-stu-id="8f38a-187">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8f38a-188">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8f38a-188">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8f38a-189">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-189">'Blazor'</span></span>
- <span data-ttu-id="8f38a-190">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8f38a-190">'Identity'</span></span>
- <span data-ttu-id="8f38a-191">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8f38a-191">'Let's Encrypt'</span></span>
- <span data-ttu-id="8f38a-192">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-192">'Razor'</span></span>
- <span data-ttu-id="8f38a-193">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="8f38a-193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8f38a-194">title: "configurare il linker per ASP.NET Core Blazor " autore: Descrizione: "informazioni su come controllare il linker linguaggio intermedio (il) durante la compilazione di un' Blazor app".</span><span class="sxs-lookup"><span data-stu-id="8f38a-194">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8f38a-195">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8f38a-195">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8f38a-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-196">'Blazor'</span></span>
- <span data-ttu-id="8f38a-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8f38a-197">'Identity'</span></span>
- <span data-ttu-id="8f38a-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8f38a-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="8f38a-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-199">'Razor'</span></span>
- <span data-ttu-id="8f38a-200">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="8f38a-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8f38a-201">title: "configurare il linker per ASP.NET Core Blazor " autore: Descrizione: "informazioni su come controllare il linker linguaggio intermedio (il) durante la compilazione di un' Blazor app".</span><span class="sxs-lookup"><span data-stu-id="8f38a-201">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8f38a-202">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8f38a-202">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8f38a-203">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-203">'Blazor'</span></span>
- <span data-ttu-id="8f38a-204">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8f38a-204">'Identity'</span></span>
- <span data-ttu-id="8f38a-205">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8f38a-205">'Let's Encrypt'</span></span>
- <span data-ttu-id="8f38a-206">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-206">'Razor'</span></span>
- <span data-ttu-id="8f38a-207">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="8f38a-207">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8f38a-208">title: "configurare il linker per ASP.NET Core Blazor " autore: Descrizione: "informazioni su come controllare il linker linguaggio intermedio (il) durante la compilazione di un' Blazor app".</span><span class="sxs-lookup"><span data-stu-id="8f38a-208">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8f38a-209">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8f38a-209">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8f38a-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-210">'Blazor'</span></span>
- <span data-ttu-id="8f38a-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8f38a-211">'Identity'</span></span>
- <span data-ttu-id="8f38a-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8f38a-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="8f38a-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-213">'Razor'</span></span>
- <span data-ttu-id="8f38a-214">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="8f38a-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8f38a-215">title: "configurare il linker per ASP.NET Core Blazor " autore: Descrizione: "informazioni su come controllare il linker linguaggio intermedio (il) durante la compilazione di un' Blazor app".</span><span class="sxs-lookup"><span data-stu-id="8f38a-215">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8f38a-216">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8f38a-216">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8f38a-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-217">'Blazor'</span></span>
- <span data-ttu-id="8f38a-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8f38a-218">'Identity'</span></span>
- <span data-ttu-id="8f38a-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8f38a-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="8f38a-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-220">'Razor'</span></span>
- <span data-ttu-id="8f38a-221">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="8f38a-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8f38a-222">title: "configurare il linker per ASP.NET Core Blazor " autore: Descrizione: "informazioni su come controllare il linker linguaggio intermedio (il) durante la compilazione di un' Blazor app".</span><span class="sxs-lookup"><span data-stu-id="8f38a-222">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8f38a-223">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8f38a-223">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8f38a-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-224">'Blazor'</span></span>
- <span data-ttu-id="8f38a-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8f38a-225">'Identity'</span></span>
- <span data-ttu-id="8f38a-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8f38a-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="8f38a-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-227">'Razor'</span></span>
- <span data-ttu-id="8f38a-228">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="8f38a-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8f38a-229">title: "configurare il linker per ASP.NET Core Blazor " autore: Descrizione: "informazioni su come controllare il linker linguaggio intermedio (il) durante la compilazione di un' Blazor app".</span><span class="sxs-lookup"><span data-stu-id="8f38a-229">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8f38a-230">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8f38a-230">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8f38a-231">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-231">'Blazor'</span></span>
- <span data-ttu-id="8f38a-232">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8f38a-232">'Identity'</span></span>
- <span data-ttu-id="8f38a-233">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8f38a-233">'Let's Encrypt'</span></span>
- <span data-ttu-id="8f38a-234">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-234">'Razor'</span></span>
- <span data-ttu-id="8f38a-235">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="8f38a-235">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8f38a-236">title: "configurare il linker per ASP.NET Core Blazor " autore: Descrizione: "informazioni su come controllare il linker linguaggio intermedio (il) durante la compilazione di un' Blazor app".</span><span class="sxs-lookup"><span data-stu-id="8f38a-236">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8f38a-237">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8f38a-237">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8f38a-238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-238">'Blazor'</span></span>
- <span data-ttu-id="8f38a-239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8f38a-239">'Identity'</span></span>
- <span data-ttu-id="8f38a-240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8f38a-240">'Let's Encrypt'</span></span>
- <span data-ttu-id="8f38a-241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8f38a-241">'Razor'</span></span>
- <span data-ttu-id="8f38a-242">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="8f38a-242">'SignalR' uid:</span></span> 

<span data-ttu-id="8f38a-243">------------ | | `all`            | Tutti gli assembly inclusi | | `cjk`             |  *I18n. CJK. dll* | | `mideast`         |  *I18n. . Dll* `none`(impostazione predefinita) | Nessuno | | `other`           |  *I18n. Other. dll* | | `rare`            |  *I18n. Rare. dll* | | `west`            |  *I18n. Dll occidentale*         |</span><span class="sxs-lookup"><span data-stu-id="8f38a-243">------------ | | `all`            | All assemblies included | | `cjk`            | *I18N.CJK.dll*          | | `mideast`        | *I18N.MidEast.dll*      | | `none` (default) | None                    | | `other`          | *I18N.Other.dll*        | | `rare`           | *I18N.Rare.dll*         | | `west`           | *I18N.West.dll*         |</span></span>

<span data-ttu-id="8f38a-244">Usare una virgola per separare più valori (ad esempio, `mideast,west` ).</span><span class="sxs-lookup"><span data-stu-id="8f38a-244">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="8f38a-245">Per altre informazioni, vedere [i18n: Pnetlib internationalation Framework Library (repository GitHub mono/mono)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="8f38a-245">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8f38a-246">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="8f38a-246">Additional resources</span></span>

* <xref:performance/blazor/webassembly-best-practices#intermediate-language-il-linking>
