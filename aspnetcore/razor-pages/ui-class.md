---
title: Interfaccia utente Razor riutilizzabile nelle librerie di classi con ASP.NET Core
author: Rick-Anderson
description: Viene illustrato come creare un'interfaccia utente Razor riutilizzabile usando visualizzazioni parziali in una libreria di classi in ASP.NET Core.Explains how to create reusable Razor UI using partial views in a class library in ASP.NET Core.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: f24dc62eba345a8a3d35143805b4966cb51832fa
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667566"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="69257-103">Creare un'interfaccia utente riutilizzabile usando il progetto di libreria di classi Razor in ASP.NET CoreCreate reusable UI using the Razor class library project in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="69257-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="69257-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="69257-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="69257-105">In una libreria di classi Razor (RCL) è possibile compilare visualizzazioni Razor, controller, modelli di pagina, [componenti Razor](xref:blazor/class-libraries), [Visualizza componenti](xref:mvc/views/view-components)e modelli di dati.</span><span class="sxs-lookup"><span data-stu-id="69257-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="69257-106">La libreria di classi Razor può essere inclusa nel pacchetto e usata nuovamente.</span><span class="sxs-lookup"><span data-stu-id="69257-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="69257-107">Le applicazioni possono includere la libreria di classi Razor ed eseguire l'override delle visualizzazioni e pagine in essa contenute.</span><span class="sxs-lookup"><span data-stu-id="69257-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="69257-108">Quando viene trovata una visualizzazione, visualizzazione parziale o pagina Razor sia nell'app Web che nella libreria di classi Razor, il markup Razor (file con estensione *cshtml*) nell'app Web ha la precedenza.</span><span class="sxs-lookup"><span data-stu-id="69257-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="69257-109">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="69257-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="69257-110">Creare una libreria di classi contenente l'interfaccia utente Razor</span><span class="sxs-lookup"><span data-stu-id="69257-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="69257-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="69257-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="69257-112">In Visual Studio selezionare **Crea nuovo progetto**.</span><span class="sxs-lookup"><span data-stu-id="69257-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="69257-113">Selezionare **Razor Class Library** > **Next**.</span><span class="sxs-lookup"><span data-stu-id="69257-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="69257-114">Assegnare alla libreria il nome (ad esempio, "RazorClassLib"), > **Create**.</span><span class="sxs-lookup"><span data-stu-id="69257-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="69257-115">Per evitare un conflitto di nomi di file con la libreria di visualizzazione generata, verificare che il nome della libreria non finisca per `.Views`.</span><span class="sxs-lookup"><span data-stu-id="69257-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="69257-116">Selezionare **Pagine e visualizzazioni** di supporto se è necessario supportare le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="69257-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="69257-117">Per impostazione predefinita, sono supportate solo le pagine Razor.By default, only Razor Pages are supported.</span><span class="sxs-lookup"><span data-stu-id="69257-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="69257-118">Selezionare **Create** (Crea).</span><span class="sxs-lookup"><span data-stu-id="69257-118">Select **Create**.</span></span>

<span data-ttu-id="69257-119">Il modello Razor libreria di classi (RCL) per impostazione predefinita è Razor component development per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="69257-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="69257-120">L'opzione **Pagine e visualizzazioni** di supporto supporta le pagine e le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="69257-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="69257-121">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="69257-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="69257-122">Eseguire `dotnet new razorclasslib` dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="69257-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="69257-123">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="69257-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="69257-124">Il modello Razor libreria di classi (RCL) per impostazione predefinita è Razor component development per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="69257-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="69257-125">Passare `--support-pages-and-views` l'opzione (`dotnet new razorclasslib --support-pages-and-views`) per fornire supporto per pagine e visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="69257-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="69257-126">Per altre informazioni, vedere [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="69257-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="69257-127">Per evitare un conflitto di nomi di file con la libreria di visualizzazione generata, verificare che il nome della libreria non finisca per `.Views`.</span><span class="sxs-lookup"><span data-stu-id="69257-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="69257-128">Aggiungere i file Razor alla libreria di classi Razor.</span><span class="sxs-lookup"><span data-stu-id="69257-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="69257-129">I modelli ASP.NET Core presuppongono che il contenuto RCL si trova nella cartella *Aree.*</span><span class="sxs-lookup"><span data-stu-id="69257-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="69257-130">Vedere [Layout delle pagine RCL](#rcl-pages-layout) per creare `~/Pages` un `~/Areas/Pages`RCL che espone il contenuto anziché .</span><span class="sxs-lookup"><span data-stu-id="69257-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="69257-131">Contenuto RCL di riferimento</span><span class="sxs-lookup"><span data-stu-id="69257-131">Reference RCL content</span></span>

<span data-ttu-id="69257-132">Il riferimento alla libreria di classi Razor può essere eseguito da:</span><span class="sxs-lookup"><span data-stu-id="69257-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="69257-133">Pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="69257-133">NuGet package.</span></span> <span data-ttu-id="69257-134">Vedere [Creazione di pacchetti NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Creare e pubblicare un pacchetto NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="69257-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="69257-135">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="69257-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="69257-136">Vedere [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="69257-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="69257-137">Eseguire l'override di visualizzazioni, visualizzazioni parziali e pagine</span><span class="sxs-lookup"><span data-stu-id="69257-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="69257-138">Quando viene trovata una visualizzazione, visualizzazione parziale o pagina Razor sia nell'app Web che nella libreria di classi Razor, il markup Razor (file con estensione *cshtml*) nell'app Web ha la precedenza.</span><span class="sxs-lookup"><span data-stu-id="69257-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="69257-139">Ad esempio, aggiungere *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* a WebApp1 e Page1 in WebApp1 avrà la precedenza su Page1 nell'RCL.</span><span class="sxs-lookup"><span data-stu-id="69257-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="69257-140">Nel download di esempio, rinominare *WebApp1/aree/MyFeature2* in *WebApp1/aree/MyFeature* per testare la precedenza.</span><span class="sxs-lookup"><span data-stu-id="69257-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="69257-141">Copiare la visualizzazione parziali *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* in *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="69257-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="69257-142">Aggiornare il markup per indicare la nuova posizione.</span><span class="sxs-lookup"><span data-stu-id="69257-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="69257-143">Compilare ed eseguire l'app per verificare quale versione del parziale sia in uso.</span><span class="sxs-lookup"><span data-stu-id="69257-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="69257-144">Layout Pagine RCL</span><span class="sxs-lookup"><span data-stu-id="69257-144">RCL Pages layout</span></span>

<span data-ttu-id="69257-145">Per fare riferimento al contenuto RCL come se fa parte della cartella *Pages* dell'app Web, creare il progetto RCL con la struttura di file seguente:</span><span class="sxs-lookup"><span data-stu-id="69257-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="69257-146">*RazorUIClassLib/Pagine*</span><span class="sxs-lookup"><span data-stu-id="69257-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="69257-147">*RazorUIClassLib/Pagine/Condiviso*</span><span class="sxs-lookup"><span data-stu-id="69257-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="69257-148">Si supponga che *RazorUIClassLib/Pages/Shared* contenga due file parziali: *_Header.cshtml* e *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="69257-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="69257-149">I `<partial>` tag possono essere aggiunti al file *_Layout.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="69257-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="69257-150">Creare un RCL con asset staticiCreate an RCL with static assets</span><span class="sxs-lookup"><span data-stu-id="69257-150">Create an RCL with static assets</span></span>

<span data-ttu-id="69257-151">Una libreria RCL può richiedere risorse statiche complementari a cui è possibile fare riferimento con la RCL o l'app consumer di RCL.</span><span class="sxs-lookup"><span data-stu-id="69257-151">An RCL may require companion static assets that can be referenced by either the RCL or the consuming app of the RCL.</span></span> <span data-ttu-id="69257-152">ASP.NET Core consente di creare RCL che includono risorse statiche disponibili per un'app consumer.</span><span class="sxs-lookup"><span data-stu-id="69257-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="69257-153">Per includere risorse complementari come parte di una RCL, create una cartella *wwwroot* nella libreria di classi e includete tutti i file necessari in tale cartella.</span><span class="sxs-lookup"><span data-stu-id="69257-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="69257-154">Quando si crea l'imballaggio di una RCL, tutte le risorse complementari nella cartella *wwwroot* vengono automaticamente incluse nel pacchetto.</span><span class="sxs-lookup"><span data-stu-id="69257-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="69257-155">Escludere le risorse statiche</span><span class="sxs-lookup"><span data-stu-id="69257-155">Exclude static assets</span></span>

<span data-ttu-id="69257-156">Per escludere le risorse statiche, `$(DefaultItemExcludes)` aggiungete il percorso di esclusione desiderato al gruppo di proprietà nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="69257-156">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="69257-157">Separare le voci con`;`un punto e virgola ( ).</span><span class="sxs-lookup"><span data-stu-id="69257-157">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="69257-158">Nell'esempio seguente, il foglio di stile *lib.css* nella cartella *wwwroot* non è considerato una risorsa statica e non è incluso nella RCL pubblicata:</span><span class="sxs-lookup"><span data-stu-id="69257-158">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="69257-159">Integrazione typescript</span><span class="sxs-lookup"><span data-stu-id="69257-159">Typescript integration</span></span>

<span data-ttu-id="69257-160">Per includere i file TypeScript in un RCL:</span><span class="sxs-lookup"><span data-stu-id="69257-160">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="69257-161">Inserire i file TypeScript (*.ts*) all'esterno della cartella *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="69257-161">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="69257-162">Ad esempio, inserire i file in una cartella *Client.*</span><span class="sxs-lookup"><span data-stu-id="69257-162">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="69257-163">Configurare l'output di compilazione TypeScript per la cartella *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="69257-163">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="69257-164">Impostare `TypescriptOutDir` la proprietà `PropertyGroup` all'interno di un nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="69257-164">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="69257-165">Includere la destinazione TypeScript come `ResolveCurrentProjectStaticWebAssets` dipendenza della destinazione aggiungendo la `PropertyGroup` destinazione seguente all'interno di un nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="69257-165">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="69257-166">Utilizzo di contenuto da una RCL di riferimentoConsume content from a referenced RCL</span><span class="sxs-lookup"><span data-stu-id="69257-166">Consume content from a referenced RCL</span></span>

<span data-ttu-id="69257-167">I file inclusi nella cartella *wwwroot* della RCL sono esposti alla RCL o all'app consumer sotto il prefisso `_content/{LIBRARY NAME}/`.</span><span class="sxs-lookup"><span data-stu-id="69257-167">The files included in the *wwwroot* folder of the RCL are exposed to either the RCL or the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="69257-168">Ad esempio, una libreria denominata *Razor.Class.Lib* determina `_content/Razor.Class.Lib/`un percorso al contenuto statico in corrispondenza di .</span><span class="sxs-lookup"><span data-stu-id="69257-168">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="69257-169">Quando si produce un pacchetto NuGet e il nome dell'assembly non corrisponde `{LIBRARY NAME}`all'ID del pacchetto, utilizzare l'ID del pacchetto per .</span><span class="sxs-lookup"><span data-stu-id="69257-169">When producing a NuGet package and the assembly name isn't the same as the package ID, use the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="69257-170">L'app consumer fa riferimento a `<script>` `<style>`risorse `<img>`statiche fornite dalla libreria con tag , , e altri tag HTML.</span><span class="sxs-lookup"><span data-stu-id="69257-170">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="69257-171">L'app consumer deve avere `Startup.Configure`il supporto dei file [statici](xref:fundamentals/static-files) abilitato in:</span><span class="sxs-lookup"><span data-stu-id="69257-171">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="69257-172">Quando si esegue l'app`dotnet run`consumer dall'output di compilazione ( ), gli asset Web statici sono abilitati per impostazione predefinita nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="69257-172">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="69257-173">Per supportare gli asset in altri `UseStaticWebAssets` ambienti durante l'esecuzione dall'output di compilazione, chiamare il generatore host in *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="69257-173">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="69257-174">La `UseStaticWebAssets` chiamata non è necessaria quando si`dotnet publish`esegue un'app dall'output pubblicato ( ).</span><span class="sxs-lookup"><span data-stu-id="69257-174">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="69257-175">Flusso di sviluppo multiprogetto</span><span class="sxs-lookup"><span data-stu-id="69257-175">Multi-project development flow</span></span>

<span data-ttu-id="69257-176">Quando viene eseguita l'app consumer:</span><span class="sxs-lookup"><span data-stu-id="69257-176">When the consuming app runs:</span></span>

* <span data-ttu-id="69257-177">Le risorse nella RCL rimangono nelle cartelle originali.</span><span class="sxs-lookup"><span data-stu-id="69257-177">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="69257-178">Le risorse non vengono spostate nell'app consumer.</span><span class="sxs-lookup"><span data-stu-id="69257-178">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="69257-179">Qualsiasi modifica all'interno della cartella *wwwroot* della RCL si riflette nell'app consumer dopo la ricompilazione della RCL e senza ricompilare l'app consumer.</span><span class="sxs-lookup"><span data-stu-id="69257-179">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="69257-180">Quando viene compilato il controllo RCL, viene prodotto un manifesto che descrive i percorsi delle risorse Web statiche.</span><span class="sxs-lookup"><span data-stu-id="69257-180">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="69257-181">L'app consumer legge il manifesto in fase di esecuzione per usare gli asset dai progetti e dai pacchetti a cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="69257-181">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="69257-182">Quando un nuovo asset viene aggiunto a una RCL, la libreria RCL deve essere ricompilata per aggiornare il manifesto prima che un'app consumer possa accedere al nuovo asset.</span><span class="sxs-lookup"><span data-stu-id="69257-182">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="69257-183">Pubblica</span><span class="sxs-lookup"><span data-stu-id="69257-183">Publish</span></span>

<span data-ttu-id="69257-184">Quando l'app viene pubblicata, le risorse complementari di tutti i progetti e `_content/{LIBRARY NAME}/`i pacchetti di riferimento vengono copiate nella cartella *wwwroot* dell'app pubblicata in .</span><span class="sxs-lookup"><span data-stu-id="69257-184">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="69257-185">In una libreria di classi Razor (RCL) è possibile compilare visualizzazioni Razor, controller, modelli di pagina, [componenti Razor](xref:blazor/class-libraries), [Visualizza componenti](xref:mvc/views/view-components)e modelli di dati.</span><span class="sxs-lookup"><span data-stu-id="69257-185">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="69257-186">La libreria di classi Razor può essere inclusa nel pacchetto e usata nuovamente.</span><span class="sxs-lookup"><span data-stu-id="69257-186">The RCL can be packaged and reused.</span></span> <span data-ttu-id="69257-187">Le applicazioni possono includere la libreria di classi Razor ed eseguire l'override delle visualizzazioni e pagine in essa contenute.</span><span class="sxs-lookup"><span data-stu-id="69257-187">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="69257-188">Quando viene trovata una visualizzazione, visualizzazione parziale o pagina Razor sia nell'app Web che nella libreria di classi Razor, il markup Razor (file con estensione *cshtml*) nell'app Web ha la precedenza.</span><span class="sxs-lookup"><span data-stu-id="69257-188">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="69257-189">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="69257-189">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="69257-190">Creare una libreria di classi contenente l'interfaccia utente Razor</span><span class="sxs-lookup"><span data-stu-id="69257-190">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="69257-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="69257-191">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="69257-192">Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.</span><span class="sxs-lookup"><span data-stu-id="69257-192">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="69257-193">Selezionare **Applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="69257-193">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="69257-194">Assegnare un nome di libreria (ad esempio, "RazorClassLib") > **OK**.</span><span class="sxs-lookup"><span data-stu-id="69257-194">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="69257-195">Per evitare un conflitto di nomi di file con la libreria di visualizzazione generata, verificare che il nome della libreria non finisca per `.Views`.</span><span class="sxs-lookup"><span data-stu-id="69257-195">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="69257-196">Verificare che sia selezionato **ASP.NET Core 2.1** o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="69257-196">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="69257-197">Selezionare **Razor Class Library (Libreria di classi Razor)** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="69257-197">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="69257-198">Un RCL ha il seguente file di progetto:</span><span class="sxs-lookup"><span data-stu-id="69257-198">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[<span data-ttu-id="69257-199">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="69257-199">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="69257-200">Eseguire `dotnet new razorclasslib` dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="69257-200">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="69257-201">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="69257-201">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="69257-202">Per altre informazioni, vedere [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="69257-202">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="69257-203">Per evitare un conflitto di nomi di file con la libreria di visualizzazione generata, verificare che il nome della libreria non finisca per `.Views`.</span><span class="sxs-lookup"><span data-stu-id="69257-203">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="69257-204">Aggiungere i file Razor alla libreria di classi Razor.</span><span class="sxs-lookup"><span data-stu-id="69257-204">Add Razor files to the RCL.</span></span>

<span data-ttu-id="69257-205">I modelli ASP.NET Core presuppongono che il contenuto RCL si trova nella cartella *Aree.*</span><span class="sxs-lookup"><span data-stu-id="69257-205">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="69257-206">Vedere [Layout delle pagine RCL](#rcl-pages-layout) per creare `~/Pages` un `~/Areas/Pages`RCL che espone il contenuto anziché .</span><span class="sxs-lookup"><span data-stu-id="69257-206">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="69257-207">Contenuto RCL di riferimento</span><span class="sxs-lookup"><span data-stu-id="69257-207">Reference RCL content</span></span>

<span data-ttu-id="69257-208">Il riferimento alla libreria di classi Razor può essere eseguito da:</span><span class="sxs-lookup"><span data-stu-id="69257-208">The RCL can be referenced by:</span></span>

* <span data-ttu-id="69257-209">Pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="69257-209">NuGet package.</span></span> <span data-ttu-id="69257-210">Vedere [Creazione di pacchetti NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Creare e pubblicare un pacchetto NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="69257-210">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="69257-211">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="69257-211">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="69257-212">Vedere [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="69257-212">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="69257-213">Procedura dettagliata: Creare un progetto RCL e usare da un progetto Razor PagesWalkthrough: Create an RCL project and use from a Razor Pages project</span><span class="sxs-lookup"><span data-stu-id="69257-213">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="69257-214">È possibile scaricare il [progetto completo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) e testarlo anziché crearlo.</span><span class="sxs-lookup"><span data-stu-id="69257-214">You can download the [complete project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="69257-215">Il download di esempio contiene codice aggiuntivo e collegamenti che rendono più semplice testare il progetto.</span><span class="sxs-lookup"><span data-stu-id="69257-215">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="69257-216">È possibile lasciare commenti e suggerimenti in [questa discussione su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/6098) con i commenti sui download di esempio rispetto alle istruzioni dettagliate.</span><span class="sxs-lookup"><span data-stu-id="69257-216">You can leave feedback in [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="69257-217">Testare l'app di download</span><span class="sxs-lookup"><span data-stu-id="69257-217">Test the download app</span></span>

<span data-ttu-id="69257-218">Se non è stata scaricata l'app completa e si vuole invece creare il progetto della procedura dettagliata, passare alla [prossima sezione](#create-an-rcl).</span><span class="sxs-lookup"><span data-stu-id="69257-218">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="69257-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="69257-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="69257-220">Aprire il file con estensione *sln* in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="69257-220">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="69257-221">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="69257-221">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="69257-222">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="69257-222">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="69257-223">Al prompt dei comandi nella directory *cli*, compilare la libreria di classi Razor e l'app Web.</span><span class="sxs-lookup"><span data-stu-id="69257-223">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="69257-224">Passare alla directory *WebApp1* ed eseguire l'app:</span><span class="sxs-lookup"><span data-stu-id="69257-224">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="69257-225">Seguire le istruzioni indicate in [Testare WebApp1](#test-webapp1)</span><span class="sxs-lookup"><span data-stu-id="69257-225">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="69257-226">Creare un RCLCreate an RCL</span><span class="sxs-lookup"><span data-stu-id="69257-226">Create an RCL</span></span>

<span data-ttu-id="69257-227">In questa sezione viene creato un RCL.</span><span class="sxs-lookup"><span data-stu-id="69257-227">In this section, an RCL is created.</span></span> <span data-ttu-id="69257-228">I file Razor vengono aggiunti alla libreria di classi Razor.</span><span class="sxs-lookup"><span data-stu-id="69257-228">Razor files are added to the RCL.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="69257-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="69257-229">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="69257-230">Creare il progetto di libreria di classi Razor:</span><span class="sxs-lookup"><span data-stu-id="69257-230">Create the RCL project:</span></span>

* <span data-ttu-id="69257-231">Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.</span><span class="sxs-lookup"><span data-stu-id="69257-231">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="69257-232">Selezionare **Applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="69257-232">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="69257-233">Denominare l'app **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="69257-233">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="69257-234">Verificare che sia selezionato **ASP.NET Core 2.1** o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="69257-234">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="69257-235">Selezionare **Razor Class Library (Libreria di classi Razor)** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="69257-235">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="69257-236">Aggiungere un file di visualizzazione parziale Razor denominato *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="69257-236">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="69257-237">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="69257-237">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="69257-238">Eseguire il comando seguente dalla riga di comando:</span><span class="sxs-lookup"><span data-stu-id="69257-238">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="69257-239">I comandi precedenti:</span><span class="sxs-lookup"><span data-stu-id="69257-239">The preceding commands:</span></span>

* <span data-ttu-id="69257-240">Crea `RazorUIClassLib` l'RCL.</span><span class="sxs-lookup"><span data-stu-id="69257-240">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="69257-241">Crea una pagina Razor _Message e la aggiunge alla libreria di classi Razor.</span><span class="sxs-lookup"><span data-stu-id="69257-241">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="69257-242">Il parametro `-np` crea la pagina senza un `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="69257-242">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="69257-243">Crea un file [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) e lo aggiunge all'RCL.</span><span class="sxs-lookup"><span data-stu-id="69257-243">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="69257-244">Il file *_ViewStart.cshtml* è necessario per utilizzare il layout del progetto Razor Pages (che viene aggiunto nella sezione successiva).</span><span class="sxs-lookup"><span data-stu-id="69257-244">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="69257-245">Aggiungere file e cartelle Razor al progetto</span><span class="sxs-lookup"><span data-stu-id="69257-245">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="69257-246">Sostituire il markup *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="69257-246">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="69257-247">Sostituire il markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="69257-247">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="69257-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` è necessario per usare la visualizzazione parziale (`<partial name="_Message" />`).</span><span class="sxs-lookup"><span data-stu-id="69257-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="69257-249">Anziché includere la direttiva `@addTagHelper`, è possibile aggiungere un file *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="69257-249">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="69257-250">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="69257-250">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="69257-251">Per ulteriori informazioni su _ViewImports.cshtml , vedere [Importazione di direttive condiviseFor more](xref:mvc/views/layout#importing-shared-directives) information on *_ViewImports.cshtml*, see Importing Shared Directives</span><span class="sxs-lookup"><span data-stu-id="69257-251">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="69257-252">Compilare la libreria di classi per verificare che non siano presenti errori del compilatore:</span><span class="sxs-lookup"><span data-stu-id="69257-252">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="69257-253">L'output di compilazione contiene *RazorUIClassLib.dll* e *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="69257-253">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="69257-254">*RazorUIClassLib.Views.dll* contiene il contenuto Razor compilato.</span><span class="sxs-lookup"><span data-stu-id="69257-254">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="69257-255">Usare la libreria dell'interfaccia utente Razor da un progetto Razor Pages</span><span class="sxs-lookup"><span data-stu-id="69257-255">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="69257-256">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="69257-256">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="69257-257">Creare l'app Web di Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="69257-257">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="69257-258">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla soluzione > **Aggiungi** >  **Nuovo progetto**.</span><span class="sxs-lookup"><span data-stu-id="69257-258">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="69257-259">Selezionare **Applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="69257-259">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="69257-260">Denominare l'app **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="69257-260">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="69257-261">Verificare che sia selezionato **ASP.NET Core 2.1** o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="69257-261">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="69257-262">Selezionare **Applicazione Web** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="69257-262">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="69257-263">In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **WebApp1** e selezionare **Imposta come progetto di avvio**.</span><span class="sxs-lookup"><span data-stu-id="69257-263">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="69257-264">In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **WebApp1** e selezionare **Dipendenze di compilazione** > **Dipendenze progetto**.</span><span class="sxs-lookup"><span data-stu-id="69257-264">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="69257-265">Selezionare **RazorUIClassLib** come una dipendenza di **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="69257-265">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="69257-266">In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **WebApp1** e scegliere **Aggiungi** > **Riferimento**.</span><span class="sxs-lookup"><span data-stu-id="69257-266">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="69257-267">Nella finestra di dialogo **Gestione riferimenti** selezionare **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="69257-267">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="69257-268">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="69257-268">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="69257-269">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="69257-269">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="69257-270">Creare un'app Web Razor Pages e un file di soluzione contenente l'app Razor Pages e la rcL:</span><span class="sxs-lookup"><span data-stu-id="69257-270">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="69257-271">Compilare ed eseguire l'app Web:</span><span class="sxs-lookup"><span data-stu-id="69257-271">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="69257-272">Testare WebApp1</span><span class="sxs-lookup"><span data-stu-id="69257-272">Test WebApp1</span></span>

<span data-ttu-id="69257-273">Passare `/MyFeature/Page1` a per verificare che la libreria di classi dell'interfaccia utente Razor sia in uso.</span><span class="sxs-lookup"><span data-stu-id="69257-273">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="69257-274">Eseguire l'override di visualizzazioni, visualizzazioni parziali e pagine</span><span class="sxs-lookup"><span data-stu-id="69257-274">Override views, partial views, and pages</span></span>

<span data-ttu-id="69257-275">Quando viene trovata una visualizzazione, visualizzazione parziale o pagina Razor sia nell'app Web che nella libreria di classi Razor, il markup Razor (file con estensione *cshtml*) nell'app Web ha la precedenza.</span><span class="sxs-lookup"><span data-stu-id="69257-275">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="69257-276">Ad esempio, aggiungere *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* a WebApp1 e Page1 in WebApp1 avrà la precedenza su Page1 nell'RCL.</span><span class="sxs-lookup"><span data-stu-id="69257-276">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="69257-277">Nel download di esempio, rinominare *WebApp1/aree/MyFeature2* in *WebApp1/aree/MyFeature* per testare la precedenza.</span><span class="sxs-lookup"><span data-stu-id="69257-277">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="69257-278">Copiare la visualizzazione parziali *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* in *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="69257-278">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="69257-279">Aggiornare il markup per indicare la nuova posizione.</span><span class="sxs-lookup"><span data-stu-id="69257-279">Update the markup to indicate the new location.</span></span> <span data-ttu-id="69257-280">Compilare ed eseguire l'app per verificare quale versione del parziale sia in uso.</span><span class="sxs-lookup"><span data-stu-id="69257-280">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="69257-281">Layout Pagine RCL</span><span class="sxs-lookup"><span data-stu-id="69257-281">RCL Pages layout</span></span>

<span data-ttu-id="69257-282">Per fare riferimento al contenuto RCL come se fa parte della cartella *Pages* dell'app Web, creare il progetto RCL con la struttura di file seguente:</span><span class="sxs-lookup"><span data-stu-id="69257-282">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="69257-283">*RazorUIClassLib/Pagine*</span><span class="sxs-lookup"><span data-stu-id="69257-283">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="69257-284">*RazorUIClassLib/Pagine/Condiviso*</span><span class="sxs-lookup"><span data-stu-id="69257-284">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="69257-285">Si supponga che *RazorUIClassLib/Pages/Shared* contenga due file parziali: *_Header.cshtml* e *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="69257-285">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="69257-286">I `<partial>` tag possono essere aggiunti al file *_Layout.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="69257-286">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="69257-287">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="69257-287">Additional resources</span></span>

* <xref:blazor/class-libraries>
