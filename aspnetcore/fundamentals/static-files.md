---
title: File statici in ASP.NET Core
author: rick-anderson
description: Informazioni sull'uso, sulla protezione di file statici e sulla configurazione dei comportamenti del middleware che ospita i file statici nell'app Web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/static-files
ms.openlocfilehash: 95a77defc7e98328e1f4e3615648b1d14485e51e
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660125"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="6b728-103">File statici in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6b728-103">Static files in ASP.NET Core</span></span>

<span data-ttu-id="6b728-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="6b728-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="6b728-105">I file statici, ad esempio HTML, CSS, immagini e JavaScript, sono asset che un'app ASP.NET Core rende direttamente disponibili nei client.</span><span class="sxs-lookup"><span data-stu-id="6b728-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="6b728-106">Per abilitare l'uso di questi file, sono necessarie alcune configurazioni.</span><span class="sxs-lookup"><span data-stu-id="6b728-106">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="6b728-107">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6b728-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="6b728-108">Usare i file statici</span><span class="sxs-lookup"><span data-stu-id="6b728-108">Serve static files</span></span>

<span data-ttu-id="6b728-109">I file statici vengono memorizzati nella directory [radice Web](xref:fundamentals/index#web-root) del progetto.</span><span class="sxs-lookup"><span data-stu-id="6b728-109">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="6b728-110">La directory predefinita è *"content root"/wwwroot*, ma può essere modificata tramite il metodo [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) .</span><span class="sxs-lookup"><span data-stu-id="6b728-110">The default directory is *{content root}/wwwroot*, but it can be changed via the [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) method.</span></span> <span data-ttu-id="6b728-111">Vedere [Radice del contenuto](xref:fundamentals/index#content-root) e [Radice Web](xref:fundamentals/index#web-root) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="6b728-111">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="6b728-112">L'host Web dell'app deve conoscere la directory radice del contenuto.</span><span class="sxs-lookup"><span data-stu-id="6b728-112">The app's web host must be made aware of the content root directory.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="6b728-113">Il metodo `WebHost.CreateDefaultBuilder` imposta la radice del contenuto nella directory corrente:</span><span class="sxs-lookup"><span data-stu-id="6b728-113">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="6b728-114">Impostare la radice del contenuto nella directory corrente richiamando [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) all'interno di `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="6b728-114">Set the content root to the current directory by invoking [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) inside of `Program.Main`:</span></span>

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

<span data-ttu-id="6b728-115">I file statici sono accessibili tramite un percorso relativo alla [radice Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="6b728-115">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="6b728-116">Ad esempio, il modello di progetto **Applicazione Web** contiene varie cartelle all'interno della cartella *wwwroot*:</span><span class="sxs-lookup"><span data-stu-id="6b728-116">For example, the **Web Application** project template contains several folders within the *wwwroot* folder:</span></span>

* <span data-ttu-id="6b728-117">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="6b728-117">**wwwroot**</span></span>
  * <span data-ttu-id="6b728-118">**css**</span><span class="sxs-lookup"><span data-stu-id="6b728-118">**css**</span></span>
  * <span data-ttu-id="6b728-119">**Immagini**</span><span class="sxs-lookup"><span data-stu-id="6b728-119">**images**</span></span>
  * <span data-ttu-id="6b728-120">**Js**</span><span class="sxs-lookup"><span data-stu-id="6b728-120">**js**</span></span>

<span data-ttu-id="6b728-121">Il formato dell'URI per accedere a un file nella sottocartella *images* (immagini) è *http://\<indirizzo_server > /images/\<nome_file_immagine >*.</span><span class="sxs-lookup"><span data-stu-id="6b728-121">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="6b728-122">Ad esempio, *http://localhost:9189/images/banner3.svg*.</span><span class="sxs-lookup"><span data-stu-id="6b728-122">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="6b728-123">Se la destinazione è .NET Framework, aggiungere il pacchetto [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) al progetto.</span><span class="sxs-lookup"><span data-stu-id="6b728-123">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="6b728-124">Se la destinazione è .NET Core, questo pacchetto è incluso nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="6b728-124">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="6b728-125">Se la destinazione è .NET Framework, aggiungere il pacchetto [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) al progetto.</span><span class="sxs-lookup"><span data-stu-id="6b728-125">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="6b728-126">Se la destinazione è .NET Core, questo pacchetto è incluso in [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="6b728-126">If targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) includes this package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="6b728-127">Aggiungere il pacchetto [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) al progetto.</span><span class="sxs-lookup"><span data-stu-id="6b728-127">Add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span>

::: moniker-end

<span data-ttu-id="6b728-128">Configurare il [middleware](xref:fundamentals/middleware/index) che consente l'uso di file statici.</span><span class="sxs-lookup"><span data-stu-id="6b728-128">Configure the [middleware](xref:fundamentals/middleware/index) which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="6b728-129">Usare i file all'interno della radice Web</span><span class="sxs-lookup"><span data-stu-id="6b728-129">Serve files inside of web root</span></span>

<span data-ttu-id="6b728-130">Richiamare il metodo [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) all'interno di `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="6b728-130">Invoke the [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="6b728-131">L'overload `UseStaticFiles` del metodo senza parametri contrassegna i file nella [radice Web](xref:fundamentals/index#web-root) come servable.</span><span class="sxs-lookup"><span data-stu-id="6b728-131">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="6b728-132">Il markup seguente si riferisce a *wwwroot/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="6b728-132">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="6b728-133">Nel codice precedente, il carattere `~/` tilde punta alla [radice Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="6b728-133">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="6b728-134">Usare i file all'esterno della radice Web</span><span class="sxs-lookup"><span data-stu-id="6b728-134">Serve files outside of web root</span></span>

<span data-ttu-id="6b728-135">Si consideri una gerarchia di directory in cui i file statici da servire risiedono all'esterno della [radice Web](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="6b728-135">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* <span data-ttu-id="6b728-136">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="6b728-136">**wwwroot**</span></span>
  * <span data-ttu-id="6b728-137">**css**</span><span class="sxs-lookup"><span data-stu-id="6b728-137">**css**</span></span>
  * <span data-ttu-id="6b728-138">**Immagini**</span><span class="sxs-lookup"><span data-stu-id="6b728-138">**images**</span></span>
  * <span data-ttu-id="6b728-139">**Js**</span><span class="sxs-lookup"><span data-stu-id="6b728-139">**js**</span></span>
* <span data-ttu-id="6b728-140">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="6b728-140">**MyStaticFiles**</span></span>
  * <span data-ttu-id="6b728-141">**Immagini**</span><span class="sxs-lookup"><span data-stu-id="6b728-141">**images**</span></span>
    * <span data-ttu-id="6b728-142">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="6b728-142">*banner1.svg*</span></span>

<span data-ttu-id="6b728-143">Una richiesta può accedere al file *banner1.svg* configurando il middleware dei file statici come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="6b728-143">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="6b728-144">Nel codice precedente la gerarchia di directory *MyStaticFiles* viene esposta pubblicamente tramite il segmento dell'URI *StaticFiles*.</span><span class="sxs-lookup"><span data-stu-id="6b728-144">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="6b728-145">Una richiesta a *http://\<indirizzo_server > /StaticFiles/images/banner1.svg* usa il file *banner1.svg*.</span><span class="sxs-lookup"><span data-stu-id="6b728-145">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="6b728-146">Il markup seguente si riferisce a *MyStaticFiles/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="6b728-146">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="6b728-147">Impostare le intestazioni della risposta HTTP</span><span class="sxs-lookup"><span data-stu-id="6b728-147">Set HTTP response headers</span></span>

<span data-ttu-id="6b728-148">Per impostare le intestazioni della risposta HTTP, è possibile usare un oggetto [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions).</span><span class="sxs-lookup"><span data-stu-id="6b728-148">A [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) object can be used to set HTTP response headers.</span></span> <span data-ttu-id="6b728-149">Oltre a configurare i file statici che servono `Cache-Control` dalla radice [Web](xref:fundamentals/index#web-root), il codice seguente imposta l'intestazione:</span><span class="sxs-lookup"><span data-stu-id="6b728-149">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="6b728-150">Il metodo [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) è disponibile nel pacchetto [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).</span><span class="sxs-lookup"><span data-stu-id="6b728-150">The [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="6b728-151">I file sono stati resi pubblicamente inseribili nella cache per 10 minuti (600 secondi) nell'ambiente di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="6b728-151">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Sono state aggiunte le intestazioni della risposta che visualizzano l'intestazione Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="6b728-153">Autorizzazione dei file statici</span><span class="sxs-lookup"><span data-stu-id="6b728-153">Static file authorization</span></span>

<span data-ttu-id="6b728-154">Il middleware dei file statici non offre controlli di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="6b728-154">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="6b728-155">I file usati dal middleware, inclusi i file in *wwwroot*, sono disponibili pubblicamente.</span><span class="sxs-lookup"><span data-stu-id="6b728-155">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="6b728-156">Per usare i file in base alle autorizzazioni:</span><span class="sxs-lookup"><span data-stu-id="6b728-156">To serve files based on authorization:</span></span>

* <span data-ttu-id="6b728-157">Archiviare i file all'esterno di *wwwroot* e di qualsiasi directory alla quale può accedere il middleware dei file statici.</span><span class="sxs-lookup"><span data-stu-id="6b728-157">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="6b728-158">Usarli tramite un metodo di azione al quale è applicata l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="6b728-158">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="6b728-159">Restituire un oggetto [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult):</span><span class="sxs-lookup"><span data-stu-id="6b728-159">Return a [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) object:</span></span>

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="6b728-160">Abilitare l'esplorazione directory</span><span class="sxs-lookup"><span data-stu-id="6b728-160">Enable directory browsing</span></span>

<span data-ttu-id="6b728-161">L'esplorazione directory consente agli utenti dell'app Web di visualizzare un elenco di directory e i file all'interno di una directory specifica.</span><span class="sxs-lookup"><span data-stu-id="6b728-161">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="6b728-162">Per motivi di sicurezza, l'esplorazione directory è disabilitata per impostazione predefinita (vedere [Considerazioni](#considerations)).</span><span class="sxs-lookup"><span data-stu-id="6b728-162">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="6b728-163">Abilitare l'esplorazione directory richiamando il metodo [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) in `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="6b728-163">Enable directory browsing by invoking the [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="6b728-164">Aggiungere i servizi necessari richiamando il metodo [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) da `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6b728-164">Add required services by invoking the [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="6b728-165">Il codice precedente consente l'esplorazione directory della cartella *wwwroot/images* tramite l'URL *http://\<indirizzo_server >/MyImages*, con collegamenti ai singoli file e cartelle:</span><span class="sxs-lookup"><span data-stu-id="6b728-165">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![esplorazione directory](static-files/_static/dir-browse.png)

<span data-ttu-id="6b728-167">Vedere la sezione [Considerazioni](#considerations) per conoscere i rischi per la sicurezza quando l'esplorazione viene abilitata.</span><span class="sxs-lookup"><span data-stu-id="6b728-167">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="6b728-168">Si notino le due chiamate `UseStaticFiles` nell'esempio seguente.</span><span class="sxs-lookup"><span data-stu-id="6b728-168">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="6b728-169">La prima chiamata consente l'uso di file statici nella cartella *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="6b728-169">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="6b728-170">La seconda chiamata consente l'esplorazione directory nella cartella *wwwroot/images* tramite l'URL *http://\<indirizzo_server >/MyImages*:</span><span class="sxs-lookup"><span data-stu-id="6b728-170">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="6b728-171">Usare un documento predefinito</span><span class="sxs-lookup"><span data-stu-id="6b728-171">Serve a default document</span></span>

<span data-ttu-id="6b728-172">L'impostazione di una home page predefinita offre ai visitatori un punto di partenza logico per la visita del sito.</span><span class="sxs-lookup"><span data-stu-id="6b728-172">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="6b728-173">Per usare una pagina predefinita senza che l'utente debba specificare in modo completo l'URI, chiamare il metodo [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) da `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="6b728-173">To serve a default page without the user fully qualifying the URI, call the [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="6b728-174">`UseDefaultFiles` deve essere chiamato prima di `UseStaticFiles` per usare il file predefinito.</span><span class="sxs-lookup"><span data-stu-id="6b728-174">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="6b728-175">`UseDefaultFiles` è un URL rewriter che effettivamente non usa il file.</span><span class="sxs-lookup"><span data-stu-id="6b728-175">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="6b728-176">Abilitare il middleware dei file statici tramite `UseStaticFiles` per usare il file.</span><span class="sxs-lookup"><span data-stu-id="6b728-176">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="6b728-177">Con `UseDefaultFiles`, si richiede la ricerca nella cartella degli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="6b728-177">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="6b728-178">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="6b728-178">*default.htm*</span></span>
* <span data-ttu-id="6b728-179">*default.html*</span><span class="sxs-lookup"><span data-stu-id="6b728-179">*default.html*</span></span>
* <span data-ttu-id="6b728-180">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="6b728-180">*index.htm*</span></span>
* <span data-ttu-id="6b728-181">*index.html (informazioni in lingua inlingua in stato*</span><span class="sxs-lookup"><span data-stu-id="6b728-181">*index.html*</span></span>

<span data-ttu-id="6b728-182">Il primo file trovato nell'elenco viene usato come se la richiesta fosse l'URI completo.</span><span class="sxs-lookup"><span data-stu-id="6b728-182">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="6b728-183">L'URL del browser continua a riflettere l'URI richiesto.</span><span class="sxs-lookup"><span data-stu-id="6b728-183">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="6b728-184">Il codice seguente modifica il nome file predefinito in *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="6b728-184">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="6b728-185">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="6b728-185">UseFileServer</span></span>

<span data-ttu-id="6b728-186"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>combina le funzionalità `UseStaticFiles` `UseDefaultFiles`di , `UseDirectoryBrowser`, e, facoltativamente, .</span><span class="sxs-lookup"><span data-stu-id="6b728-186"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="6b728-187">Il codice seguente consente di usare i file statici e il file predefinito.</span><span class="sxs-lookup"><span data-stu-id="6b728-187">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="6b728-188">L'esplorazione directory non è abilitata.</span><span class="sxs-lookup"><span data-stu-id="6b728-188">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="6b728-189">Il codice seguente si basa sull'overload senza parametri, consentendo l'esplorazione directory:</span><span class="sxs-lookup"><span data-stu-id="6b728-189">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="6b728-190">Considerare la gerarchia di directory seguente:</span><span class="sxs-lookup"><span data-stu-id="6b728-190">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="6b728-191">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="6b728-191">**wwwroot**</span></span>
  * <span data-ttu-id="6b728-192">**css**</span><span class="sxs-lookup"><span data-stu-id="6b728-192">**css**</span></span>
  * <span data-ttu-id="6b728-193">**Immagini**</span><span class="sxs-lookup"><span data-stu-id="6b728-193">**images**</span></span>
  * <span data-ttu-id="6b728-194">**Js**</span><span class="sxs-lookup"><span data-stu-id="6b728-194">**js**</span></span>
* <span data-ttu-id="6b728-195">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="6b728-195">**MyStaticFiles**</span></span>
  * <span data-ttu-id="6b728-196">**Immagini**</span><span class="sxs-lookup"><span data-stu-id="6b728-196">**images**</span></span>
    * <span data-ttu-id="6b728-197">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="6b728-197">*banner1.svg*</span></span>
  * <span data-ttu-id="6b728-198">*default.html*</span><span class="sxs-lookup"><span data-stu-id="6b728-198">*default.html*</span></span>

<span data-ttu-id="6b728-199">Il codice seguente abilita i file statici, i file predefiniti e l'esplorazione directory di `MyStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="6b728-199">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="6b728-200">Chiamare il metodo `AddDirectoryBrowser` quando il valore della proprietà `EnableDirectoryBrowsing` è `true`:</span><span class="sxs-lookup"><span data-stu-id="6b728-200">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="6b728-201">Dall'uso della gerarchia di file e del codice precedente ne deriva quanto segue:</span><span class="sxs-lookup"><span data-stu-id="6b728-201">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="6b728-202">URI</span><span class="sxs-lookup"><span data-stu-id="6b728-202">URI</span></span>            |                             <span data-ttu-id="6b728-203">Risposta</span><span class="sxs-lookup"><span data-stu-id="6b728-203">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="6b728-204">*http://\<server_address>/StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="6b728-204">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="6b728-205">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="6b728-205">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="6b728-206">*http://\<server_address>/StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="6b728-206">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="6b728-207">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="6b728-207">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="6b728-208">Se non esiste un file predefinito nella directory *MyStaticFiles*, *http://\<indirizzo_server > / StaticFiles* restituisce l'elenco di directory con i collegamenti su cui è possibile fare clic:</span><span class="sxs-lookup"><span data-stu-id="6b728-208">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Elenco di file statici](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="6b728-210"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> e <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> eseguono un reindirizzamento sul lato client da `http://{SERVER ADDRESS}/StaticFiles` (senza barra rovesciata) a `http://{SERVER ADDRESS}/StaticFiles/` (con barra rovesciata).</span><span class="sxs-lookup"><span data-stu-id="6b728-210"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="6b728-211">Gli URL relativi all'interno della directory *StaticFiles* non sono validi senza barra finale.</span><span class="sxs-lookup"><span data-stu-id="6b728-211">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="6b728-212">Classe FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="6b728-212">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="6b728-213">La classe [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) contiene una proprietà `Mappings` che può essere usata come mapping di estensioni di file nei tipi di contenuto MIME.</span><span class="sxs-lookup"><span data-stu-id="6b728-213">The [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="6b728-214">Nell'esempio seguente varie estensioni di file vengono registrate in tipi MIME noti.</span><span class="sxs-lookup"><span data-stu-id="6b728-214">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="6b728-215">L'estensione *rtf* viene sostituita e l'estensione *mp4* viene rimossa.</span><span class="sxs-lookup"><span data-stu-id="6b728-215">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="6b728-216">Vedere [Tipi di contenuto MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="6b728-216">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="6b728-217">Tipi di contenuto non standard</span><span class="sxs-lookup"><span data-stu-id="6b728-217">Non-standard content types</span></span>

<span data-ttu-id="6b728-218">Il middleware dei file statici riconosce almeno 400 tipi di contenuto file noti.</span><span class="sxs-lookup"><span data-stu-id="6b728-218">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="6b728-219">Se viene richiesto un file con un tipo di file non noto, il middleware dei file statici passa la richiesta al middleware successivo nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="6b728-219">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="6b728-220">Se nessun middleware gestisce la richiesta, viene restituita la risposta *404 Non trovato*.</span><span class="sxs-lookup"><span data-stu-id="6b728-220">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="6b728-221">Se è abilitata l'esplorazione directory, viene visualizzato un collegamento al file nella visualizzazione directory.</span><span class="sxs-lookup"><span data-stu-id="6b728-221">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="6b728-222">Il codice seguente consente di usare tipi sconosciuti ed esegue il rendering del file sconosciuto come immagine:</span><span class="sxs-lookup"><span data-stu-id="6b728-222">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="6b728-223">Con il codice precedente, una richiesta per un file con un tipo di contenuto sconosciuto viene restituita come immagine.</span><span class="sxs-lookup"><span data-stu-id="6b728-223">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="6b728-224">L'abilitazione di [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) costituisce un rischio per la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="6b728-224">Enabling [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) is a security risk.</span></span> <span data-ttu-id="6b728-225">È disabilitato per impostazione predefinita e ne è sconsigliato l'uso.</span><span class="sxs-lookup"><span data-stu-id="6b728-225">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="6b728-226">La classe [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) offre un'alternativa più sicura per usare i file con estensioni non standard.</span><span class="sxs-lookup"><span data-stu-id="6b728-226">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="6b728-227">Servire file da più posizioni</span><span class="sxs-lookup"><span data-stu-id="6b728-227">Serve files from multiple locations</span></span>

<span data-ttu-id="6b728-228">`UseStaticFiles`e `UseFileServer` il valore predefinito è il provider di file che punta a *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="6b728-228">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="6b728-229">È possibile fornire `UseStaticFiles` istanze aggiuntive di e `UseFileServer` con altri provider di file per la gestione di file da altre posizioni.</span><span class="sxs-lookup"><span data-stu-id="6b728-229">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="6b728-230">Per altre informazioni, vedere [questo problema in GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="6b728-230">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="6b728-231">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="6b728-231">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="6b728-232">L'uso di `UseDirectoryBrowser` e `UseStaticFiles` può comportare la perdita di informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="6b728-232">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="6b728-233">È consigliabile disabilitare l'esplorazione directory nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="6b728-233">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="6b728-234">Controllare attentamente quali sono le directory abilitate tramite `UseStaticFiles` o `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="6b728-234">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="6b728-235">L'intera directory e le relative sottodirectory diventano pubblicamente accessibili.</span><span class="sxs-lookup"><span data-stu-id="6b728-235">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="6b728-236">Archiviare i file adatti per servire al pubblico in una directory dedicata, ad \* \<esempio content_root>/wwwroot\*.</span><span class="sxs-lookup"><span data-stu-id="6b728-236">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="6b728-237">Tenere questi file separati da visualizzazioni MVC, Razor Pages (solo versione 2.x), file di configurazione e così via.</span><span class="sxs-lookup"><span data-stu-id="6b728-237">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="6b728-238">Gli URL per il contenuto esposto con `UseDirectoryBrowser` e `UseStaticFiles` sono soggetti a distinzione tra maiuscole e minuscole e a limitazione di caratteri del file system sottostante.</span><span class="sxs-lookup"><span data-stu-id="6b728-238">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="6b728-239">Ad esempio, in Windows viene fatta distinzione tra maiuscole e minuscole, al contrario di quanto avviene in macOS e Linux.</span><span class="sxs-lookup"><span data-stu-id="6b728-239">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="6b728-240">Le app ASP.NET Core ospitate in IIS usano il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) per inoltrare tutte le richieste all'app, incluse le richieste di file statici.</span><span class="sxs-lookup"><span data-stu-id="6b728-240">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="6b728-241">Non viene usato il gestore di file statici di IIS.</span><span class="sxs-lookup"><span data-stu-id="6b728-241">The IIS static file handler isn't used.</span></span> <span data-ttu-id="6b728-242">Non è possibile gestire le richieste se queste non sono state prima gestite dal modulo.</span><span class="sxs-lookup"><span data-stu-id="6b728-242">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="6b728-243">Completare la procedura seguente in Gestione IIS per rimuovere il gestore di file statici di IIS a livello di server o di sito Web:</span><span class="sxs-lookup"><span data-stu-id="6b728-243">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="6b728-244">Passare alla funzionalità **Moduli**.</span><span class="sxs-lookup"><span data-stu-id="6b728-244">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="6b728-245">Selezionare **StaticFileModule** nell'elenco.</span><span class="sxs-lookup"><span data-stu-id="6b728-245">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="6b728-246">Fare clic su **Rimuovi** nell'intestazione laterale **Azioni**.</span><span class="sxs-lookup"><span data-stu-id="6b728-246">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="6b728-247">Se il gestore di file statici di IIS è abilitato **e** il modulo ASP.NET Core non è configurato correttamente, vengono usati i file statici.</span><span class="sxs-lookup"><span data-stu-id="6b728-247">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="6b728-248">Tale scenario si verifica ad esempio se il file *web.config* non è stato distribuito.</span><span class="sxs-lookup"><span data-stu-id="6b728-248">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="6b728-249">Inserire i file di codice (inclusi *.cs* e *.cshtml)* all'esterno della [radice Web](xref:fundamentals/index#web-root)del progetto app.</span><span class="sxs-lookup"><span data-stu-id="6b728-249">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="6b728-250">Si crea quindi un separazione logica tra il contenuto sul lato client dell'app e il codice basato su server.</span><span class="sxs-lookup"><span data-stu-id="6b728-250">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="6b728-251">In questo modo si impedisce la perdita del codice sul lato server.</span><span class="sxs-lookup"><span data-stu-id="6b728-251">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6b728-252">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="6b728-252">Additional resources</span></span>

* [<span data-ttu-id="6b728-253">Middleware</span><span class="sxs-lookup"><span data-stu-id="6b728-253">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="6b728-254">Introduzione ad ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6b728-254">Introduction to ASP.NET Core</span></span>](xref:index)
