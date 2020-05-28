---
<span data-ttu-id="cfd64-101">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-102">'Blazor'</span></span>
- <span data-ttu-id="cfd64-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-103">'Identity'</span></span>
- <span data-ttu-id="cfd64-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-105">'Razor'</span></span>
- <span data-ttu-id="cfd64-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-106">'SignalR' uid:</span></span> 

---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="cfd64-107">Provider di file in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cfd64-107">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="cfd64-108">Di [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="cfd64-108">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cfd64-109">ASP.NET Core astrae l'accesso al file system tramite l'utilizzo di provider di file.</span><span class="sxs-lookup"><span data-stu-id="cfd64-109">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="cfd64-110">I provider di file vengono utilizzati in tutto il framework ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cfd64-110">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="cfd64-111">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="cfd64-111">For example:</span></span>

* <span data-ttu-id="cfd64-112"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>espone la radice del [contenuto](xref:fundamentals/index#content-root) dell'app e la [radice Web](xref:fundamentals/index#web-root) come `IFileProvider` tipi.</span><span class="sxs-lookup"><span data-stu-id="cfd64-112"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="cfd64-113">Il [middleware dei file statici](xref:fundamentals/static-files) usa i provider di file per trovare i file statici.</span><span class="sxs-lookup"><span data-stu-id="cfd64-113">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="cfd64-114">[Razor](xref:mvc/views/razor)USA i provider di file per individuare le pagine e le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="cfd64-114">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="cfd64-115">Gli strumenti .NET Core usano i provider di file e i criteri GLOB per specificare i file da pubblicare.</span><span class="sxs-lookup"><span data-stu-id="cfd64-115">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="cfd64-116">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cfd64-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="cfd64-117">Interfacce del provider di file</span><span class="sxs-lookup"><span data-stu-id="cfd64-117">File Provider interfaces</span></span>

<span data-ttu-id="cfd64-118">L'interfaccia primaria è <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="cfd64-118">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="cfd64-119">`IFileProvider` espone metodi per:</span><span class="sxs-lookup"><span data-stu-id="cfd64-119">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="cfd64-120">Ottenere informazioni sui file (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="cfd64-120">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="cfd64-121">Ottenere informazioni sulle directory (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="cfd64-121">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="cfd64-122">Configurare le notifiche di modifica (usando un <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="cfd64-122">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="cfd64-123">`IFileInfo` fornisce metodi e proprietà per l'uso di file:</span><span class="sxs-lookup"><span data-stu-id="cfd64-123">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="cfd64-124"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in byte)</span><span class="sxs-lookup"><span data-stu-id="cfd64-124"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="cfd64-125">Data <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified></span><span class="sxs-lookup"><span data-stu-id="cfd64-125"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="cfd64-126">È possibile leggere dal file usando il <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> metodo.</span><span class="sxs-lookup"><span data-stu-id="cfd64-126">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="cfd64-127">L'app di esempio *FileProviderSample* illustra come configurare un provider di file in `Startup.ConfigureServices` per l'uso nell'app tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cfd64-127">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="cfd64-128">Implementazioni dei provider di file</span><span class="sxs-lookup"><span data-stu-id="cfd64-128">File Provider implementations</span></span>

<span data-ttu-id="cfd64-129">Nella tabella seguente sono elencate le implementazioni di `IFileProvider` .</span><span class="sxs-lookup"><span data-stu-id="cfd64-129">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="cfd64-130">Implementazione</span><span class="sxs-lookup"><span data-stu-id="cfd64-130">Implementation</span></span> | <span data-ttu-id="cfd64-131">Descrizione</span><span class="sxs-lookup"><span data-stu-id="cfd64-131">Description</span></span> |
| ---
<span data-ttu-id="cfd64-132">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-133">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-133">'Blazor'</span></span>
- <span data-ttu-id="cfd64-134">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-134">'Identity'</span></span>
- <span data-ttu-id="cfd64-135">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-135">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-136">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-136">'Razor'</span></span>
- <span data-ttu-id="cfd64-137">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-137">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cfd64-138">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-139">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-139">'Blazor'</span></span>
- <span data-ttu-id="cfd64-140">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-140">'Identity'</span></span>
- <span data-ttu-id="cfd64-141">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-141">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-142">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-142">'Razor'</span></span>
- <span data-ttu-id="cfd64-143">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-143">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cfd64-144">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-144">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-145">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-145">'Blazor'</span></span>
- <span data-ttu-id="cfd64-146">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-146">'Identity'</span></span>
- <span data-ttu-id="cfd64-147">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-147">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-148">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-148">'Razor'</span></span>
- <span data-ttu-id="cfd64-149">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-149">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cfd64-150">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-151">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-151">'Blazor'</span></span>
- <span data-ttu-id="cfd64-152">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-152">'Identity'</span></span>
- <span data-ttu-id="cfd64-153">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-153">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-154">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-154">'Razor'</span></span>
- <span data-ttu-id="cfd64-155">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-155">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cfd64-156">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-157">'Blazor'</span></span>
- <span data-ttu-id="cfd64-158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-158">'Identity'</span></span>
- <span data-ttu-id="cfd64-159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-159">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-160">'Razor'</span></span>
- <span data-ttu-id="cfd64-161">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-161">'SignalR' uid:</span></span> 

<span data-ttu-id="cfd64-162">------- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-162">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-163">'Blazor'</span></span>
- <span data-ttu-id="cfd64-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-164">'Identity'</span></span>
- <span data-ttu-id="cfd64-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-166">'Razor'</span></span>
- <span data-ttu-id="cfd64-167">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cfd64-168">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-169">'Blazor'</span></span>
- <span data-ttu-id="cfd64-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-170">'Identity'</span></span>
- <span data-ttu-id="cfd64-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-172">'Razor'</span></span>
- <span data-ttu-id="cfd64-173">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cfd64-174">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-175">'Blazor'</span></span>
- <span data-ttu-id="cfd64-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-176">'Identity'</span></span>
- <span data-ttu-id="cfd64-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-178">'Razor'</span></span>
- <span data-ttu-id="cfd64-179">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-179">'SignalR' uid:</span></span> 

<span data-ttu-id="cfd64-180">------ | | [CompositeFileProvider](#compositefileprovider) | Utilizzato per fornire l'accesso combinato a file e directory da uno o più provider.</span><span class="sxs-lookup"><span data-stu-id="cfd64-180">------ | | [CompositeFileProvider](#compositefileprovider) | Used to provide combined access to files and directories from one or more other providers.</span></span> <span data-ttu-id="cfd64-181">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Utilizzato per accedere ai file incorporati negli assembly.</span><span class="sxs-lookup"><span data-stu-id="cfd64-181">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Used to access files embedded in assemblies.</span></span> <span data-ttu-id="cfd64-182">| | [PhysicalFileProvider](#physicalfileprovider) | Utilizzato per accedere ai file fisici del sistema.</span><span class="sxs-lookup"><span data-stu-id="cfd64-182">| | [PhysicalFileProvider](#physicalfileprovider) | Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="cfd64-183">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="cfd64-183">PhysicalFileProvider</span></span>

<span data-ttu-id="cfd64-184"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> consente di accedere al file system fisico.</span><span class="sxs-lookup"><span data-stu-id="cfd64-184">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="cfd64-185">`PhysicalFileProvider` usa il tipo <xref:System.IO.File?displayProperty=fullName> (per il provider fisico) e definisce una directory e i relativi elementi figlio come ambito per tutti i percorsi.</span><span class="sxs-lookup"><span data-stu-id="cfd64-185">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="cfd64-186">La definizione dell'ambito impedisce l'accesso al file system al di fuori della directory specificata e dei relativi elementi figlio.</span><span class="sxs-lookup"><span data-stu-id="cfd64-186">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="cfd64-187">Lo scenario più comune per la creazione e l'uso di un `PhysicalFileProvider` consiste nel richiedere un oggetto `IFileProvider` in un costruttore tramite [inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cfd64-187">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="cfd64-188">Quando si crea direttamente un'istanza di questo provider, viene richiesto un percorso di directory assoluto che funge da percorso di base per tutte le richieste effettuate tramite il provider.</span><span class="sxs-lookup"><span data-stu-id="cfd64-188">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="cfd64-189">I modelli Glob non sono supportati nel percorso della directory.</span><span class="sxs-lookup"><span data-stu-id="cfd64-189">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="cfd64-190">Nel codice seguente viene illustrato come utilizzare `PhysicalFileProvider` per ottenere il contenuto della directory e le informazioni sul file:</span><span class="sxs-lookup"><span data-stu-id="cfd64-190">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="cfd64-191">Tipi nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="cfd64-191">Types in the preceding example:</span></span>

* <span data-ttu-id="cfd64-192">`provider` è di tipo `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="cfd64-192">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="cfd64-193">`contents` è di tipo `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="cfd64-193">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="cfd64-194">`fileInfo` è di tipo `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="cfd64-194">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="cfd64-195">Il provider di file può essere usato per scorrere la directory specificata da `applicationRoot` oppure per chiamare `GetFileInfo` per ottenere informazioni su un file.</span><span class="sxs-lookup"><span data-stu-id="cfd64-195">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="cfd64-196">Non è possibile passare i modelli glob al `GetFileInfo` metodo.</span><span class="sxs-lookup"><span data-stu-id="cfd64-196">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="cfd64-197">Il provider di file non ha accesso all'esterno della directory `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="cfd64-197">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="cfd64-198">L'app di esempio *FileProviderSample* crea il provider nel `Startup.ConfigureServices` Metodo usando <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="cfd64-198">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="cfd64-199">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="cfd64-199">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="cfd64-200"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> consente di accedere ai file incorporati negli assembly.</span><span class="sxs-lookup"><span data-stu-id="cfd64-200">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="cfd64-201">`ManifestEmbeddedFileProvider` usa un manifesto compilato nell'assembly per ricostruire i percorsi originali dei file incorporati.</span><span class="sxs-lookup"><span data-stu-id="cfd64-201">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="cfd64-202">Per generare un manifesto dei file incorporati:</span><span class="sxs-lookup"><span data-stu-id="cfd64-202">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="cfd64-203">Aggiungere il pacchetto NuGet [Microsoft. Extensions. Fileprovides. Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) al progetto.</span><span class="sxs-lookup"><span data-stu-id="cfd64-203">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="cfd64-204">Impostare la proprietà `<GenerateEmbeddedFilesManifest>` su `true`.</span><span class="sxs-lookup"><span data-stu-id="cfd64-204">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="cfd64-205">Specificare i file da incorporare con [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) :</span><span class="sxs-lookup"><span data-stu-id="cfd64-205">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="cfd64-206">Usare [modelli GLOB](#glob-patterns) per specificare uno o più file da incorporare nell'assembly.</span><span class="sxs-lookup"><span data-stu-id="cfd64-206">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="cfd64-207">L'app di esempio *FileProviderSample* crea `ManifestEmbeddedFileProvider` e passa l'assembly attualmente in esecuzione al relativo costruttore.</span><span class="sxs-lookup"><span data-stu-id="cfd64-207">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="cfd64-208">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="cfd64-208">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="cfd64-209">Overload aggiuntivi consentono di:</span><span class="sxs-lookup"><span data-stu-id="cfd64-209">Additional overloads allow you to:</span></span>

* <span data-ttu-id="cfd64-210">Specificare un percorso di file relativo.</span><span class="sxs-lookup"><span data-stu-id="cfd64-210">Specify a relative file path.</span></span>
* <span data-ttu-id="cfd64-211">Definire la data dell'ultima modifica come ambito dei file.</span><span class="sxs-lookup"><span data-stu-id="cfd64-211">Scope files to a last modified date.</span></span>
* <span data-ttu-id="cfd64-212">Assegnare un nome alla risorsa incorporata contenente il manifesto dei file incorporati.</span><span class="sxs-lookup"><span data-stu-id="cfd64-212">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="cfd64-213">Overload</span><span class="sxs-lookup"><span data-stu-id="cfd64-213">Overload</span></span> | <span data-ttu-id="cfd64-214">Descrizione</span><span class="sxs-lookup"><span data-stu-id="cfd64-214">Description</span></span> |
| ---
<span data-ttu-id="cfd64-215">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-216">'Blazor'</span></span>
- <span data-ttu-id="cfd64-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-217">'Identity'</span></span>
- <span data-ttu-id="cfd64-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-219">'Razor'</span></span>
- <span data-ttu-id="cfd64-220">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cfd64-221">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-221">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-222">'Blazor'</span></span>
- <span data-ttu-id="cfd64-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-223">'Identity'</span></span>
- <span data-ttu-id="cfd64-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-225">'Razor'</span></span>
- <span data-ttu-id="cfd64-226">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-226">'SignalR' uid:</span></span> 

<span data-ttu-id="cfd64-227">---- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-227">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-228">'Blazor'</span></span>
- <span data-ttu-id="cfd64-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-229">'Identity'</span></span>
- <span data-ttu-id="cfd64-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-231">'Razor'</span></span>
- <span data-ttu-id="cfd64-232">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cfd64-233">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-233">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-234">'Blazor'</span></span>
- <span data-ttu-id="cfd64-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-235">'Identity'</span></span>
- <span data-ttu-id="cfd64-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-237">'Razor'</span></span>
- <span data-ttu-id="cfd64-238">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cfd64-239">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-239">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-240">'Blazor'</span></span>
- <span data-ttu-id="cfd64-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-241">'Identity'</span></span>
- <span data-ttu-id="cfd64-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-243">'Razor'</span></span>
- <span data-ttu-id="cfd64-244">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-244">'SignalR' uid:</span></span> 

<span data-ttu-id="cfd64-245">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accetta un `root` parametro di percorso relativo facoltativo.</span><span class="sxs-lookup"><span data-stu-id="cfd64-245">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="cfd64-246">Specificare `root` per definire come ambito delle chiamate a <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> le risorse incluse nel percorso specificato.</span><span class="sxs-lookup"><span data-stu-id="cfd64-246">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> <span data-ttu-id="cfd64-247">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accetta un `root` parametro di percorso relativo facoltativo e `lastModified` un parametro date ( <xref:System.DateTimeOffset> ).</span><span class="sxs-lookup"><span data-stu-id="cfd64-247">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="cfd64-248">La data `lastModified` definisce la data dell'ultima modifica come ambito per le istanze di <xref:Microsoft.Extensions.FileProviders.IFileInfo> restituite da <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="cfd64-248">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="cfd64-249">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accetta un `root` percorso relativo, una `lastModified` data e parametri facoltativi `manifestName` .</span><span class="sxs-lookup"><span data-stu-id="cfd64-249">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="cfd64-250">`manifestName` rappresenta il nome della risorsa incorporata contenente il manifesto.</span><span class="sxs-lookup"><span data-stu-id="cfd64-250">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="cfd64-251">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="cfd64-251">CompositeFileProvider</span></span>

<span data-ttu-id="cfd64-252"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combina le istanze `IFileProvider` esponendo una sola interfaccia per l'uso dei file di più provider.</span><span class="sxs-lookup"><span data-stu-id="cfd64-252">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="cfd64-253">Quando si crea `CompositeFileProvider`, passare una o più istanze `IFileProvider` al relativo costruttore.</span><span class="sxs-lookup"><span data-stu-id="cfd64-253">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="cfd64-254">Nell'app di esempio *FileProviderSample* , un `PhysicalFileProvider` e un `ManifestEmbeddedFileProvider` forniscono i file a un `CompositeFileProvider` registrato nel contenitore del servizio dell'app.</span><span class="sxs-lookup"><span data-stu-id="cfd64-254">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="cfd64-255">Il codice seguente si trova nel metodo del progetto `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="cfd64-255">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="cfd64-256">Controllo delle modifiche</span><span class="sxs-lookup"><span data-stu-id="cfd64-256">Watch for changes</span></span>

<span data-ttu-id="cfd64-257">Il <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> metodo fornisce uno scenario per controllare la presenza di modifiche in uno o più file o directory.</span><span class="sxs-lookup"><span data-stu-id="cfd64-257">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="cfd64-258">Il metodo `Watch`:</span><span class="sxs-lookup"><span data-stu-id="cfd64-258">The `Watch` method:</span></span>

* <span data-ttu-id="cfd64-259">Accetta una stringa di percorso del file, che può usare i [modelli glob](#glob-patterns) per specificare più file.</span><span class="sxs-lookup"><span data-stu-id="cfd64-259">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="cfd64-260">Restituisce un valore <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="cfd64-260">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="cfd64-261">Il token di modifica risultante espone:</span><span class="sxs-lookup"><span data-stu-id="cfd64-261">The resulting change token exposes:</span></span>

* <span data-ttu-id="cfd64-262"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Proprietà che può essere controllata per determinare se si è verificata una modifica.</span><span class="sxs-lookup"><span data-stu-id="cfd64-262"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="cfd64-263"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Viene chiamato quando vengono rilevate modifiche nella stringa di percorso specificata.</span><span class="sxs-lookup"><span data-stu-id="cfd64-263"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="cfd64-264">Ogni token di modifica chiama solo il relativo callback associato in risposta a una singola modifica.</span><span class="sxs-lookup"><span data-stu-id="cfd64-264">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="cfd64-265">Per abilitare un monitoraggio continuo, usare <xref:System.Threading.Tasks.TaskCompletionSource`1> come illustrato di seguito oppure ricreare istanze di `IChangeToken` in risposta alle modifiche.</span><span class="sxs-lookup"><span data-stu-id="cfd64-265">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="cfd64-266">L'app di esempio *WatchConsole* scrive un messaggio ogni volta che viene modificato un file con *estensione txt* nella directory *textfiles* :</span><span class="sxs-lookup"><span data-stu-id="cfd64-266">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="cfd64-267">È possibile che alcuni file system, ad esempio i contenitori Docker e le condivisioni di rete, non inviino sempre le notifiche di modifica.</span><span class="sxs-lookup"><span data-stu-id="cfd64-267">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="cfd64-268">Impostare la variabile di ambiente `DOTNET_USE_POLLING_FILE_WATCHER` su `1` o `true` per eseguire il polling delle modifiche nel file system ogni quattro secondi (intervallo non configurabile).</span><span class="sxs-lookup"><span data-stu-id="cfd64-268">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="cfd64-269">Modelli GLOB</span><span class="sxs-lookup"><span data-stu-id="cfd64-269">Glob patterns</span></span>

<span data-ttu-id="cfd64-270">Nei percorsi del file system vengono usati criteri con caratteri jolly chiamati *criteri GLOB (o globbing)*.</span><span class="sxs-lookup"><span data-stu-id="cfd64-270">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="cfd64-271">Specificare gruppi di file con questi criteri.</span><span class="sxs-lookup"><span data-stu-id="cfd64-271">Specify groups of files with these patterns.</span></span> <span data-ttu-id="cfd64-272">I due caratteri jolly sono `*` e `**`:</span><span class="sxs-lookup"><span data-stu-id="cfd64-272">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="cfd64-273">Cerca qualsiasi elemento a livello della cartella corrente, qualsiasi nome di file o qualsiasi estensione di file.</span><span class="sxs-lookup"><span data-stu-id="cfd64-273">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="cfd64-274">Le corrispondenze vengono terminate con i caratteri `/` e `.` nel percorso dei file.</span><span class="sxs-lookup"><span data-stu-id="cfd64-274">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="cfd64-275">Cerca qualsiasi elemento in più livelli di directory.</span><span class="sxs-lookup"><span data-stu-id="cfd64-275">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="cfd64-276">Può essere usato per cercare in modo ricorsivo numerosi file all'interno di una gerarchia di directory.</span><span class="sxs-lookup"><span data-stu-id="cfd64-276">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="cfd64-277">La tabella seguente fornisce esempi comuni di modelli glob.</span><span class="sxs-lookup"><span data-stu-id="cfd64-277">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="cfd64-278">Criterio</span><span class="sxs-lookup"><span data-stu-id="cfd64-278">Pattern</span></span>  |<span data-ttu-id="cfd64-279">Descrizione</span><span class="sxs-lookup"><span data-stu-id="cfd64-279">Description</span></span>  |
|---
<span data-ttu-id="cfd64-280">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-281">'Blazor'</span></span>
- <span data-ttu-id="cfd64-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-282">'Identity'</span></span>
- <span data-ttu-id="cfd64-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-284">'Razor'</span></span>
- <span data-ttu-id="cfd64-285">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cfd64-286">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-287">'Blazor'</span></span>
- <span data-ttu-id="cfd64-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-288">'Identity'</span></span>
- <span data-ttu-id="cfd64-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-290">'Razor'</span></span>
- <span data-ttu-id="cfd64-291">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-291">'SignalR' uid:</span></span> 

<span data-ttu-id="cfd64-292">-----|---
title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-292">-----|---
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-293">'Blazor'</span></span>
- <span data-ttu-id="cfd64-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-294">'Identity'</span></span>
- <span data-ttu-id="cfd64-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-296">'Razor'</span></span>
- <span data-ttu-id="cfd64-297">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cfd64-298">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-299">'Blazor'</span></span>
- <span data-ttu-id="cfd64-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-300">'Identity'</span></span>
- <span data-ttu-id="cfd64-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-302">'Razor'</span></span>
- <span data-ttu-id="cfd64-303">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-303">'SignalR' uid:</span></span> 

<span data-ttu-id="cfd64-304">-----|
|`directory/file.txt`| Corrisponde a un file specifico in una directory specifica. | |`directory/*.txt`| Corrisponde a tutti i file con estensione *txt* in una directory specifica. | |`directory/*/appsettings.json`| Corrisponde a tutti i file *appSettings. JSON* nelle directory esattamente un livello sotto la cartella *directory* . | |`directory/**/*.txt`| Corrisponde a tutti i file con estensione *txt* trovati in un punto qualsiasi della cartella della *directory* . |</span><span class="sxs-lookup"><span data-stu-id="cfd64-304">-----|
|`directory/file.txt`|Matches a specific file in a specific directory.| |`directory/*.txt`|Matches all files with *.txt* extension in a specific directory.| |`directory/*/appsettings.json`|Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.| |`directory/**/*.txt`|Matches all files with a *.txt* extension found anywhere under the *directory* folder.|</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cfd64-305">ASP.NET Core astrae l'accesso al file system tramite l'utilizzo di provider di file.</span><span class="sxs-lookup"><span data-stu-id="cfd64-305">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="cfd64-306">I provider di file vengono usati in tutto il framework di ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="cfd64-306">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="cfd64-307"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>espone la radice del [contenuto](xref:fundamentals/index#content-root) dell'app e la [radice Web](xref:fundamentals/index#web-root) come `IFileProvider` tipi.</span><span class="sxs-lookup"><span data-stu-id="cfd64-307"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="cfd64-308">Il [middleware dei file statici](xref:fundamentals/static-files) usa i provider di file per trovare i file statici.</span><span class="sxs-lookup"><span data-stu-id="cfd64-308">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="cfd64-309">[Razor](xref:mvc/views/razor)USA i provider di file per individuare le pagine e le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="cfd64-309">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="cfd64-310">Gli strumenti .NET Core usano i provider di file e i criteri GLOB per specificare i file da pubblicare.</span><span class="sxs-lookup"><span data-stu-id="cfd64-310">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="cfd64-311">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cfd64-311">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="cfd64-312">Interfacce del provider di file</span><span class="sxs-lookup"><span data-stu-id="cfd64-312">File Provider interfaces</span></span>

<span data-ttu-id="cfd64-313">L'interfaccia primaria è <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="cfd64-313">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="cfd64-314">`IFileProvider` espone metodi per:</span><span class="sxs-lookup"><span data-stu-id="cfd64-314">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="cfd64-315">Ottenere informazioni sui file (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="cfd64-315">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="cfd64-316">Ottenere informazioni sulle directory (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="cfd64-316">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="cfd64-317">Configurare le notifiche di modifica (usando un <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="cfd64-317">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="cfd64-318">`IFileInfo` fornisce metodi e proprietà per l'uso di file:</span><span class="sxs-lookup"><span data-stu-id="cfd64-318">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="cfd64-319"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in byte)</span><span class="sxs-lookup"><span data-stu-id="cfd64-319"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="cfd64-320">Data <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified></span><span class="sxs-lookup"><span data-stu-id="cfd64-320"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="cfd64-321">È possibile leggere dal file usando il metodo [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*).</span><span class="sxs-lookup"><span data-stu-id="cfd64-321">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="cfd64-322">L'app di esempio dimostra come configurare un provider di file in `Startup.ConfigureServices` da usare in tutta l'app tramite [inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cfd64-322">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="cfd64-323">Implementazioni dei provider di file</span><span class="sxs-lookup"><span data-stu-id="cfd64-323">File Provider implementations</span></span>

<span data-ttu-id="cfd64-324">Sono disponibili tre implementazioni di `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="cfd64-324">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="cfd64-325">Implementazione</span><span class="sxs-lookup"><span data-stu-id="cfd64-325">Implementation</span></span> | <span data-ttu-id="cfd64-326">Descrizione</span><span class="sxs-lookup"><span data-stu-id="cfd64-326">Description</span></span> |
| ---
<span data-ttu-id="cfd64-327">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-328">'Blazor'</span></span>
- <span data-ttu-id="cfd64-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-329">'Identity'</span></span>
- <span data-ttu-id="cfd64-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-331">'Razor'</span></span>
- <span data-ttu-id="cfd64-332">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cfd64-333">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-334">'Blazor'</span></span>
- <span data-ttu-id="cfd64-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-335">'Identity'</span></span>
- <span data-ttu-id="cfd64-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-337">'Razor'</span></span>
- <span data-ttu-id="cfd64-338">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cfd64-339">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-340">'Blazor'</span></span>
- <span data-ttu-id="cfd64-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-341">'Identity'</span></span>
- <span data-ttu-id="cfd64-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-343">'Razor'</span></span>
- <span data-ttu-id="cfd64-344">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cfd64-345">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-346">'Blazor'</span></span>
- <span data-ttu-id="cfd64-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-347">'Identity'</span></span>
- <span data-ttu-id="cfd64-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-349">'Razor'</span></span>
- <span data-ttu-id="cfd64-350">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cfd64-351">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-352">'Blazor'</span></span>
- <span data-ttu-id="cfd64-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-353">'Identity'</span></span>
- <span data-ttu-id="cfd64-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-355">'Razor'</span></span>
- <span data-ttu-id="cfd64-356">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-356">'SignalR' uid:</span></span> 

<span data-ttu-id="cfd64-357">------- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-357">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-358">'Blazor'</span></span>
- <span data-ttu-id="cfd64-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-359">'Identity'</span></span>
- <span data-ttu-id="cfd64-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-361">'Razor'</span></span>
- <span data-ttu-id="cfd64-362">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cfd64-363">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-364">'Blazor'</span></span>
- <span data-ttu-id="cfd64-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-365">'Identity'</span></span>
- <span data-ttu-id="cfd64-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-367">'Razor'</span></span>
- <span data-ttu-id="cfd64-368">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cfd64-369">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-370">'Blazor'</span></span>
- <span data-ttu-id="cfd64-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-371">'Identity'</span></span>
- <span data-ttu-id="cfd64-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-373">'Razor'</span></span>
- <span data-ttu-id="cfd64-374">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-374">'SignalR' uid:</span></span> 

<span data-ttu-id="cfd64-375">------ | | [PhysicalFileProvider](#physicalfileprovider) | Il provider fisico viene utilizzato per accedere ai file fisici del sistema.</span><span class="sxs-lookup"><span data-stu-id="cfd64-375">------ | | [PhysicalFileProvider](#physicalfileprovider) | The physical provider is used to access the system's physical files.</span></span> <span data-ttu-id="cfd64-376">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Il provider incorporato del manifesto viene usato per accedere ai file incorporati negli assembly.</span><span class="sxs-lookup"><span data-stu-id="cfd64-376">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | The manifest embedded provider is used to access files embedded in assemblies.</span></span> <span data-ttu-id="cfd64-377">| | [CompositeFileProvider](#compositefileprovider) | Il provider composito viene usato per fornire l'accesso combinato a file e directory da uno o più provider.</span><span class="sxs-lookup"><span data-stu-id="cfd64-377">| | [CompositeFileProvider](#compositefileprovider) | The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="cfd64-378">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="cfd64-378">PhysicalFileProvider</span></span>

<span data-ttu-id="cfd64-379"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> consente di accedere al file system fisico.</span><span class="sxs-lookup"><span data-stu-id="cfd64-379">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="cfd64-380">`PhysicalFileProvider` usa il tipo <xref:System.IO.File?displayProperty=fullName> (per il provider fisico) e definisce una directory e i relativi elementi figlio come ambito per tutti i percorsi.</span><span class="sxs-lookup"><span data-stu-id="cfd64-380">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="cfd64-381">La definizione dell'ambito impedisce l'accesso al file system al di fuori della directory specificata e dei relativi elementi figlio.</span><span class="sxs-lookup"><span data-stu-id="cfd64-381">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="cfd64-382">Lo scenario più comune per la creazione e l'uso di un `PhysicalFileProvider` consiste nel richiedere un oggetto `IFileProvider` in un costruttore tramite [inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cfd64-382">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="cfd64-383">Per la creazione diretta di un'istanza di questo provider, è richiesto un percorso di directory che viene usato come percorso di base per tutte le richieste effettuate tramite il provider.</span><span class="sxs-lookup"><span data-stu-id="cfd64-383">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="cfd64-384">Il codice seguente illustra come creare un `PhysicalFileProvider` e usarlo per ottenere il contenuto della directory e informazioni sui file:</span><span class="sxs-lookup"><span data-stu-id="cfd64-384">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="cfd64-385">Tipi nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="cfd64-385">Types in the preceding example:</span></span>

* <span data-ttu-id="cfd64-386">`provider` è di tipo `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="cfd64-386">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="cfd64-387">`contents` è di tipo `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="cfd64-387">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="cfd64-388">`fileInfo` è di tipo `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="cfd64-388">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="cfd64-389">Il provider di file può essere usato per scorrere la directory specificata da `applicationRoot` oppure per chiamare `GetFileInfo` per ottenere informazioni su un file.</span><span class="sxs-lookup"><span data-stu-id="cfd64-389">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="cfd64-390">Il provider di file non ha accesso all'esterno della directory `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="cfd64-390">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="cfd64-391">L'app di esempio crea il provider nella classe `Startup.ConfigureServices` dell'app usando [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="cfd64-391">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="cfd64-392">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="cfd64-392">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="cfd64-393"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> consente di accedere ai file incorporati negli assembly.</span><span class="sxs-lookup"><span data-stu-id="cfd64-393">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="cfd64-394">`ManifestEmbeddedFileProvider` usa un manifesto compilato nell'assembly per ricostruire i percorsi originali dei file incorporati.</span><span class="sxs-lookup"><span data-stu-id="cfd64-394">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="cfd64-395">Per generare un manifesto dei file incorporati, impostare la proprietà `<GenerateEmbeddedFilesManifest>` su `true`.</span><span class="sxs-lookup"><span data-stu-id="cfd64-395">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="cfd64-396">Specificare i file da incorporare con [ &lt; EmbeddedResource &gt; ](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="cfd64-396">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="cfd64-397">Usare [modelli GLOB](#glob-patterns) per specificare uno o più file da incorporare nell'assembly.</span><span class="sxs-lookup"><span data-stu-id="cfd64-397">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="cfd64-398">L'app di esempio crea un `ManifestEmbeddedFileProvider` e passa l'assembly attualmente in esecuzione al rispettivo costruttore.</span><span class="sxs-lookup"><span data-stu-id="cfd64-398">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="cfd64-399">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="cfd64-399">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="cfd64-400">Overload aggiuntivi consentono di:</span><span class="sxs-lookup"><span data-stu-id="cfd64-400">Additional overloads allow you to:</span></span>

* <span data-ttu-id="cfd64-401">Specificare un percorso di file relativo.</span><span class="sxs-lookup"><span data-stu-id="cfd64-401">Specify a relative file path.</span></span>
* <span data-ttu-id="cfd64-402">Definire la data dell'ultima modifica come ambito dei file.</span><span class="sxs-lookup"><span data-stu-id="cfd64-402">Scope files to a last modified date.</span></span>
* <span data-ttu-id="cfd64-403">Assegnare un nome alla risorsa incorporata contenente il manifesto dei file incorporati.</span><span class="sxs-lookup"><span data-stu-id="cfd64-403">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="cfd64-404">Overload</span><span class="sxs-lookup"><span data-stu-id="cfd64-404">Overload</span></span> | <span data-ttu-id="cfd64-405">Descrizione</span><span class="sxs-lookup"><span data-stu-id="cfd64-405">Description</span></span> |
| ---
<span data-ttu-id="cfd64-406">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-406">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-407">'Blazor'</span></span>
- <span data-ttu-id="cfd64-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-408">'Identity'</span></span>
- <span data-ttu-id="cfd64-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-410">'Razor'</span></span>
- <span data-ttu-id="cfd64-411">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cfd64-412">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-412">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-413">'Blazor'</span></span>
- <span data-ttu-id="cfd64-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-414">'Identity'</span></span>
- <span data-ttu-id="cfd64-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-416">'Razor'</span></span>
- <span data-ttu-id="cfd64-417">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-417">'SignalR' uid:</span></span> 

<span data-ttu-id="cfd64-418">---- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-418">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-419">'Blazor'</span></span>
- <span data-ttu-id="cfd64-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-420">'Identity'</span></span>
- <span data-ttu-id="cfd64-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-422">'Razor'</span></span>
- <span data-ttu-id="cfd64-423">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-423">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cfd64-424">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-424">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-425">'Blazor'</span></span>
- <span data-ttu-id="cfd64-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-426">'Identity'</span></span>
- <span data-ttu-id="cfd64-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-428">'Razor'</span></span>
- <span data-ttu-id="cfd64-429">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cfd64-430">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="cfd64-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cfd64-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-431">'Blazor'</span></span>
- <span data-ttu-id="cfd64-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cfd64-432">'Identity'</span></span>
- <span data-ttu-id="cfd64-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cfd64-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="cfd64-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cfd64-434">'Razor'</span></span>
- <span data-ttu-id="cfd64-435">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="cfd64-435">'SignalR' uid:</span></span> 

<span data-ttu-id="cfd64-436">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accetta un `root` parametro di percorso relativo facoltativo.</span><span class="sxs-lookup"><span data-stu-id="cfd64-436">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="cfd64-437">Specificare `root` per definire come ambito delle chiamate a <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> le risorse incluse nel percorso specificato.</span><span class="sxs-lookup"><span data-stu-id="cfd64-437">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> <span data-ttu-id="cfd64-438">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accetta un `root` parametro di percorso relativo facoltativo e `lastModified` un parametro date ( <xref:System.DateTimeOffset> ).</span><span class="sxs-lookup"><span data-stu-id="cfd64-438">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="cfd64-439">La data `lastModified` definisce la data dell'ultima modifica come ambito per le istanze di <xref:Microsoft.Extensions.FileProviders.IFileInfo> restituite da <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="cfd64-439">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="cfd64-440">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accetta un `root` percorso relativo, una `lastModified` data e parametri facoltativi `manifestName` .</span><span class="sxs-lookup"><span data-stu-id="cfd64-440">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="cfd64-441">`manifestName` rappresenta il nome della risorsa incorporata contenente il manifesto.</span><span class="sxs-lookup"><span data-stu-id="cfd64-441">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="cfd64-442">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="cfd64-442">CompositeFileProvider</span></span>

<span data-ttu-id="cfd64-443"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combina le istanze `IFileProvider` esponendo una sola interfaccia per l'uso dei file di più provider.</span><span class="sxs-lookup"><span data-stu-id="cfd64-443">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="cfd64-444">Quando si crea `CompositeFileProvider`, passare una o più istanze `IFileProvider` al relativo costruttore.</span><span class="sxs-lookup"><span data-stu-id="cfd64-444">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="cfd64-445">Nell'app di esempio, un `PhysicalFileProvider` e un `ManifestEmbeddedFileProvider` forniscono i file a un `CompositeFileProvider` registrato nel contenitore dei servizi dell'app:</span><span class="sxs-lookup"><span data-stu-id="cfd64-445">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="cfd64-446">Controllo delle modifiche</span><span class="sxs-lookup"><span data-stu-id="cfd64-446">Watch for changes</span></span>

<span data-ttu-id="cfd64-447">Il metodo [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) consente di controllare uno o più file o directory per il rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="cfd64-447">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="cfd64-448">`Watch` accetta una stringa di percorso in cui è possibile usare [criteri GLOB](#glob-patterns) per specificare più file.</span><span class="sxs-lookup"><span data-stu-id="cfd64-448">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="cfd64-449">`Watch` restituisce un oggetto <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="cfd64-449">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="cfd64-450">Il token di modifica espone:</span><span class="sxs-lookup"><span data-stu-id="cfd64-450">The change token exposes:</span></span>

* <span data-ttu-id="cfd64-451"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Proprietà che può essere controllata per determinare se si è verificata una modifica.</span><span class="sxs-lookup"><span data-stu-id="cfd64-451"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="cfd64-452"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Viene chiamato quando vengono rilevate modifiche nella stringa di percorso specificata.</span><span class="sxs-lookup"><span data-stu-id="cfd64-452"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="cfd64-453">Ogni token di modifica chiama solo il relativo callback associato in risposta a una singola modifica.</span><span class="sxs-lookup"><span data-stu-id="cfd64-453">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="cfd64-454">Per abilitare un monitoraggio continuo, usare <xref:System.Threading.Tasks.TaskCompletionSource`1> come illustrato di seguito oppure ricreare istanze di `IChangeToken` in risposta alle modifiche.</span><span class="sxs-lookup"><span data-stu-id="cfd64-454">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="cfd64-455">Nell'app di esempio, l'app console *WatchConsole* viene configurata per visualizzare un messaggio quando viene modificato un file di testo:</span><span class="sxs-lookup"><span data-stu-id="cfd64-455">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="cfd64-456">È possibile che alcuni file system, ad esempio i contenitori Docker e le condivisioni di rete, non inviino sempre le notifiche di modifica.</span><span class="sxs-lookup"><span data-stu-id="cfd64-456">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="cfd64-457">Impostare la variabile di ambiente `DOTNET_USE_POLLING_FILE_WATCHER` su `1` o `true` per eseguire il polling delle modifiche nel file system ogni quattro secondi (intervallo non configurabile).</span><span class="sxs-lookup"><span data-stu-id="cfd64-457">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="cfd64-458">Modelli GLOB</span><span class="sxs-lookup"><span data-stu-id="cfd64-458">Glob patterns</span></span>

<span data-ttu-id="cfd64-459">Nei percorsi del file system vengono usati criteri con caratteri jolly chiamati *criteri GLOB (o globbing)*.</span><span class="sxs-lookup"><span data-stu-id="cfd64-459">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="cfd64-460">Specificare gruppi di file con questi criteri.</span><span class="sxs-lookup"><span data-stu-id="cfd64-460">Specify groups of files with these patterns.</span></span> <span data-ttu-id="cfd64-461">I due caratteri jolly sono `*` e `**`:</span><span class="sxs-lookup"><span data-stu-id="cfd64-461">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="cfd64-462">Cerca qualsiasi elemento a livello della cartella corrente, qualsiasi nome di file o qualsiasi estensione di file.</span><span class="sxs-lookup"><span data-stu-id="cfd64-462">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="cfd64-463">Le corrispondenze vengono terminate con i caratteri `/` e `.` nel percorso dei file.</span><span class="sxs-lookup"><span data-stu-id="cfd64-463">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="cfd64-464">Cerca qualsiasi elemento in più livelli di directory.</span><span class="sxs-lookup"><span data-stu-id="cfd64-464">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="cfd64-465">Può essere usato per cercare in modo ricorsivo numerosi file all'interno di una gerarchia di directory.</span><span class="sxs-lookup"><span data-stu-id="cfd64-465">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="cfd64-466">**Esempi di criteri GLOB**</span><span class="sxs-lookup"><span data-stu-id="cfd64-466">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="cfd64-467">Cerca un file specifico in una directory specifica.</span><span class="sxs-lookup"><span data-stu-id="cfd64-467">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="cfd64-468">Cerca tutti i file con estensione *txt* in una directory specifica.</span><span class="sxs-lookup"><span data-stu-id="cfd64-468">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="cfd64-469">Cerca tutti i file `appsettings.json` nelle directory esattamente un livello sotto la cartella *directory*.</span><span class="sxs-lookup"><span data-stu-id="cfd64-469">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="cfd64-470">Cerca tutti i file con estensione *txt* che si trovano in qualsiasi posizione nella cartella *directory*.</span><span class="sxs-lookup"><span data-stu-id="cfd64-470">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
