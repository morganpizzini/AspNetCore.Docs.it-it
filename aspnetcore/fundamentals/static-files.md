---
title: File statici in ASP.NET Core
author: rick-anderson
description: Informazioni sull'uso, sulla protezione di file statici e sulla configurazione dei comportamenti del middleware che ospita i file statici nell'app Web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: 36eeb3532ed5ea6f5f75c20cbe14331ed840a788
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393938"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="a8bfc-103">File statici in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a8bfc-103">Static files in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a8bfc-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="a8bfc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="a8bfc-105">I file statici, ad esempio HTML, CSS, immagini e JavaScript, sono asset che un'app ASP.NET Core serve direttamente ai client per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients by default.</span></span>

<span data-ttu-id="a8bfc-106">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a8bfc-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="a8bfc-107">Usare i file statici</span><span class="sxs-lookup"><span data-stu-id="a8bfc-107">Serve static files</span></span>

<span data-ttu-id="a8bfc-108">I file statici vengono archiviati nella directory [radice Web](xref:fundamentals/index#web-root) del progetto.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-108">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="a8bfc-109">La directory predefinita è `{content root}/wwwroot` , ma può essere modificata con il <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> metodo.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-109">The default directory is `{content root}/wwwroot`, but it can be changed with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="a8bfc-110">Per altre informazioni, vedere [radice del contenuto](xref:fundamentals/index#content-root) e [radice Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="a8bfc-110">For more information, see [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="a8bfc-111">Il metodo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> imposta la radice del contenuto nella directory corrente:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-111">The <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> method sets the content root to the current directory:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

<span data-ttu-id="a8bfc-112">Il codice precedente è stato creato con il modello di app Web.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-112">The preceding code was created with the web app template.</span></span>

<span data-ttu-id="a8bfc-113">I file statici sono accessibili tramite un percorso relativo alla [radice Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="a8bfc-113">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="a8bfc-114">Ad esempio, i modelli di progetto di **applicazione Web** contengono diverse cartelle all'interno della `wwwroot` cartella:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-114">For example, the **Web Application** project templates contain several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `js`
  * `lib`

<span data-ttu-id="a8bfc-115">Si consiglia di creare la cartella *wwwroot/images* e di aggiungere il file *wwwroot/images/MyImage.jpg* .</span><span class="sxs-lookup"><span data-stu-id="a8bfc-115">Consider creating the *wwwroot/images* folder and adding the *wwwroot/images/MyImage.jpg* file.</span></span> <span data-ttu-id="a8bfc-116">Il formato dell'URI per accedere a un file nella `images` cartella è `https://<hostname>/images/<image_file_name>` .</span><span class="sxs-lookup"><span data-stu-id="a8bfc-116">The URI format to access a file in the `images` folder is `https://<hostname>/images/<image_file_name>`.</span></span> <span data-ttu-id="a8bfc-117">Ad esempio, usare `https://localhost:5001/images/MyImage.jpg`</span><span class="sxs-lookup"><span data-stu-id="a8bfc-117">For example, `https://localhost:5001/images/MyImage.jpg`</span></span>

### <a name="serve-files-in-web-root"></a><span data-ttu-id="a8bfc-118">Gestire i file nella radice Web</span><span class="sxs-lookup"><span data-stu-id="a8bfc-118">Serve files in web root</span></span>

<span data-ttu-id="a8bfc-119">I modelli di app Web predefiniti chiamano il <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> metodo in `Startup.Configure` , che consente di servire i file statici:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-119">The default web app templates call the <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> method in `Startup.Configure`, which enables static files to be served:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="a8bfc-120">L'overload del `UseStaticFiles` metodo senza parametri contrassegna i file nella [radice Web](xref:fundamentals/index#web-root) come servable.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-120">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="a8bfc-121">Il markup seguente fa riferimento a *wwwroot/images/MyImage.jpg*:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-121">The following markup references *wwwroot/images/MyImage.jpg*:</span></span>

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

<span data-ttu-id="a8bfc-122">Nel codice precedente, il carattere tilde `~/` punta alla [radice Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="a8bfc-122">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="a8bfc-123">Usare i file all'esterno della radice Web</span><span class="sxs-lookup"><span data-stu-id="a8bfc-123">Serve files outside of web root</span></span>

<span data-ttu-id="a8bfc-124">Si consideri una gerarchia di directory in cui i file statici da servire si trovano all'esterno della [radice Web](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="a8bfc-124">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

<span data-ttu-id="a8bfc-125">Una richiesta può accedere al `red-rose.jpg` file configurando il middleware dei file statici come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-125">A request can access the `red-rose.jpg` file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

<span data-ttu-id="a8bfc-126">Nel codice precedente la gerarchia di directory *MyStaticFiles* viene esposta pubblicamente tramite il segmento dell'URI *StaticFiles*.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-126">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="a8bfc-127">Richiesta di `https://<hostname>/StaticFiles/images/red-rose.jpg` servizio per il file di *red-rose.jpg* .</span><span class="sxs-lookup"><span data-stu-id="a8bfc-127">A request to `https://<hostname>/StaticFiles/images/red-rose.jpg` serves the *red-rose.jpg* file.</span></span>

<span data-ttu-id="a8bfc-128">Il markup seguente fa riferimento a *MyStaticFiles/images/red-rose.jpg*:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-128">The following markup references *MyStaticFiles/images/red-rose.jpg*:</span></span>

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a><span data-ttu-id="a8bfc-129">Impostare le intestazioni della risposta HTTP</span><span class="sxs-lookup"><span data-stu-id="a8bfc-129">Set HTTP response headers</span></span>

<span data-ttu-id="a8bfc-130">Un <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> oggetto può essere utilizzato per impostare le intestazioni di risposta http.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-130">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="a8bfc-131">Oltre alla configurazione del file statico che funge dalla [radice Web](xref:fundamentals/index#web-root), il codice seguente imposta l' `Cache-Control` intestazione:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-131">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

<span data-ttu-id="a8bfc-132">I file statici possono essere memorizzati nella cache pubblicamente per 600 secondi:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-132">Static files are publicly cacheable for 600 seconds:</span></span>

![Sono state aggiunte le intestazioni della risposta che visualizzano l'intestazione Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="a8bfc-134">Autorizzazione dei file statici</span><span class="sxs-lookup"><span data-stu-id="a8bfc-134">Static file authorization</span></span>

<span data-ttu-id="a8bfc-135">Il middleware dei file statici non offre controlli di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-135">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="a8bfc-136">Tutti i file serviti, inclusi quelli in `wwwroot` , sono accessibili pubblicamente.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-136">Any files served by it, including those under `wwwroot`, are publicly accessible.</span></span> <span data-ttu-id="a8bfc-137">Per usare i file in base alle autorizzazioni:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-137">To serve files based on authorization:</span></span>

* <span data-ttu-id="a8bfc-138">Archiviarli all'esterno di `wwwroot` e di qualsiasi directory accessibile al middleware dei file statici predefiniti.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-138">Store them outside of `wwwroot` and any directory accessible to the default Static File Middleware.</span></span>
* <span data-ttu-id="a8bfc-139">Chiamare `UseStaticFiles` dopo `UseAuthorization` e specificare il percorso:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-139">Call `UseStaticFiles` after `UseAuthorization` and specify the path:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet2)]
  
  <span data-ttu-id="a8bfc-140">Per l'approccio precedente è necessario che gli utenti siano autenticati:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-140">The preceding approach requires users to be authenticated:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet1&highlight=20-99)]

   [!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="a8bfc-141">Un approccio alternativo per gestire i file in base all'autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-141">An alternative approach to serve files based on authorization:</span></span>

* <span data-ttu-id="a8bfc-142">Archiviarli all'esterno di `wwwroot` e di qualsiasi directory accessibile al middleware di file statici.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-142">Store them outside of `wwwroot` and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="a8bfc-143">Servirle tramite un metodo di azione a cui viene applicata l'autorizzazione e restituire un <xref:Microsoft.AspNetCore.Mvc.FileResult> oggetto:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-143">Serve them via an action method to which authorization is applied and return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a><span data-ttu-id="a8bfc-144">Esplorazione directory</span><span class="sxs-lookup"><span data-stu-id="a8bfc-144">Directory browsing</span></span>

<span data-ttu-id="a8bfc-145">L'esplorazione directory consente l'elenco di directory nelle directory specificate.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-145">Directory browsing allows directory listing within specified directories.</span></span>

<span data-ttu-id="a8bfc-146">Per motivi di sicurezza, l'esplorazione directory è disabilitata per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-146">Directory browsing is disabled by default for security reasons.</span></span> <span data-ttu-id="a8bfc-147">Per ulteriori informazioni, vedere [considerazioni](#considerations).</span><span class="sxs-lookup"><span data-stu-id="a8bfc-147">For more information, see [Considerations](#considerations).</span></span>

<span data-ttu-id="a8bfc-148">Abilitare l'esplorazione directory con:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-148">Enable directory browsing with:</span></span>

* <span data-ttu-id="a8bfc-149"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a8bfc-149"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="a8bfc-150"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="a8bfc-150"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure`.</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

<span data-ttu-id="a8bfc-151">Il codice precedente consente l'esplorazione directory della cartella *wwwroot/images* utilizzando l'URL `https://<hostname>/MyImages` , con collegamenti a ogni file e cartella:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-151">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL `https://<hostname>/MyImages`, with links to each file and folder:</span></span>

![esplorazione directory](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a><span data-ttu-id="a8bfc-153">Gestire i documenti predefiniti</span><span class="sxs-lookup"><span data-stu-id="a8bfc-153">Serve default documents</span></span>

<span data-ttu-id="a8bfc-154">L'impostazione di una pagina predefinita fornisce ai visitatori un punto di partenza per un sito.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-154">Setting a default page provides visitors a starting point on a site.</span></span> <span data-ttu-id="a8bfc-155">Per gestire una pagina predefinita da `wwwroot` senza un URI completo, chiamare il <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> Metodo:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-155">To serve a default page from `wwwroot` without a fully qualified URI, call the <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> method:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="a8bfc-156">`UseDefaultFiles` deve essere chiamato prima di `UseStaticFiles` per usare il file predefinito.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-156">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="a8bfc-157">`UseDefaultFiles` è un rewriter URL che non serve il file.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-157">`UseDefaultFiles` is a URL rewriter that doesn't serve the file.</span></span>

<span data-ttu-id="a8bfc-158">Con `UseDefaultFiles` , richiede a una cartella in `wwwroot` Cerca:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-158">With `UseDefaultFiles`, requests to a folder in `wwwroot` search for:</span></span>

* <span data-ttu-id="a8bfc-159">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="a8bfc-159">*default.htm*</span></span>
* <span data-ttu-id="a8bfc-160">*default.html*</span><span class="sxs-lookup"><span data-stu-id="a8bfc-160">*default.html*</span></span>
* <span data-ttu-id="a8bfc-161">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="a8bfc-161">*index.htm*</span></span>
* <span data-ttu-id="a8bfc-162">*index.html*</span><span class="sxs-lookup"><span data-stu-id="a8bfc-162">*index.html*</span></span>

<span data-ttu-id="a8bfc-163">Il primo file trovato nell'elenco viene usato come se la richiesta fosse l'URI completo.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-163">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="a8bfc-164">L'URL del browser continua a riflettere l'URI richiesto.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-164">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="a8bfc-165">Il codice seguente modifica il nome file predefinito in *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-165">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

<span data-ttu-id="a8bfc-166">Il codice seguente illustra `Startup.Configure` con il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-166">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a><span data-ttu-id="a8bfc-167">UseFileServer per i documenti predefiniti</span><span class="sxs-lookup"><span data-stu-id="a8bfc-167">UseFileServer for default documents</span></span>

<span data-ttu-id="a8bfc-168"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combina la funzionalità di `UseStaticFiles` , `UseDefaultFiles` e facoltativamente `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="a8bfc-168"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="a8bfc-169">Chiamare `app.UseFileServer` per abilitare la funzione di file statici e il file predefinito.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-169">Call `app.UseFileServer` to enable the serving of static files and the default file.</span></span> <span data-ttu-id="a8bfc-170">L'esplorazione directory non è abilitata.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-170">Directory browsing isn't enabled.</span></span> <span data-ttu-id="a8bfc-171">Il codice seguente illustra `Startup.Configure` con `UseFileServer` :</span><span class="sxs-lookup"><span data-stu-id="a8bfc-171">The following code shows `Startup.Configure` with `UseFileServer`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="a8bfc-172">Il codice seguente consente di mantenere i file statici, il file predefinito e l'esplorazione delle directory:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-172">The following code enables the serving of static files, the default file, and directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="a8bfc-173">Il codice seguente illustra `Startup.Configure` con il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-173">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="a8bfc-174">Considerare la gerarchia di directory seguente:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-174">Consider the following directory hierarchy:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

<span data-ttu-id="a8bfc-175">Il codice seguente consente di mantenere i file statici, il file predefinito e l'esplorazione della directory di `MyStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="a8bfc-175">The following code enables the serving of static files, the default file, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<span data-ttu-id="a8bfc-176"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> deve essere chiamato quando il `EnableDirectoryBrowsing` valore della proprietà è `true` .</span><span class="sxs-lookup"><span data-stu-id="a8bfc-176"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> must be called when the `EnableDirectoryBrowsing` property value is `true`.</span></span>

<span data-ttu-id="a8bfc-177">Dall'uso della gerarchia di file e del codice precedente ne deriva quanto segue:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-177">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="a8bfc-178">URI</span><span class="sxs-lookup"><span data-stu-id="a8bfc-178">URI</span></span>            |      <span data-ttu-id="a8bfc-179">Risposta</span><span class="sxs-lookup"><span data-stu-id="a8bfc-179">Response</span></span>  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | <span data-ttu-id="a8bfc-180">*MyStaticFiles/immagini/MyImage.jpg*</span><span class="sxs-lookup"><span data-stu-id="a8bfc-180">*MyStaticFiles/images/MyImage.jpg*</span></span> |
| `https://<hostname>/StaticFiles` | <span data-ttu-id="a8bfc-181">*MyStaticFiles/default.html*</span><span class="sxs-lookup"><span data-stu-id="a8bfc-181">*MyStaticFiles/default.html*</span></span> |

<span data-ttu-id="a8bfc-182">Se nella directory *MyStaticFiles* non è presente alcun file con nome predefinito, `https://<hostname>/StaticFiles` restituisce l'elenco di directory con i collegamenti selezionabili:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-182">If no default-named file exists in the *MyStaticFiles* directory, `https://<hostname>/StaticFiles` returns the directory listing with clickable links:</span></span>

![Elenco di file statici](static-files/_static/db2.png)

<span data-ttu-id="a8bfc-184"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> ed <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> eseguono un reindirizzamento sul lato client dall'URI di destinazione senza una finalizzazione `/`  all'URI di destinazione con una finalizzazione `/` .</span><span class="sxs-lookup"><span data-stu-id="a8bfc-184"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from the target URI without a trailing `/`  to the target URI with a trailing `/`.</span></span> <span data-ttu-id="a8bfc-185">Ad esempio, da `https://<hostname>/StaticFiles` a `https://<hostname>/StaticFiles/` .</span><span class="sxs-lookup"><span data-stu-id="a8bfc-185">For example, from `https://<hostname>/StaticFiles` to `https://<hostname>/StaticFiles/`.</span></span> <span data-ttu-id="a8bfc-186">Gli URL relativi nella directory *staticfiles* non sono validi senza una barra finale ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="a8bfc-186">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash (`/`).</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="a8bfc-187">Classe FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="a8bfc-187">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="a8bfc-188">La <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> classe contiene una `Mappings` proprietà che funge da mapping tra le estensioni di file e i tipi di contenuto MIME.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-188">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property that serves as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="a8bfc-189">Nell'esempio seguente viene eseguito il mapping di diverse estensioni di file a tipi MIME noti.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-189">In the following sample, several file extensions are mapped to known MIME types.</span></span> <span data-ttu-id="a8bfc-190">L'estensione *RTF* viene sostituita e viene rimosso *. mp4* :</span><span class="sxs-lookup"><span data-stu-id="a8bfc-190">The *.rtf* extension is replaced, and *.mp4* is removed:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

<span data-ttu-id="a8bfc-191">Il codice seguente illustra `Startup.Configure` con il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-191">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

<span data-ttu-id="a8bfc-192">Vedere [Tipi di contenuto MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="a8bfc-192">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="a8bfc-193">Tipi di contenuto non standard</span><span class="sxs-lookup"><span data-stu-id="a8bfc-193">Non-standard content types</span></span>

<span data-ttu-id="a8bfc-194">Il middleware dei file statici riconosce quasi 400 tipi di contenuto di file noti.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-194">The Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="a8bfc-195">Se l'utente richiede un file con un tipo di file sconosciuto, il middleware dei file statici passa la richiesta al middleware successivo nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-195">If the user requests a file with an unknown file type, the Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="a8bfc-196">Se nessun middleware gestisce la richiesta, viene restituita la risposta *404 Non trovato*.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-196">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="a8bfc-197">Se è abilitata l'esplorazione directory, viene visualizzato un collegamento al file nella visualizzazione directory.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-197">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="a8bfc-198">Il codice seguente consente di usare tipi sconosciuti ed esegue il rendering del file sconosciuto come immagine:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-198">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

<span data-ttu-id="a8bfc-199">Il codice seguente illustra `Startup.Configure` con il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-199">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

<span data-ttu-id="a8bfc-200">Con il codice precedente, una richiesta per un file con un tipo di contenuto sconosciuto viene restituita come immagine.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-200">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="a8bfc-201">L'abilitazione <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> è un rischio per la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-201">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="a8bfc-202">È disabilitato per impostazione predefinita e ne è sconsigliato l'uso.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-202">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="a8bfc-203">La classe [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) offre un'alternativa più sicura per usare i file con estensioni non standard.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-203">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="a8bfc-204">Gestire i file da più posizioni</span><span class="sxs-lookup"><span data-stu-id="a8bfc-204">Serve files from multiple locations</span></span>

<span data-ttu-id="a8bfc-205">`UseStaticFiles` e `UseFileServer` per impostazione predefinita il provider di file che punta a `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="a8bfc-205">`UseStaticFiles` and `UseFileServer` default to the file provider pointing at `wwwroot`.</span></span> <span data-ttu-id="a8bfc-206">Istanze aggiuntive di `UseStaticFiles` e `UseFileServer` possono essere fornite con altri provider di file per gestire i file da altri percorsi.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-206">Additional instances of `UseStaticFiles` and `UseFileServer` can be provided with other file providers to serve files from other locations.</span></span> <span data-ttu-id="a8bfc-207">Per altre informazioni, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="a8bfc-207">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a><span data-ttu-id="a8bfc-208">Considerazioni sulla sicurezza per i file statici</span><span class="sxs-lookup"><span data-stu-id="a8bfc-208">Security considerations for static files</span></span>

> [!WARNING]
> <span data-ttu-id="a8bfc-209">L'uso di `UseDirectoryBrowser` e `UseStaticFiles` può comportare la perdita di informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-209">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="a8bfc-210">È consigliabile disabilitare l'esplorazione directory nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-210">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="a8bfc-211">Controllare attentamente quali sono le directory abilitate tramite `UseStaticFiles` o `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-211">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="a8bfc-212">L'intera directory e le relative sottodirectory diventano pubblicamente accessibili.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-212">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="a8bfc-213">Archiviare i file adatti al servizio pubblico in una directory dedicata, ad esempio `<content_root>/wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="a8bfc-213">Store files suitable for serving to the public in a dedicated directory, such as `<content_root>/wwwroot`.</span></span> <span data-ttu-id="a8bfc-214">Separare questi file da visualizzazioni MVC, Razor pagine, file di configurazione e così via.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-214">Separate these files from MVC views, Razor Pages, configuration files, etc.</span></span>

* <span data-ttu-id="a8bfc-215">Gli URL per il contenuto esposto con `UseDirectoryBrowser` e `UseStaticFiles` sono soggetti a distinzione tra maiuscole e minuscole e a limitazione di caratteri del file system sottostante.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-215">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="a8bfc-216">Windows, ad esempio, non fa distinzione tra maiuscole e minuscole, ma macOS e Linux non lo sono.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-216">For example, Windows is case insensitive, but macOS and Linux aren't.</span></span>

* <span data-ttu-id="a8bfc-217">Le app ASP.NET Core ospitate in IIS usano il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) per inoltrare tutte le richieste all'app, incluse le richieste di file statici.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-217">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="a8bfc-218">Il gestore di file statici di IIS non viene usato e non ha la possibilità di gestire le richieste.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-218">The IIS static file handler isn't used and has no chance to handle requests.</span></span>

* <span data-ttu-id="a8bfc-219">Completare la procedura seguente in Gestione IIS per rimuovere il gestore di file statici di IIS a livello di server o di sito Web:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-219">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="a8bfc-220">Passare alla funzionalità **Moduli**.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-220">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="a8bfc-221">Selezionare **StaticFileModule** nell'elenco.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-221">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="a8bfc-222">Fare clic su **Rimuovi** nell'intestazione laterale **Azioni**.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-222">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="a8bfc-223">Se il gestore di file statici di IIS è abilitato **e** il modulo ASP.NET Core non è configurato correttamente, vengono usati i file statici.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-223">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="a8bfc-224">Tale scenario si verifica ad esempio se il file *web.config* non è stato distribuito.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-224">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="a8bfc-225">Inserire i file di codice, tra cui *. cs* e *. cshtml*, all'esterno della [radice Web](xref:fundamentals/index#web-root)del progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-225">Place code files, including *.cs* and *.cshtml*, outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="a8bfc-226">Si crea quindi un separazione logica tra il contenuto sul lato client dell'app e il codice basato su server.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-226">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="a8bfc-227">In questo modo si impedisce la perdita del codice sul lato server.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-227">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a8bfc-228">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a8bfc-228">Additional resources</span></span>

* [<span data-ttu-id="a8bfc-229">Middleware</span><span class="sxs-lookup"><span data-stu-id="a8bfc-229">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="a8bfc-230">Introduzione ad ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a8bfc-230">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a8bfc-231">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="a8bfc-231">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="a8bfc-232">I file statici, ad esempio HTML, CSS, immagini e JavaScript, sono asset che un'app ASP.NET Core rende direttamente disponibili nei client.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-232">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="a8bfc-233">Per abilitare l'uso di questi file, sono necessarie alcune configurazioni.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-233">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="a8bfc-234">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a8bfc-234">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="a8bfc-235">Usare i file statici</span><span class="sxs-lookup"><span data-stu-id="a8bfc-235">Serve static files</span></span>

<span data-ttu-id="a8bfc-236">I file statici vengono archiviati nella directory [radice Web](xref:fundamentals/index#web-root) del progetto.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-236">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="a8bfc-237">La directory predefinita è *{Content root}/wwwroot*, ma può essere modificata tramite il <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> metodo.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-237">The default directory is *{content root}/wwwroot*, but it can be changed via the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="a8bfc-238">Vedere [Radice del contenuto](xref:fundamentals/index#content-root) e [Radice Web](xref:fundamentals/index#web-root) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-238">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="a8bfc-239">L'host Web dell'app deve conoscere la directory radice del contenuto.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-239">The app's web host must be made aware of the content root directory.</span></span>

<span data-ttu-id="a8bfc-240">Il metodo `WebHost.CreateDefaultBuilder` imposta la radice del contenuto nella directory corrente:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-240">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

<span data-ttu-id="a8bfc-241">I file statici sono accessibili tramite un percorso relativo alla [radice Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="a8bfc-241">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="a8bfc-242">Ad esempio, il modello di progetto **applicazione Web** contiene diverse cartelle all'interno della `wwwroot` cartella:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-242">For example, the **Web Application** project template contains several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`

<span data-ttu-id="a8bfc-243">Il formato URI per accedere a un file nella sottocartella *images* è *http:// \<server_address> \<image_file_name> /images/*.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-243">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="a8bfc-244">ad esempio *http://localhost:9189/images/banner3.svg*.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-244">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

<span data-ttu-id="a8bfc-245">Se la destinazione è .NET Framework, aggiungere il pacchetto [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) al progetto.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-245">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="a8bfc-246">Se la destinazione è .NET Core, questo pacchetto è incluso nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a8bfc-246">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

<span data-ttu-id="a8bfc-247">Configurare il [middleware](xref:fundamentals/middleware/index), che consente la conservazione dei file statici.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-247">Configure the [middleware](xref:fundamentals/middleware/index), which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="a8bfc-248">Usare i file all'interno della radice Web</span><span class="sxs-lookup"><span data-stu-id="a8bfc-248">Serve files inside of web root</span></span>

<span data-ttu-id="a8bfc-249">Richiamare il <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> metodo all'interno di `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="a8bfc-249">Invoke the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="a8bfc-250">L'overload del `UseStaticFiles` metodo senza parametri contrassegna i file nella [radice Web](xref:fundamentals/index#web-root) come servable.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-250">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="a8bfc-251">Il markup seguente si riferisce a *wwwroot/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-251">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="a8bfc-252">Nel codice precedente, il carattere tilde `~/` punta alla [radice Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="a8bfc-252">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="a8bfc-253">Usare i file all'esterno della radice Web</span><span class="sxs-lookup"><span data-stu-id="a8bfc-253">Serve files outside of web root</span></span>

<span data-ttu-id="a8bfc-254">Si consideri una gerarchia di directory in cui i file statici da servire si trovano all'esterno della [radice Web](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="a8bfc-254">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

<span data-ttu-id="a8bfc-255">Una richiesta può accedere al file *banner1.svg* configurando il middleware dei file statici come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-255">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="a8bfc-256">Nel codice precedente la gerarchia di directory *MyStaticFiles* viene esposta pubblicamente tramite il segmento dell'URI *StaticFiles*.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-256">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="a8bfc-257">Una richiesta a *http:// \<server_address> /staticfiles/images/banner1.svg* serve il file *banner1. svg* .</span><span class="sxs-lookup"><span data-stu-id="a8bfc-257">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="a8bfc-258">Il markup seguente si riferisce a *MyStaticFiles/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-258">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="a8bfc-259">Impostare le intestazioni della risposta HTTP</span><span class="sxs-lookup"><span data-stu-id="a8bfc-259">Set HTTP response headers</span></span>

<span data-ttu-id="a8bfc-260">Un <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> oggetto può essere utilizzato per impostare le intestazioni di risposta http.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-260">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="a8bfc-261">Oltre alla configurazione del file statico che funge dalla [radice Web](xref:fundamentals/index#web-root), il codice seguente imposta l' `Cache-Control` intestazione:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-261">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="a8bfc-262">Il <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> metodo esiste nel pacchetto [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .</span><span class="sxs-lookup"><span data-stu-id="a8bfc-262">The <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="a8bfc-263">I file sono stati resi pubblicamente inseribili nella cache per 10 minuti (600 secondi) nell'ambiente di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-263">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Sono state aggiunte le intestazioni della risposta che visualizzano l'intestazione Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="a8bfc-265">Autorizzazione dei file statici</span><span class="sxs-lookup"><span data-stu-id="a8bfc-265">Static file authorization</span></span>

<span data-ttu-id="a8bfc-266">Il middleware dei file statici non offre controlli di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-266">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="a8bfc-267">I file usati dal middleware, inclusi i file in *wwwroot*, sono disponibili pubblicamente.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-267">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="a8bfc-268">Per usare i file in base alle autorizzazioni:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-268">To serve files based on authorization:</span></span>

* <span data-ttu-id="a8bfc-269">Archiviare i file all'esterno di *wwwroot* e di qualsiasi directory alla quale può accedere il middleware dei file statici.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-269">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="a8bfc-270">Usarli tramite un metodo di azione al quale è applicata l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-270">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="a8bfc-271">Restituisce un <xref:Microsoft.AspNetCore.Mvc.FileResult> oggetto:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-271">Return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="a8bfc-272">Abilitare l'esplorazione directory</span><span class="sxs-lookup"><span data-stu-id="a8bfc-272">Enable directory browsing</span></span>

<span data-ttu-id="a8bfc-273">L'esplorazione directory consente agli utenti dell'app Web di visualizzare un elenco di directory e i file all'interno di una directory specifica.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-273">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="a8bfc-274">Per motivi di sicurezza, l'esplorazione directory è disabilitata per impostazione predefinita (vedere [Considerazioni](#considerations)).</span><span class="sxs-lookup"><span data-stu-id="a8bfc-274">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="a8bfc-275">Abilitare l'esplorazione directory richiamando il <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> metodo in `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="a8bfc-275">Enable directory browsing by invoking the <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="a8bfc-276">Aggiungere i servizi necessari richiamando il <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> metodo da `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a8bfc-276">Add required services by invoking the <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="a8bfc-277">Il codice precedente consente l'esplorazione directory della cartella *wwwroot/images* utilizzando l'URL *http:// \<server_address> /myImages*, con collegamenti a ogni file e cartella:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-277">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![esplorazione directory](static-files/_static/dir-browse.png)

<span data-ttu-id="a8bfc-279">Vedere la sezione [Considerazioni](#considerations) per conoscere i rischi per la sicurezza quando l'esplorazione viene abilitata.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-279">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="a8bfc-280">Si notino le due chiamate `UseStaticFiles` nell'esempio seguente.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-280">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="a8bfc-281">La prima chiamata consente l'uso di file statici nella cartella *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-281">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="a8bfc-282">La seconda chiamata Abilita l'esplorazione directory della cartella *wwwroot/images* utilizzando l'URL *http:// \<server_address> /myImages*:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-282">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="a8bfc-283">Usare un documento predefinito</span><span class="sxs-lookup"><span data-stu-id="a8bfc-283">Serve a default document</span></span>

<span data-ttu-id="a8bfc-284">L'impostazione di una home page predefinita offre ai visitatori un punto di partenza logico per la visita del sito.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-284">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="a8bfc-285">Per gestire una pagina predefinita senza che l'utente completi l'URI, chiamare il <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> metodo da `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="a8bfc-285">To serve a default page without the user fully qualifying the URI, call the <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="a8bfc-286">`UseDefaultFiles` deve essere chiamato prima di `UseStaticFiles` per usare il file predefinito.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-286">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="a8bfc-287">`UseDefaultFiles` è un URL rewriter che effettivamente non usa il file.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-287">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="a8bfc-288">Abilitare il middleware dei file statici tramite `UseStaticFiles` per usare il file.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-288">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="a8bfc-289">Con `UseDefaultFiles`, si richiede la ricerca nella cartella degli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-289">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="a8bfc-290">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="a8bfc-290">*default.htm*</span></span>
* <span data-ttu-id="a8bfc-291">*default.html*</span><span class="sxs-lookup"><span data-stu-id="a8bfc-291">*default.html*</span></span>
* <span data-ttu-id="a8bfc-292">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="a8bfc-292">*index.htm*</span></span>
* <span data-ttu-id="a8bfc-293">*index.html*</span><span class="sxs-lookup"><span data-stu-id="a8bfc-293">*index.html*</span></span>

<span data-ttu-id="a8bfc-294">Il primo file trovato nell'elenco viene usato come se la richiesta fosse l'URI completo.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-294">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="a8bfc-295">L'URL del browser continua a riflettere l'URI richiesto.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-295">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="a8bfc-296">Il codice seguente modifica il nome file predefinito in *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-296">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="a8bfc-297">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="a8bfc-297">UseFileServer</span></span>

<span data-ttu-id="a8bfc-298"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combina la funzionalità di `UseStaticFiles` , `UseDefaultFiles` e facoltativamente `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="a8bfc-298"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="a8bfc-299">Il codice seguente consente di usare i file statici e il file predefinito.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-299">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="a8bfc-300">L'esplorazione directory non è abilitata.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-300">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="a8bfc-301">Il codice seguente si basa sull'overload senza parametri, consentendo l'esplorazione directory:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-301">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="a8bfc-302">Considerare la gerarchia di directory seguente:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-302">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="a8bfc-303">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="a8bfc-303">**wwwroot**</span></span>
  * <span data-ttu-id="a8bfc-304">**CSS**</span><span class="sxs-lookup"><span data-stu-id="a8bfc-304">**css**</span></span>
  * <span data-ttu-id="a8bfc-305">**images**</span><span class="sxs-lookup"><span data-stu-id="a8bfc-305">**images**</span></span>
  * <span data-ttu-id="a8bfc-306">**JS**</span><span class="sxs-lookup"><span data-stu-id="a8bfc-306">**js**</span></span>
* <span data-ttu-id="a8bfc-307">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="a8bfc-307">**MyStaticFiles**</span></span>
  * <span data-ttu-id="a8bfc-308">**images**</span><span class="sxs-lookup"><span data-stu-id="a8bfc-308">**images**</span></span>
    * <span data-ttu-id="a8bfc-309">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="a8bfc-309">*banner1.svg*</span></span>
  * <span data-ttu-id="a8bfc-310">*default.html*</span><span class="sxs-lookup"><span data-stu-id="a8bfc-310">*default.html*</span></span>

<span data-ttu-id="a8bfc-311">Il codice seguente abilita i file statici, i file predefiniti e l'esplorazione directory di `MyStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-311">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="a8bfc-312">Chiamare il metodo `AddDirectoryBrowser` quando il valore della proprietà `EnableDirectoryBrowsing` è `true`:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-312">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="a8bfc-313">Dall'uso della gerarchia di file e del codice precedente ne deriva quanto segue:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-313">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="a8bfc-314">URI</span><span class="sxs-lookup"><span data-stu-id="a8bfc-314">URI</span></span>            |                             <span data-ttu-id="a8bfc-315">Risposta</span><span class="sxs-lookup"><span data-stu-id="a8bfc-315">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="a8bfc-316">*\<server_address>/StaticFiles/images/banner1.svg http://*</span><span class="sxs-lookup"><span data-stu-id="a8bfc-316">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="a8bfc-317">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="a8bfc-317">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="a8bfc-318">*\<server_address>/StaticFiles http://*</span><span class="sxs-lookup"><span data-stu-id="a8bfc-318">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="a8bfc-319">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="a8bfc-319">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="a8bfc-320">Se nella directory *MyStaticFiles* non è presente alcun file con nome predefinito, *http:// \<server_address> /staticfiles* restituisce l'elenco di directory con i collegamenti che è possibile selezionare:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-320">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Elenco di file statici](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="a8bfc-322"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> e <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> eseguono un reindirizzamento sul lato client da `http://{SERVER ADDRESS}/StaticFiles` (senza barra rovesciata) a `http://{SERVER ADDRESS}/StaticFiles/` (con barra rovesciata).</span><span class="sxs-lookup"><span data-stu-id="a8bfc-322"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="a8bfc-323">Gli URL relativi all'interno della directory *StaticFiles* non sono validi senza barra finale.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-323">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="a8bfc-324">Classe FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="a8bfc-324">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="a8bfc-325">La <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> classe contiene una `Mappings` proprietà che funge da mapping tra le estensioni di file e i tipi di contenuto MIME.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-325">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="a8bfc-326">Nell'esempio seguente varie estensioni di file vengono registrate in tipi MIME noti.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-326">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="a8bfc-327">L'estensione *rtf* viene sostituita e l'estensione *mp4* viene rimossa.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-327">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="a8bfc-328">Vedere [Tipi di contenuto MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="a8bfc-328">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

<span data-ttu-id="a8bfc-329">Per informazioni sull'utilizzo di un oggetto personalizzato <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> o per la configurazione di altri <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> in Blazor app Server, vedere <xref:blazor/fundamentals/additional-scenarios#static-files> .</span><span class="sxs-lookup"><span data-stu-id="a8bfc-329">For information on using a custom <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or to configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> in Blazor Server apps, see <xref:blazor/fundamentals/additional-scenarios#static-files>.</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="a8bfc-330">Tipi di contenuto non standard</span><span class="sxs-lookup"><span data-stu-id="a8bfc-330">Non-standard content types</span></span>

<span data-ttu-id="a8bfc-331">Il middleware dei file statici riconosce almeno 400 tipi di contenuto file noti.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-331">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="a8bfc-332">Se viene richiesto un file con un tipo di file non noto, il middleware dei file statici passa la richiesta al middleware successivo nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-332">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="a8bfc-333">Se nessun middleware gestisce la richiesta, viene restituita la risposta *404 Non trovato*.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-333">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="a8bfc-334">Se è abilitata l'esplorazione directory, viene visualizzato un collegamento al file nella visualizzazione directory.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-334">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="a8bfc-335">Il codice seguente consente di usare tipi sconosciuti ed esegue il rendering del file sconosciuto come immagine:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-335">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="a8bfc-336">Con il codice precedente, una richiesta per un file con un tipo di contenuto sconosciuto viene restituita come immagine.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-336">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="a8bfc-337">L'abilitazione <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> è un rischio per la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-337">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="a8bfc-338">È disabilitato per impostazione predefinita e ne è sconsigliato l'uso.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-338">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="a8bfc-339">La classe [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) offre un'alternativa più sicura per usare i file con estensioni non standard.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-339">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="a8bfc-340">Gestire i file da più posizioni</span><span class="sxs-lookup"><span data-stu-id="a8bfc-340">Serve files from multiple locations</span></span>

<span data-ttu-id="a8bfc-341">`UseStaticFiles``UseFileServer`per impostazione predefinita, il provider di file punta a *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-341">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="a8bfc-342">È possibile fornire istanze aggiuntive di `UseStaticFiles` e `UseFileServer` con altri provider di file per gestire i file da altri percorsi.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-342">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="a8bfc-343">Per altre informazioni, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="a8bfc-343">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="a8bfc-344">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="a8bfc-344">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="a8bfc-345">L'uso di `UseDirectoryBrowser` e `UseStaticFiles` può comportare la perdita di informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-345">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="a8bfc-346">È consigliabile disabilitare l'esplorazione directory nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-346">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="a8bfc-347">Controllare attentamente quali sono le directory abilitate tramite `UseStaticFiles` o `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-347">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="a8bfc-348">L'intera directory e le relative sottodirectory diventano pubblicamente accessibili.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-348">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="a8bfc-349">Archiviare i file adatti al servizio pubblico in una directory dedicata, ad esempio \* \<content_root> /wwwroot\*.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-349">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="a8bfc-350">Separare i file da visualizzazioni MVC, Razor pagine (solo 2. x), file di configurazione e così via.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-350">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="a8bfc-351">Gli URL per il contenuto esposto con `UseDirectoryBrowser` e `UseStaticFiles` sono soggetti a distinzione tra maiuscole e minuscole e a limitazione di caratteri del file system sottostante.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-351">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="a8bfc-352">Ad esempio, in Windows viene fatta distinzione tra maiuscole e minuscole, al contrario di quanto avviene in macOS e Linux.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-352">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="a8bfc-353">Le app ASP.NET Core ospitate in IIS usano il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) per inoltrare tutte le richieste all'app, incluse le richieste di file statici.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-353">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="a8bfc-354">Non viene usato il gestore di file statici di IIS.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-354">The IIS static file handler isn't used.</span></span> <span data-ttu-id="a8bfc-355">Non è possibile gestire le richieste se queste non sono state prima gestite dal modulo.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-355">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="a8bfc-356">Completare la procedura seguente in Gestione IIS per rimuovere il gestore di file statici di IIS a livello di server o di sito Web:</span><span class="sxs-lookup"><span data-stu-id="a8bfc-356">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="a8bfc-357">Passare alla funzionalità **Moduli**.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-357">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="a8bfc-358">Selezionare **StaticFileModule** nell'elenco.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-358">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="a8bfc-359">Fare clic su **Rimuovi** nell'intestazione laterale **Azioni**.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-359">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="a8bfc-360">Se il gestore di file statici di IIS è abilitato **e** il modulo ASP.NET Core non è configurato correttamente, vengono usati i file statici.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-360">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="a8bfc-361">Tale scenario si verifica ad esempio se il file *web.config* non è stato distribuito.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-361">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="a8bfc-362">Inserire i file di codice (incluso *. cs* e *. cshtml*) all'esterno della [radice Web](xref:fundamentals/index#web-root)del progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-362">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="a8bfc-363">Si crea quindi un separazione logica tra il contenuto sul lato client dell'app e il codice basato su server.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-363">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="a8bfc-364">In questo modo si impedisce la perdita del codice sul lato server.</span><span class="sxs-lookup"><span data-stu-id="a8bfc-364">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a8bfc-365">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a8bfc-365">Additional resources</span></span>

* [<span data-ttu-id="a8bfc-366">Middleware</span><span class="sxs-lookup"><span data-stu-id="a8bfc-366">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="a8bfc-367">Introduzione ad ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a8bfc-367">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end
