---
title: Provider di file in ASP.NET Core
author: rick-anderson
description: Informazioni su come ASP.NET Core astrae l'accesso al file system tramite l'utilizzo di provider di file.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
no-loc:
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
uid: fundamentals/file-providers
ms.openlocfilehash: 30c28e7bd4cd9c926b157f5a7b9e6688bd5b9b9a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634605"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="fc842-103">Provider di file in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc842-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="fc842-104">Di [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="fc842-104">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fc842-105">ASP.NET Core astrae l'accesso al file system tramite l'utilizzo di provider di file.</span><span class="sxs-lookup"><span data-stu-id="fc842-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="fc842-106">I provider di file vengono utilizzati in tutto il framework ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fc842-106">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="fc842-107">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="fc842-107">For example:</span></span>

* <span data-ttu-id="fc842-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> espone la radice del [contenuto](xref:fundamentals/index#content-root) dell'app e la [radice Web](xref:fundamentals/index#web-root) come `IFileProvider` tipi.</span><span class="sxs-lookup"><span data-stu-id="fc842-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="fc842-109">Il [middleware dei file statici](xref:fundamentals/static-files) usa i provider di file per trovare i file statici.</span><span class="sxs-lookup"><span data-stu-id="fc842-109">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="fc842-110">[Razor](xref:mvc/views/razor) USA i provider di file per individuare le pagine e le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="fc842-110">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="fc842-111">Gli strumenti .NET Core usano i provider di file e i criteri GLOB per specificare i file da pubblicare.</span><span class="sxs-lookup"><span data-stu-id="fc842-111">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="fc842-112">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fc842-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="fc842-113">Interfacce del provider di file</span><span class="sxs-lookup"><span data-stu-id="fc842-113">File Provider interfaces</span></span>

<span data-ttu-id="fc842-114">L'interfaccia primaria è <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="fc842-114">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="fc842-115">`IFileProvider` espone metodi per:</span><span class="sxs-lookup"><span data-stu-id="fc842-115">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="fc842-116">Ottenere informazioni sui file (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="fc842-116">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="fc842-117">Ottenere informazioni sulle directory (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="fc842-117">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="fc842-118">Configurare le notifiche di modifica (usando un <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="fc842-118">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="fc842-119">`IFileInfo` fornisce metodi e proprietà per l'uso di file:</span><span class="sxs-lookup"><span data-stu-id="fc842-119">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="fc842-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in byte)</span><span class="sxs-lookup"><span data-stu-id="fc842-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="fc842-121">Data <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified></span><span class="sxs-lookup"><span data-stu-id="fc842-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="fc842-122">È possibile leggere dal file usando il <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> metodo.</span><span class="sxs-lookup"><span data-stu-id="fc842-122">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="fc842-123">L'app di esempio *FileProviderSample* illustra come configurare un provider di file in `Startup.ConfigureServices` per l'uso nell'app tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="fc842-123">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="fc842-124">Implementazioni dei provider di file</span><span class="sxs-lookup"><span data-stu-id="fc842-124">File Provider implementations</span></span>

<span data-ttu-id="fc842-125">Nella tabella seguente sono elencate le implementazioni di `IFileProvider` .</span><span class="sxs-lookup"><span data-stu-id="fc842-125">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="fc842-126">Implementazione</span><span class="sxs-lookup"><span data-stu-id="fc842-126">Implementation</span></span> | <span data-ttu-id="fc842-127">Descrizione</span><span class="sxs-lookup"><span data-stu-id="fc842-127">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="fc842-128">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="fc842-128">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="fc842-129">Utilizzato per fornire l'accesso combinato a file e directory da uno o più provider.</span><span class="sxs-lookup"><span data-stu-id="fc842-129">Used to provide combined access to files and directories from one or more other providers.</span></span> |
| [<span data-ttu-id="fc842-130">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="fc842-130">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="fc842-131">Utilizzato per accedere ai file incorporati negli assembly.</span><span class="sxs-lookup"><span data-stu-id="fc842-131">Used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="fc842-132">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="fc842-132">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="fc842-133">Utilizzato per accedere ai file fisici del sistema.</span><span class="sxs-lookup"><span data-stu-id="fc842-133">Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="fc842-134">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="fc842-134">PhysicalFileProvider</span></span>

<span data-ttu-id="fc842-135"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> consente di accedere al file system fisico.</span><span class="sxs-lookup"><span data-stu-id="fc842-135">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="fc842-136">`PhysicalFileProvider` usa il tipo <xref:System.IO.File?displayProperty=fullName> (per il provider fisico) e definisce una directory e i relativi elementi figlio come ambito per tutti i percorsi.</span><span class="sxs-lookup"><span data-stu-id="fc842-136">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="fc842-137">La definizione dell'ambito impedisce l'accesso al file system al di fuori della directory specificata e dei relativi elementi figlio.</span><span class="sxs-lookup"><span data-stu-id="fc842-137">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="fc842-138">Lo scenario più comune per la creazione e l'uso di un `PhysicalFileProvider` consiste nel richiedere un oggetto `IFileProvider` in un costruttore tramite [inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="fc842-138">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="fc842-139">Quando si crea direttamente un'istanza di questo provider, viene richiesto un percorso di directory assoluto che funge da percorso di base per tutte le richieste effettuate tramite il provider.</span><span class="sxs-lookup"><span data-stu-id="fc842-139">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="fc842-140">I modelli Glob non sono supportati nel percorso della directory.</span><span class="sxs-lookup"><span data-stu-id="fc842-140">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="fc842-141">Nel codice seguente viene illustrato come utilizzare `PhysicalFileProvider` per ottenere il contenuto della directory e le informazioni sul file:</span><span class="sxs-lookup"><span data-stu-id="fc842-141">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="fc842-142">Tipi nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="fc842-142">Types in the preceding example:</span></span>

* <span data-ttu-id="fc842-143">`provider` è di tipo `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="fc842-143">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="fc842-144">`contents` è di tipo `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="fc842-144">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="fc842-145">`fileInfo` è di tipo `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="fc842-145">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="fc842-146">Il provider di file può essere usato per scorrere la directory specificata da `applicationRoot` oppure per chiamare `GetFileInfo` per ottenere informazioni su un file.</span><span class="sxs-lookup"><span data-stu-id="fc842-146">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="fc842-147">Non è possibile passare i modelli glob al `GetFileInfo` metodo.</span><span class="sxs-lookup"><span data-stu-id="fc842-147">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="fc842-148">Il provider di file non ha accesso all'esterno della directory `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="fc842-148">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="fc842-149">L'app di esempio *FileProviderSample* crea il provider nel `Startup.ConfigureServices` Metodo usando <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="fc842-149">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="fc842-150">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="fc842-150">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="fc842-151"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> consente di accedere ai file incorporati negli assembly.</span><span class="sxs-lookup"><span data-stu-id="fc842-151">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="fc842-152">`ManifestEmbeddedFileProvider` usa un manifesto compilato nell'assembly per ricostruire i percorsi originali dei file incorporati.</span><span class="sxs-lookup"><span data-stu-id="fc842-152">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="fc842-153">Per generare un manifesto dei file incorporati:</span><span class="sxs-lookup"><span data-stu-id="fc842-153">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="fc842-154">Aggiungere il pacchetto NuGet [Microsoft. Extensions. Fileprovides. Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) al progetto.</span><span class="sxs-lookup"><span data-stu-id="fc842-154">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="fc842-155">Impostare la proprietà `<GenerateEmbeddedFilesManifest>` su `true`.</span><span class="sxs-lookup"><span data-stu-id="fc842-155">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="fc842-156">Specificare i file da incorporare con [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) :</span><span class="sxs-lookup"><span data-stu-id="fc842-156">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="fc842-157">Usare [modelli GLOB](#glob-patterns) per specificare uno o più file da incorporare nell'assembly.</span><span class="sxs-lookup"><span data-stu-id="fc842-157">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="fc842-158">L'app di esempio *FileProviderSample* crea `ManifestEmbeddedFileProvider` e passa l'assembly attualmente in esecuzione al relativo costruttore.</span><span class="sxs-lookup"><span data-stu-id="fc842-158">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="fc842-159">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="fc842-159">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="fc842-160">Overload aggiuntivi consentono di:</span><span class="sxs-lookup"><span data-stu-id="fc842-160">Additional overloads allow you to:</span></span>

* <span data-ttu-id="fc842-161">Specificare un percorso di file relativo.</span><span class="sxs-lookup"><span data-stu-id="fc842-161">Specify a relative file path.</span></span>
* <span data-ttu-id="fc842-162">Definire la data dell'ultima modifica come ambito dei file.</span><span class="sxs-lookup"><span data-stu-id="fc842-162">Scope files to a last modified date.</span></span>
* <span data-ttu-id="fc842-163">Assegnare un nome alla risorsa incorporata contenente il manifesto dei file incorporati.</span><span class="sxs-lookup"><span data-stu-id="fc842-163">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="fc842-164">Overload</span><span class="sxs-lookup"><span data-stu-id="fc842-164">Overload</span></span> | <span data-ttu-id="fc842-165">Descrizione</span><span class="sxs-lookup"><span data-stu-id="fc842-165">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="fc842-166">Accetta un parametro di percorso relativo `root` facoltativo.</span><span class="sxs-lookup"><span data-stu-id="fc842-166">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="fc842-167">Specificare `root` per definire come ambito delle chiamate a <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> le risorse incluse nel percorso specificato.</span><span class="sxs-lookup"><span data-stu-id="fc842-167">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="fc842-168">Accetta un parametro di percorso relativo `root` facoltativo e un parametro di data `lastModified` (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="fc842-168">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="fc842-169">La data `lastModified` definisce la data dell'ultima modifica come ambito per le istanze di <xref:Microsoft.Extensions.FileProviders.IFileInfo> restituite da <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="fc842-169">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="fc842-170">Accetta un parametro di percorso relativo `root` facoltativo, un parametro di data `lastModified` e il parametro `manifestName`.</span><span class="sxs-lookup"><span data-stu-id="fc842-170">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="fc842-171">`manifestName` rappresenta il nome della risorsa incorporata contenente il manifesto.</span><span class="sxs-lookup"><span data-stu-id="fc842-171">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="fc842-172">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="fc842-172">CompositeFileProvider</span></span>

<span data-ttu-id="fc842-173"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combina le istanze `IFileProvider` esponendo una sola interfaccia per l'uso dei file di più provider.</span><span class="sxs-lookup"><span data-stu-id="fc842-173">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="fc842-174">Quando si crea `CompositeFileProvider`, passare una o più istanze `IFileProvider` al relativo costruttore.</span><span class="sxs-lookup"><span data-stu-id="fc842-174">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="fc842-175">Nell'app di esempio *FileProviderSample* , un `PhysicalFileProvider` e un `ManifestEmbeddedFileProvider` forniscono i file a un `CompositeFileProvider` registrato nel contenitore del servizio dell'app.</span><span class="sxs-lookup"><span data-stu-id="fc842-175">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="fc842-176">Il codice seguente si trova nel metodo del progetto `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="fc842-176">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="fc842-177">Controllo delle modifiche</span><span class="sxs-lookup"><span data-stu-id="fc842-177">Watch for changes</span></span>

<span data-ttu-id="fc842-178">Il <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> metodo fornisce uno scenario per controllare la presenza di modifiche in uno o più file o directory.</span><span class="sxs-lookup"><span data-stu-id="fc842-178">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="fc842-179">Il metodo `Watch`:</span><span class="sxs-lookup"><span data-stu-id="fc842-179">The `Watch` method:</span></span>

* <span data-ttu-id="fc842-180">Accetta una stringa di percorso del file, che può usare i [modelli glob](#glob-patterns) per specificare più file.</span><span class="sxs-lookup"><span data-stu-id="fc842-180">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="fc842-181">Restituisce un valore <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="fc842-181">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="fc842-182">Il token di modifica risultante espone:</span><span class="sxs-lookup"><span data-stu-id="fc842-182">The resulting change token exposes:</span></span>

* <span data-ttu-id="fc842-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Proprietà che può essere controllata per determinare se si è verificata una modifica.</span><span class="sxs-lookup"><span data-stu-id="fc842-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="fc842-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Viene chiamato quando vengono rilevate modifiche nella stringa di percorso specificata.</span><span class="sxs-lookup"><span data-stu-id="fc842-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="fc842-185">Ogni token di modifica chiama solo il relativo callback associato in risposta a una singola modifica.</span><span class="sxs-lookup"><span data-stu-id="fc842-185">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="fc842-186">Per abilitare un monitoraggio continuo, usare <xref:System.Threading.Tasks.TaskCompletionSource`1> come illustrato di seguito oppure ricreare istanze di `IChangeToken` in risposta alle modifiche.</span><span class="sxs-lookup"><span data-stu-id="fc842-186">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="fc842-187">L'app di esempio *WatchConsole* scrive un messaggio ogni volta che viene modificato un file con *estensione txt* nella directory *textfiles* :</span><span class="sxs-lookup"><span data-stu-id="fc842-187">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="fc842-188">È possibile che alcuni file system, ad esempio i contenitori Docker e le condivisioni di rete, non inviino sempre le notifiche di modifica.</span><span class="sxs-lookup"><span data-stu-id="fc842-188">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="fc842-189">Impostare la variabile di ambiente `DOTNET_USE_POLLING_FILE_WATCHER` su `1` o `true` per eseguire il polling delle modifiche nel file system ogni quattro secondi (intervallo non configurabile).</span><span class="sxs-lookup"><span data-stu-id="fc842-189">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="fc842-190">Modelli GLOB</span><span class="sxs-lookup"><span data-stu-id="fc842-190">Glob patterns</span></span>

<span data-ttu-id="fc842-191">Nei percorsi del file system vengono usati criteri con caratteri jolly chiamati *criteri GLOB (o globbing)*.</span><span class="sxs-lookup"><span data-stu-id="fc842-191">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="fc842-192">Specificare gruppi di file con questi criteri.</span><span class="sxs-lookup"><span data-stu-id="fc842-192">Specify groups of files with these patterns.</span></span> <span data-ttu-id="fc842-193">I due caratteri jolly sono `*` e `**`:</span><span class="sxs-lookup"><span data-stu-id="fc842-193">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="fc842-194">Cerca qualsiasi elemento a livello della cartella corrente, qualsiasi nome di file o qualsiasi estensione di file.</span><span class="sxs-lookup"><span data-stu-id="fc842-194">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="fc842-195">Le corrispondenze vengono terminate con i caratteri `/` e `.` nel percorso dei file.</span><span class="sxs-lookup"><span data-stu-id="fc842-195">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="fc842-196">Cerca qualsiasi elemento in più livelli di directory.</span><span class="sxs-lookup"><span data-stu-id="fc842-196">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="fc842-197">Può essere usato per cercare in modo ricorsivo numerosi file all'interno di una gerarchia di directory.</span><span class="sxs-lookup"><span data-stu-id="fc842-197">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="fc842-198">La tabella seguente fornisce esempi comuni di modelli glob.</span><span class="sxs-lookup"><span data-stu-id="fc842-198">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="fc842-199">Modello</span><span class="sxs-lookup"><span data-stu-id="fc842-199">Pattern</span></span>  |<span data-ttu-id="fc842-200">Descrizione</span><span class="sxs-lookup"><span data-stu-id="fc842-200">Description</span></span>  |
|---------|---------|
|`directory/file.txt`|<span data-ttu-id="fc842-201">Cerca un file specifico in una directory specifica.</span><span class="sxs-lookup"><span data-stu-id="fc842-201">Matches a specific file in a specific directory.</span></span>|
|`directory/*.txt`|<span data-ttu-id="fc842-202">Cerca tutti i file con estensione *txt* in una directory specifica.</span><span class="sxs-lookup"><span data-stu-id="fc842-202">Matches all files with *.txt* extension in a specific directory.</span></span>|
|`directory/*/appsettings.json`|<span data-ttu-id="fc842-203">Corrisponde a tutti i *appsettings.jssui* file nelle directory esattamente un livello sotto la cartella *directory* .</span><span class="sxs-lookup"><span data-stu-id="fc842-203">Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.</span></span>|
|`directory/**/*.txt`|<span data-ttu-id="fc842-204">Corrisponde a tutti i file con estensione *txt* trovati in un punto qualsiasi della cartella della *directory* .</span><span class="sxs-lookup"><span data-stu-id="fc842-204">Matches all files with a *.txt* extension found anywhere under the *directory* folder.</span></span>|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fc842-205">ASP.NET Core astrae l'accesso al file system tramite l'utilizzo di provider di file.</span><span class="sxs-lookup"><span data-stu-id="fc842-205">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="fc842-206">I provider di file vengono usati in tutto il framework di ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="fc842-206">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="fc842-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> espone la radice del [contenuto](xref:fundamentals/index#content-root) dell'app e la [radice Web](xref:fundamentals/index#web-root) come `IFileProvider` tipi.</span><span class="sxs-lookup"><span data-stu-id="fc842-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="fc842-208">Il [middleware dei file statici](xref:fundamentals/static-files) usa i provider di file per trovare i file statici.</span><span class="sxs-lookup"><span data-stu-id="fc842-208">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="fc842-209">[Razor](xref:mvc/views/razor) USA i provider di file per individuare le pagine e le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="fc842-209">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="fc842-210">Gli strumenti .NET Core usano i provider di file e i criteri GLOB per specificare i file da pubblicare.</span><span class="sxs-lookup"><span data-stu-id="fc842-210">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="fc842-211">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fc842-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="fc842-212">Interfacce del provider di file</span><span class="sxs-lookup"><span data-stu-id="fc842-212">File Provider interfaces</span></span>

<span data-ttu-id="fc842-213">L'interfaccia primaria è <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="fc842-213">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="fc842-214">`IFileProvider` espone metodi per:</span><span class="sxs-lookup"><span data-stu-id="fc842-214">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="fc842-215">Ottenere informazioni sui file (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="fc842-215">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="fc842-216">Ottenere informazioni sulle directory (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="fc842-216">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="fc842-217">Configurare le notifiche di modifica (usando un <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="fc842-217">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="fc842-218">`IFileInfo` fornisce metodi e proprietà per l'uso di file:</span><span class="sxs-lookup"><span data-stu-id="fc842-218">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="fc842-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in byte)</span><span class="sxs-lookup"><span data-stu-id="fc842-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="fc842-220">Data <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified></span><span class="sxs-lookup"><span data-stu-id="fc842-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="fc842-221">È possibile leggere dal file usando il metodo [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*).</span><span class="sxs-lookup"><span data-stu-id="fc842-221">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="fc842-222">L'app di esempio dimostra come configurare un provider di file in `Startup.ConfigureServices` da usare in tutta l'app tramite [inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="fc842-222">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="fc842-223">Implementazioni dei provider di file</span><span class="sxs-lookup"><span data-stu-id="fc842-223">File Provider implementations</span></span>

<span data-ttu-id="fc842-224">Sono disponibili tre implementazioni di `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="fc842-224">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="fc842-225">Implementazione</span><span class="sxs-lookup"><span data-stu-id="fc842-225">Implementation</span></span> | <span data-ttu-id="fc842-226">Descrizione</span><span class="sxs-lookup"><span data-stu-id="fc842-226">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="fc842-227">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="fc842-227">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="fc842-228">Il provider fisico viene usato per accedere ai file fisici del sistema.</span><span class="sxs-lookup"><span data-stu-id="fc842-228">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="fc842-229">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="fc842-229">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="fc842-230">Il provider incorporato nel manifesto viene usato per accedere ai file incorporati negli assembly.</span><span class="sxs-lookup"><span data-stu-id="fc842-230">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="fc842-231">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="fc842-231">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="fc842-232">Il provider composito consente di offrire un accesso combinato a file e directory da uno o più provider.</span><span class="sxs-lookup"><span data-stu-id="fc842-232">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="fc842-233">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="fc842-233">PhysicalFileProvider</span></span>

<span data-ttu-id="fc842-234"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> consente di accedere al file system fisico.</span><span class="sxs-lookup"><span data-stu-id="fc842-234">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="fc842-235">`PhysicalFileProvider` usa il tipo <xref:System.IO.File?displayProperty=fullName> (per il provider fisico) e definisce una directory e i relativi elementi figlio come ambito per tutti i percorsi.</span><span class="sxs-lookup"><span data-stu-id="fc842-235">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="fc842-236">La definizione dell'ambito impedisce l'accesso al file system al di fuori della directory specificata e dei relativi elementi figlio.</span><span class="sxs-lookup"><span data-stu-id="fc842-236">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="fc842-237">Lo scenario più comune per la creazione e l'uso di un `PhysicalFileProvider` consiste nel richiedere un oggetto `IFileProvider` in un costruttore tramite [inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="fc842-237">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="fc842-238">Per la creazione diretta di un'istanza di questo provider, è richiesto un percorso di directory che viene usato come percorso di base per tutte le richieste effettuate tramite il provider.</span><span class="sxs-lookup"><span data-stu-id="fc842-238">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="fc842-239">Il codice seguente illustra come creare un `PhysicalFileProvider` e usarlo per ottenere il contenuto della directory e informazioni sui file:</span><span class="sxs-lookup"><span data-stu-id="fc842-239">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="fc842-240">Tipi nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="fc842-240">Types in the preceding example:</span></span>

* <span data-ttu-id="fc842-241">`provider` è di tipo `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="fc842-241">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="fc842-242">`contents` è di tipo `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="fc842-242">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="fc842-243">`fileInfo` è di tipo `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="fc842-243">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="fc842-244">Il provider di file può essere usato per scorrere la directory specificata da `applicationRoot` oppure per chiamare `GetFileInfo` per ottenere informazioni su un file.</span><span class="sxs-lookup"><span data-stu-id="fc842-244">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="fc842-245">Il provider di file non ha accesso all'esterno della directory `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="fc842-245">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="fc842-246">L'app di esempio crea il provider nella classe `Startup.ConfigureServices` dell'app usando [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="fc842-246">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="fc842-247">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="fc842-247">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="fc842-248"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> consente di accedere ai file incorporati negli assembly.</span><span class="sxs-lookup"><span data-stu-id="fc842-248">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="fc842-249">`ManifestEmbeddedFileProvider` usa un manifesto compilato nell'assembly per ricostruire i percorsi originali dei file incorporati.</span><span class="sxs-lookup"><span data-stu-id="fc842-249">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="fc842-250">Per generare un manifesto dei file incorporati, impostare la proprietà `<GenerateEmbeddedFilesManifest>` su `true`.</span><span class="sxs-lookup"><span data-stu-id="fc842-250">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="fc842-251">Specificare i file da incorporare con [ &lt; EmbeddedResource &gt; ](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="fc842-251">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-xml[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="fc842-252">Usare [modelli GLOB](#glob-patterns) per specificare uno o più file da incorporare nell'assembly.</span><span class="sxs-lookup"><span data-stu-id="fc842-252">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="fc842-253">L'app di esempio crea un `ManifestEmbeddedFileProvider` e passa l'assembly attualmente in esecuzione al rispettivo costruttore.</span><span class="sxs-lookup"><span data-stu-id="fc842-253">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="fc842-254">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="fc842-254">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="fc842-255">Overload aggiuntivi consentono di:</span><span class="sxs-lookup"><span data-stu-id="fc842-255">Additional overloads allow you to:</span></span>

* <span data-ttu-id="fc842-256">Specificare un percorso di file relativo.</span><span class="sxs-lookup"><span data-stu-id="fc842-256">Specify a relative file path.</span></span>
* <span data-ttu-id="fc842-257">Definire la data dell'ultima modifica come ambito dei file.</span><span class="sxs-lookup"><span data-stu-id="fc842-257">Scope files to a last modified date.</span></span>
* <span data-ttu-id="fc842-258">Assegnare un nome alla risorsa incorporata contenente il manifesto dei file incorporati.</span><span class="sxs-lookup"><span data-stu-id="fc842-258">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="fc842-259">Overload</span><span class="sxs-lookup"><span data-stu-id="fc842-259">Overload</span></span> | <span data-ttu-id="fc842-260">Descrizione</span><span class="sxs-lookup"><span data-stu-id="fc842-260">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="fc842-261">Accetta un parametro di percorso relativo `root` facoltativo.</span><span class="sxs-lookup"><span data-stu-id="fc842-261">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="fc842-262">Specificare `root` per definire come ambito delle chiamate a <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> le risorse incluse nel percorso specificato.</span><span class="sxs-lookup"><span data-stu-id="fc842-262">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="fc842-263">Accetta un parametro di percorso relativo `root` facoltativo e un parametro di data `lastModified` (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="fc842-263">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="fc842-264">La data `lastModified` definisce la data dell'ultima modifica come ambito per le istanze di <xref:Microsoft.Extensions.FileProviders.IFileInfo> restituite da <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="fc842-264">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="fc842-265">Accetta un parametro di percorso relativo `root` facoltativo, un parametro di data `lastModified` e il parametro `manifestName`.</span><span class="sxs-lookup"><span data-stu-id="fc842-265">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="fc842-266">`manifestName` rappresenta il nome della risorsa incorporata contenente il manifesto.</span><span class="sxs-lookup"><span data-stu-id="fc842-266">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="fc842-267">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="fc842-267">CompositeFileProvider</span></span>

<span data-ttu-id="fc842-268"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combina le istanze `IFileProvider` esponendo una sola interfaccia per l'uso dei file di più provider.</span><span class="sxs-lookup"><span data-stu-id="fc842-268">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="fc842-269">Quando si crea `CompositeFileProvider`, passare una o più istanze `IFileProvider` al relativo costruttore.</span><span class="sxs-lookup"><span data-stu-id="fc842-269">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="fc842-270">Nell'app di esempio, un `PhysicalFileProvider` e un `ManifestEmbeddedFileProvider` forniscono i file a un `CompositeFileProvider` registrato nel contenitore dei servizi dell'app:</span><span class="sxs-lookup"><span data-stu-id="fc842-270">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="fc842-271">Controllo delle modifiche</span><span class="sxs-lookup"><span data-stu-id="fc842-271">Watch for changes</span></span>

<span data-ttu-id="fc842-272">Il metodo [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) consente di controllare uno o più file o directory per il rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="fc842-272">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="fc842-273">`Watch` accetta una stringa di percorso in cui è possibile usare [criteri GLOB](#glob-patterns) per specificare più file.</span><span class="sxs-lookup"><span data-stu-id="fc842-273">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="fc842-274">`Watch` restituisce un oggetto <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="fc842-274">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="fc842-275">Il token di modifica espone:</span><span class="sxs-lookup"><span data-stu-id="fc842-275">The change token exposes:</span></span>

* <span data-ttu-id="fc842-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Proprietà che può essere controllata per determinare se si è verificata una modifica.</span><span class="sxs-lookup"><span data-stu-id="fc842-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="fc842-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Viene chiamato quando vengono rilevate modifiche nella stringa di percorso specificata.</span><span class="sxs-lookup"><span data-stu-id="fc842-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="fc842-278">Ogni token di modifica chiama solo il relativo callback associato in risposta a una singola modifica.</span><span class="sxs-lookup"><span data-stu-id="fc842-278">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="fc842-279">Per abilitare un monitoraggio continuo, usare <xref:System.Threading.Tasks.TaskCompletionSource`1> come illustrato di seguito oppure ricreare istanze di `IChangeToken` in risposta alle modifiche.</span><span class="sxs-lookup"><span data-stu-id="fc842-279">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="fc842-280">Nell'app di esempio, l'app console *WatchConsole* viene configurata per visualizzare un messaggio quando viene modificato un file di testo:</span><span class="sxs-lookup"><span data-stu-id="fc842-280">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="fc842-281">È possibile che alcuni file system, ad esempio i contenitori Docker e le condivisioni di rete, non inviino sempre le notifiche di modifica.</span><span class="sxs-lookup"><span data-stu-id="fc842-281">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="fc842-282">Impostare la variabile di ambiente `DOTNET_USE_POLLING_FILE_WATCHER` su `1` o `true` per eseguire il polling delle modifiche nel file system ogni quattro secondi (intervallo non configurabile).</span><span class="sxs-lookup"><span data-stu-id="fc842-282">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="fc842-283">Modelli GLOB</span><span class="sxs-lookup"><span data-stu-id="fc842-283">Glob patterns</span></span>

<span data-ttu-id="fc842-284">Nei percorsi del file system vengono usati criteri con caratteri jolly chiamati *criteri GLOB (o globbing)*.</span><span class="sxs-lookup"><span data-stu-id="fc842-284">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="fc842-285">Specificare gruppi di file con questi criteri.</span><span class="sxs-lookup"><span data-stu-id="fc842-285">Specify groups of files with these patterns.</span></span> <span data-ttu-id="fc842-286">I due caratteri jolly sono `*` e `**`:</span><span class="sxs-lookup"><span data-stu-id="fc842-286">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="fc842-287">Cerca qualsiasi elemento a livello della cartella corrente, qualsiasi nome di file o qualsiasi estensione di file.</span><span class="sxs-lookup"><span data-stu-id="fc842-287">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="fc842-288">Le corrispondenze vengono terminate con i caratteri `/` e `.` nel percorso dei file.</span><span class="sxs-lookup"><span data-stu-id="fc842-288">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="fc842-289">Cerca qualsiasi elemento in più livelli di directory.</span><span class="sxs-lookup"><span data-stu-id="fc842-289">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="fc842-290">Può essere usato per cercare in modo ricorsivo numerosi file all'interno di una gerarchia di directory.</span><span class="sxs-lookup"><span data-stu-id="fc842-290">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="fc842-291">**Esempi di criteri GLOB**</span><span class="sxs-lookup"><span data-stu-id="fc842-291">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="fc842-292">Cerca un file specifico in una directory specifica.</span><span class="sxs-lookup"><span data-stu-id="fc842-292">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="fc842-293">Cerca tutti i file con estensione *txt* in una directory specifica.</span><span class="sxs-lookup"><span data-stu-id="fc842-293">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="fc842-294">Cerca tutti i file `appsettings.json` nelle directory esattamente un livello sotto la cartella *directory*.</span><span class="sxs-lookup"><span data-stu-id="fc842-294">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="fc842-295">Cerca tutti i file con estensione *txt* che si trovano in qualsiasi posizione nella cartella *directory*.</span><span class="sxs-lookup"><span data-stu-id="fc842-295">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
